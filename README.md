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
