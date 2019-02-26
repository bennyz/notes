dmsetup
=======
  dmsetup ls | grep my_vg | cut -f1 | xargs dmsetup remove
