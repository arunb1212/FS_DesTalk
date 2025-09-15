Route Matching Algorithm
Goal: Find other active routes whose path is within a proximity threshold (e.g., 1 km) of the current user's route.

Trade-off: Perfect accuracy (calculating distance to every route) is O(n) and computationally expensive. We use a two-step process to minimize expensive calculations.

Steps:

Broad Filter (Fast): Use a bounding box around the user's route to quickly find candidate routes using a spatially indexed query.

Precise Filter (Accurate): Calculate the precise distance from the user's route to each candidate route.

// Pseudo-Code for findMatches(userRoute)
function findMatches(userRoute) {

    // 1. CREATE A SEARCH AREA (Bounding Box with buffer)
    const searchArea = createBoundingBox(userRoute.path, bufferDistance = 2000); // 2km buffer

    // 2. BROAD FILTER: Find candidate routes whose path intersects the bounding box
    const candidateRoutes = db.query(`
        SELECT * FROM routes
        WHERE id != :currentRouteId
        AND is_active = TRUE
        AND ST_Intersects(path, ST_MakeEnvelope(:searchArea))
    `);

    // 3. PRECISE FILTER: Calculate exact distance for each candidate
    const matches = [];
    for (const candidate of candidateRoutes) {
        // Use PostGIS function to get minimum distance between two LineStrings
        const distance = db.query(`
            SELECT ST_Distance(:userPath, :candidatePath) as distance
        `);

        if (distance < PROXIMITY_THRESHOLD) { // e.g., 1000 meters
            matches.push({ route: candidate, distance: distance });
        }
    }

    // 4. SORT and RETURN
    return matches.sort((a, b) => a.distance - b.distance);
}




REST API Endpoints
Method	Endpoint	Description	Request Body
POST	/api/auth/register	Register a new user	{ username, email, password, homeLocation, schoolLocation }
POST	/api/auth/login	Authenticate a user	{ email, password }
POST	/api/routes	Create a new active route	{ startPoint, endPoint, departureTime }
GET	/api/routes/matches	Get matches for active route	-
DELETE	/api/routes/:id	End trip / deactivate route	-
GET	/api/chat/threads	Get user's chat threads	-
GET	/api/chat/threads/:id	Get messages in a thread	-
WebSocket Events
join: Client joins their own room (e.g., userId) for receiving messages.

send_message: { threadId, message } → Server relays it to the other user in the thread.

receive_message: { threadId, senderUsername, message, timestamp } → Sent to the recipient.

5. Trade-offs & Considerations
Spatial Indexing:

Choice: Using a GiST index on the path (LineString) column in PostgreSQL.

Trade-off: Adds overhead on INSERT/UPDATE but makes spatial queries (ST_Intersects, ST_Distance) orders of magnitude faster. Essential for performance.

Matching Algorithm Efficiency:

Choice: Two-step filtering process.

Trade-off: The buffer distance is a tunable parameter. A larger value makes the broad filter less effective but decreases the chance of missing a potential match. This requires empirical testing with real data.

Anonymity vs. Accountability:

Choice: Only usernames are shared. No personal info is exposed in the UI.

Trade-off: Makes building trust more difficult. A future rating system would be necessary for a production system without compromising anonymity.

Real-time vs. REST for Chat:

Choice: WebSockets (via Socket.io) for instant messaging.

Trade-off: Adds complexity to the backend (stateful connections) but provides a vastly superior user experience compared to HTTP polling.



---

### **4. `backend/package.json`**

```json
{
  "name": "student-commute-backend",
  "version": "1.0.0",
  "description": "Backend API and real-time server for the Student Commute Optimizer",
  "main": "src/server.js",
  "scripts": {
    "dev": "nodemon src/server.js",
    "start": "node src/server.js"
  },
  "dependencies": {
    "express": "^4.18.2",
    "socket.io": "^4.7.2",
    "pg": "^8.11.3",
    "postgis": "^0.4.0",
    "bcryptjs": "^2.4.3",
    "jsonwebtoken": "^9.0.2",
    "cors": "^2.8.5",
    "dotenv": "^16.3.1"
  },
  "devDependencies": {
    "nodemon": "^3.0.1"
  }
}