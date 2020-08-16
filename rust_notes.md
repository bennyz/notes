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
