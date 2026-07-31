# DOCS

## Database

### Truncate tables

```sql
TRUNCATE TABLE orders RESTART IDENTITY CASCADE;
```


### Monitor Redis

```sh
docker exec -it aa-redis redis-cli SUBSCRIBE MessageCreated
```