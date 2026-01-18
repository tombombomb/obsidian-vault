# PostgreSQL Docker Container Setup

## Overview
Created a Docker Compose environment with PostgreSQL database, REST API, and web-based management interface.

## Docker Compose Configuration

**File Location:** `/home/tom/docker-compose.yml`

### Services Included

1. **PostgreSQL Database** (`postgres:16-alpine`)
   - Container name: `postgres_db`
   - Port: `5432`
   - Persistent storage via Docker volume

2. **PostgREST API** (`postgrest/postgrest:latest`)
   - Container name: `postgres_api`
   - Port: `3000`
   - Provides automatic REST API for database tables

3. **pgAdmin** (`dpage/pgadmin4:latest`)
   - Container name: `pgadmin`
   - Port: `5050`
   - Web-based database management interface

## Connection Details

### PostgreSQL Database
- **Host:** `localhost`
- **Port:** `5432`
- **Database:** `mydb`
- **Username:** `admin`
- **Password:** `admin123`

### pgAdmin Web Interface
- **URL:** http://localhost:5050
- **Email:** `admin@admin.com`
- **Password:** `admin123`

### REST API
- **URL:** http://localhost:3000
- **Documentation:** Automatic OpenAPI docs available

## Docker Commands

### Start Containers
```bash
cd /home/tom
docker-compose up -d
```

### Stop Containers
```bash
docker-compose down
```

### Restart Containers
```bash
docker-compose restart
```

### View Logs
```bash
docker-compose logs
docker-compose logs postgres_db
docker-compose logs postgres_api
```

### Check Container Status
```bash
docker ps
```

## Sample Database Created

### Products Table Schema
```sql
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    description TEXT,
    price DECIMAL(10, 2),
    category VARCHAR(50),
    in_stock BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Sample Data (8 Products)
- Laptop ($1299.99) - Electronics
- Wireless Mouse ($29.99) - Electronics
- Office Chair ($249.99) - Furniture
- Coffee Maker ($89.99) - Appliances
- Desk Lamp ($45.50) - Furniture
- Notebook Set ($12.99) - Stationery
- Bluetooth Speaker ($79.99) - Electronics
- Water Bottle ($24.99) - Accessories

## How to Interact with the Database

### 1. Using pgAdmin (Web Interface)
1. Navigate to http://localhost:5050
2. Login with credentials above
3. Register new server:
   - **Name:** My Database (or any name)
   - **Host:** `postgres` (or `host.docker.internal`)
   - **Port:** `5432`
   - **Database:** `mydb`
   - **Username:** `admin`
   - **Password:** `admin123`

### 2. Using REST API (PostgREST)

**Get all products:**
```bash
curl http://localhost:3000/products
```

**Get specific product:**
```bash
curl http://localhost:3000/products?id=eq.1
```

**Filter by category:**
```bash
curl http://localhost:3000/products?category=eq.Electronics
```

**Filter by price (under $50):**
```bash
curl http://localhost:3000/products?price=lt.50
```

**Get in-stock items:**
```bash
curl http://localhost:3000/products?in_stock=eq.true
```

**Add new product:**
```bash
curl -X POST http://localhost:3000/products \
  -H "Content-Type: application/json" \
  -d '{
    "name": "USB Cable",
    "description": "USB-C to USB-A cable 6ft",
    "price": 9.99,
    "category": "Electronics",
    "in_stock": true
  }'
```

**Update product:**
```bash
curl -X PATCH http://localhost:3000/products?id=eq.5 \
  -H "Content-Type: application/json" \
  -d '{"in_stock": true}'
```

**Delete product:**
```bash
curl -X DELETE http://localhost:3000/products?id=eq.8
```

### 3. Using Command Line (psql)

**Connect to database:**
```bash
docker exec postgres_db psql -U admin -d mydb
```

**Common SQL commands:**
```sql
-- List all tables
\dt

-- View table structure
\d products

-- Query data
SELECT * FROM products;
SELECT * FROM products WHERE price < 100;
SELECT category, COUNT(*) FROM products GROUP BY category;

-- Insert data
INSERT INTO products (name, description, price, category, in_stock)
VALUES ('New Product', 'Description here', 99.99, 'Category', true);

-- Update data
UPDATE products SET in_stock = false WHERE id = 5;

-- Delete data
DELETE FROM products WHERE id = 8;

-- Exit psql
\q
```

### 4. Using Database Clients

Compatible with any PostgreSQL client:
- **DBeaver** (free, cross-platform)
- **TablePlus**
- **DataGrip**
- **VS Code Extensions** (PostgreSQL, SQLTools)

Use the PostgreSQL connection details listed above.

## PostgREST Query Operators

| Operator | Meaning | Example |
|----------|---------|---------|
| `eq` | Equals | `?price=eq.29.99` |
| `lt` | Less than | `?price=lt.50` |
| `gt` | Greater than | `?price=gt.100` |
| `lte` | Less than or equal | `?price=lte.50` |
| `gte` | Greater than or equal | `?price=gte.100` |
| `neq` | Not equal | `?category=neq.Electronics` |
| `like` | Pattern match | `?name=like.*Mouse*` |
| `in` | In list | `?category=in.(Electronics,Furniture)` |

## Managing Docker Desktop

### Viewing Containers
1. Open Docker Desktop application
2. Go to "Containers" tab
3. See all three containers: `postgres_db`, `postgres_api`, `pgadmin`

### Starting/Stopping via GUI
- Click on container name to view details
- Use start/stop/restart buttons
- View real-time logs

## Important Notes

⚠️ **Security Warning:** The default passwords are for development only. Change them before using in production.

⚠️ **Data Persistence:** Database data is stored in Docker volume `tom_postgres_data`. Data persists even when containers are stopped.

⚠️ **Schema Cache:** After creating new tables, restart the PostgREST container:
```bash
docker restart postgres_api
```

## Troubleshooting

### Containers won't start
```bash
docker-compose down
docker-compose up -d
```

### REST API returns schema error
```bash
docker restart postgres_api
```

### Can't connect to database
1. Verify containers are running: `docker ps`
2. Check Docker Desktop is running
3. Verify ports aren't in use by other applications

### View container logs
```bash
docker logs postgres_db
docker logs postgres_api
docker logs pgadmin
```

## Next Steps

- Create additional tables for your application
- Explore pgAdmin's query tool and visualization features
- Integrate the REST API with your application
- Set up database backups
- Configure proper authentication for production use

---

**Created:** 2026-01-09
**Location:** /home/tom/
**Files:** docker-compose.yml, PostgreSQL-Docker-Setup.md
