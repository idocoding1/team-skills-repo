# Examples

Before:

```sql
select * from orders a, customers b where a.customer_id = b.id and date(a.created_at) = '2026-01-01'
```

After:

```sql
SELECT
    o.id,
    o.total,
    c.email
FROM orders o
JOIN customers c ON c.id = o.customer_id
WHERE o.created_at >= :day_start
  AND o.created_at < :day_end;
```
