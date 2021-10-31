# vserv-minio

A minio installation for a single netcup storage server

## Manual Steps

Create Service Accounts (ACCESS_KEY_ID & ACCESS_KEY_PASSWORD) at:
https://dashboard-minio.storage1500.vserv.fun/account

## Backup Strategy

Source: https://docs.min.io/minio/baremetal/replication/replication-overview.html#

Option 1: Minio Server to (Minio S3 Gateway => AWS S3) Replication

- Add Minio S3 Gateway as a service in minio docker-compose:
  https://docs.min.io/minio/baremetal/reference/minio-server/minio-gateway.html
- Add Replication on minio server to this minio S3 gateway
  https://docs.min.io/minio/baremetal/replication/replication-overview.html#

=> Problem: same number of GET / POST Requests on AWS as on my MINIO Server => increases COST
=> Problem: more complicated, since this gateway setup is needed
=> Problem: needs versioning on the Bucket & Versioning is only possible in replicated setups
=> Good: immediate mirror

Option 2: Client-Side Bucket Replication with `mc mirror`
=> Good: Can replicate to AWS S3 directly :)
=> Good: Reduces number of GET/POST Requests to O(n) (only when running incremental!)
=> Problem: mirror must make many more GET requsts for first run, if it doesn't work incrementally
=> Good: mc mirror has a 'watch mode'
=> https://docs.min.io/minio/baremetal/reference/minio-cli/minio-mc/mc-mirror.html#continuously-mirror-s3-bucket-to-an-s3-compatible-host
