Multi Store Order Management System

Assessment submission - order management with real time updates.

Stack
Backend: Node + Express + MongoDB (mongoose) + Socket.IO
Frontend: Next.js (app router) + React Query + Zustand + Tailwind
How to run

You need MongoDB running locally (or point MONGO_URI at Atlas).

Backend

cd backend
cp .env.example .env
npm install
npm run dev

runs on http://localhost:5000

Frontend

cd frontend
cp .env.local.example .env.local
npm install
npm run dev

runs on http://localhost:3000

API
Orders
POST /api/orders - create order, body: { store_id, items: [{item_id, name, qty, price}] }
GET /api/orders?store_id=&page=&limit=&status= - paginated list for a store
GET /api/orders/:id
PATCH /api/orders/:id/status - body: { status }, only allows PLACED -> PREPARING -> COMPLETED
DELETE /api/orders/:id - cancel, only works if still PLACED (extra feature, not in the original spec but felt useful)
Analytics
GET /api/analytics/orders-per-day?store_id=&days=30
GET /api/analytics/revenue-per-store
GET /api/analytics/top-items?store_id=&limit=5
Archival
POST /api/archive-old-orders - moves orders older than 30 days to orders_archive, does it in batches of 500
Sockets

Client emits subscribe:store with a store_id when it wants updates for that store.

Server emits order:created / order:status_updated / order:cancelled to that store's room.

Socket.IO handles reconnects on its own, the app just re-subscribes to the current store on mount.

Notes / assumptions
total_amount gets recalculated on the server from items, doesn't trust whatever the client sends
revenue-per-store only counts COMPLETED orders since the others aren't final yet
compound index on (store_id, created_at) since basically every query filters by store and sorts by date
store list on the frontend is hardcoded for now (store_1/2/3), a real version would fetch this from an API
added a basic search box on the orders page and a cancel button as small extras beyond the spec
no auth, no tests - didn't have those in scope for a 3 day take home
What I'd do with more time
cursor based pagination instead of skip/limit once volume actually gets big
proper transactions on the archive job so a crash mid batch can't leave things half-moved
a stores collection instead of hardcoding 3 stores in the frontend
