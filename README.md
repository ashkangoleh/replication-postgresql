# REPLICATION PSQL DOCKER


1. create user on Master
`CREATE USER replicator WITH REPLICATION ENCRYPTED PASSWORD 'my_replicator_password';`

2. config pg_hba
`host replication replicator <slave_ip_address>/32 md5`
3. Modify parameters that require modification on the master(***optional***)
- `ALTER SYSTEM SET listen_addresses TO '*'`
- `ALTER SYSTEM SET archive_mode TO 'ON'`
- `ALTER SYSTEM SET archive_command TO 'cp %p /archives/%f'`
- `ALTER SYSTEM SET restore_command TO 'cp /archives/%f %p'`
- `ALTER SYSTEM SET wal_keep_segments TO '100'`
4. Run pg_basebackup from the standby to take a backup of the master
- `$ pg_basebackup -h <master_ip> -U replicator -p 5432 -D <temp file> -Fp -Xs -P -R`
5. copy temp file to slave and replace with current data folder
6. restart master and slaves docker-compose
