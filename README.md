# Quick Bites — Food Delivery API
## CS 432 Databases — Assignment 4: Sharding

### Group: P_and_team

### Tech Stack
- Python / Flask
- MySQL (3 sharded servers)
- B+ Tree indexing
- WAL for crash recovery

### Sharding Setup
- Shard Key: item_id
- Strategy: Hash-based (item_id % 3)
- Shard 0: port 3307
- Shard 1: port 3308
- Shard 2: port 3309

### How to Run
1. Install dependencies:
   pip install -r requirements.txt

2. Run migration (first time only):
   python migrate.py

3. Start the server:
   python app.py

### API Endpoints
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | /api/menu | Get all items (all shards) |
| GET | /api/menu/<item_id> | Get single item (routed to correct shard) |
| POST | /api/menu | Add item (routed to correct shard) |
| PUT | /api/menu/<item_id> | Update item (routed to correct shard) |
| DELETE | /api/menu/<item_id> | Delete item (routed to correct shard) |
| GET | /api/menu/range?start=X&end=Y | Range query (all shards merged) |


## Sharding Architecture

- **Sharding Approach:** 3 separate MySQL server instances (Docker containers) 
  provided by course instructor at host 10.0.116.184
- **Shard Isolation:** Each shard is a completely separate MySQL server on a 
  different port — data on one shard is physically unreachable from another
- **Routing Logic:** shard_router.py computes item_id % 3 at request time — 
  O(1) with no external lookup needed

## Data Migration

- Original dataset: 19 menu items in local menuitem table
- Migrated using migrate.py with formula item_id % 3
- Shard 0 (port 3307): 7 items — item_ids where item_id % 3 == 0
- Shard 1 (port 3308): 5 items — item_ids where item_id % 3 == 1  
- Shard 2 (port 3309): 7 items — item_ids where item_id % 3 == 2
- Verified: 0 duplicates, 0 data loss

## Scalability Trade-offs

- **Horizontal Scaling:** Data split across 3 independent servers instead of 
  upgrading one large server
- **Consistency:** Each shard is independent — no cross-shard sync needed since 
  no item exists on more than one shard
- **Availability:** If one shard goes down, other 2 shards continue serving normally
- **Partition Tolerance:** Hash routing detects unreachable shards and returns 
  partial responses from remaining shards

