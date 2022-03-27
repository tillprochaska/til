# PostgreSQL

## Convert binary dump to SQL

`pg_restore`gibt SQL aus, wenn es ohne eine Datenbankverbindung ausgeführt wird:


```
pg_restore -f my-sql-dump.sql my-binary-dump
```

Darüber hinaus können alle Optionen von `pg_restore` normal verwendet werden, z.B. um nur eine einzelne Tabelle zu exportieren:

```
pg_restore -f users.sql my-binary-dump
```
