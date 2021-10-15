# diesel

To use a struct Jsonb, I can do the following:
```rust
#[derive(FromSqlRow, Serialize, Deserialize, Debug, AsExpression, Clone, QueryableByName)]
#[sql_type = "Jsonb"]
pub struct StorageConfig {
    #[sql_type = "Uuid"]
    host_id: Option<Uuid>,
    #[sql_type = "Uuid"]
    path: Option<String>,
    #[sql_type = "Varchar"]
    pool_name: Option<String>,
}

impl FromSql<Jsonb, Pg> for StorageConfig {
    fn from_sql(bytes: Option<&[u8]>) -> diesel::deserialize::Result<Self> {
        let value = <serde_json::Value as FromSql<Jsonb, Pg>>::from_sql(bytes)?;
        Ok(serde_json::from_value(value)?)
    }
}

impl ToSql<Jsonb, Pg> for StorageConfig {
    fn to_sql<W: Write>(&self, out: &mut Output<W, Pg>) -> diesel::serialize::Result {
        let value = serde_json::to_value(self)?;
        <serde_json::Value as ToSql<Jsonb, Pg>>::to_sql(&value, out)
    }
}
```

Feature `serde_json` has to be enabled:
```rust
diesel = { version = "1.4", features = ["postgres", "r2d2", "uuid", "serde_json"] }
```
# Typed APIs
From Will Crichton's excellent talk https://www.youtube.com/watch?v=bnnacleqg6k

TODO: go over it in more detail and write notes
```rust
use std::{time::Duration, thread::sleep};

const CLEAR: &str = "\x1B[2J\x1B[1;1H";

struct Unbounded;
struct Bounded {
    bound: usize,
    delims: (char, char),
}

struct Progress<Iter, Bound> {
    iter: Iter,
    i: usize,
    bound: Bound,
}

trait ProgressDisplay: Sized {
   fn display<Iter>(&self, progress: &Progress<Iter, Self>);
}

impl ProgressDisplay for Unbounded {
   fn display<Iter>(&self, progress: &Progress<Iter, Self>) {
       println!("{}", "*".repeat(progress.i));
   }
}

impl ProgressDisplay for Bounded {
   fn display<Iter>(&self, progress: &Progress<Iter, Self>) {
      println!("{}{}{}{}",
               self.delims.0,
               "*".repeat(progress.i), // self is Bounded
               " ".repeat(self.bound - progress.i),
               self.delims.1);
   }
}

impl<Iter> Progress<Iter, Unbounded> {
    pub fn new(iter: Iter) -> Self {
        Progress { iter, i: 0, bound: Unbounded }
    }
}

impl<Iter> Progress<Iter, Unbounded>
where Iter: ExactSizeIterator {
    pub fn with_bound(self) -> Progress<Iter, Bounded> {
        let bound = Bounded {
            bound: self.iter.len(),
            delims: ('[', ']'),
        };

        Progress { i: self.i, iter: self.iter, bound }
    }
}

impl<Iter> Progress<Iter, Bounded> {
    pub fn with_delims(mut self, delims: (char, char)) -> Self {
        self.bound.delims = delims;
        self
    }
}

impl<Iter, Bound> Iterator for Progress<Iter, Bound>
where Iter: Iterator, Bound: ProgressDisplay {
    type Item = Iter::Item;

    fn next(&mut self) -> Option<Self::Item> {
        println!("{}", CLEAR);
        self.bound.display(&self);
        self.i += 1;
        self.iter.next()
    }
}

trait ProgressIteratorExt: Sized {
    fn progress(self) -> Progress<Self, Unbounded>;
}

impl<Iter> ProgressIteratorExt for Iter {
    fn progress(self) -> Progress<Self, Unbounded> {
     Progress::new(self)
    }
}

fn expensive_calc(_n: &i32) {
    sleep(Duration::from_secs(1))
}

fn main() {
    let v = vec![1, 2, 3];
    for n in v.iter().progress().with_bound() {
        expensive_calc(n);
    }
}
```
