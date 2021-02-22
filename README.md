# Workerbee

```bash
NODE_OPTIONS=--max-old-space-size=32768 ts-node src/cli.ts
```

## Setup

```
docker-compose up -d
```

## Add Worker

```bash
# in master node
docker-compose exec db mongo -u vespa -p
```

```js
use vespa

db.createUser({
  user: "worker-tokyo1",
  pwd: passwordPrompt(), // or cleartext password
  roles: [{ role: "readWrite", db: "vespa" }],
  authenticationRestrictions: [
    {
      clientSource: ["<ip|cidr>"]
    }
  ]
});
```

```bash
# in worklet
cat secret | docker login pkg.uechi.io --username <user> --password-stdin
docker run \
  -e JOB_CONCURRENCY=100 \
  -e MONGO_URI=mongodb://<user>:<pwd>@<host>/<db> \
  -e REDIS_URI=redis://:<password>@<host> \
  pkg.uechi.io/vespa-honeybee
```

## Remove worker

```js
use vespa
show users
db.dropUser("worker-singapore1")
```
