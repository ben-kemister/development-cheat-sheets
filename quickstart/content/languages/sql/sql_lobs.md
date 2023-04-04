---
title: LOBs
tags:
 - database
 - sql
 - oracle
 - clob
 - blob
---

This page contains information and simple code examples about dealing with LOBs (CLOBs and BLOBs) in SQL statements.

## CLOB/BLOB length

To find out the length of a LOB object you can use the `DBMS_LOB.getLength` function:

```sql
Select
    up.*, DBMS_LOB.getLength(up.stage_layout) as "BLOB_LENGTH"
    from ui_properties up
    where up.stage_layout is not null
    order by "BLOB_LENGTH" desc;
```


## Converting BLOB to CLOB - No truncation

Unfortunately there is no built in function that will do this conversion that does not have a length restriction on it. 
A workaround to this is to create a function which you can then use in your SQL statements:

### Function

```sql
create function clobfromblob(p_blob blob) return clob is
      l_clob         clob;
      l_dest_offsset integer := 1;
      l_src_offsset  integer := 1;
      l_lang_context integer := dbms_lob.default_lang_ctx;
      l_warning      integer;

   begin

      if p_blob is null then
         return null;
      end if;

      dbms_lob.createTemporary(lob_loc => l_clob
                              ,cache   => false);

      dbms_lob.converttoclob(dest_lob     => l_clob
                            ,src_blob     => p_blob
                            ,amount       => dbms_lob.lobmaxsize
                            ,dest_offset  => l_dest_offsset
                            ,src_offset   => l_src_offsset
                            ,blob_csid    => dbms_lob.default_csid
                            ,lang_context => l_lang_context
                            ,warning      => l_warning);

      return l_clob;

   end;
```

### Function use

```sql
select up.*, clobfromblob(up.stage_layout), DBMS_LOB.getLength(up.stage_layout) as "BLOB_LENGTH"
    from ui_properties cup
    where up.stage_layout is not null;
```
