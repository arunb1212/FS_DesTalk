🚗 Student Commute Optimizer
📌 Overview

The Student Commute Optimizer is a full-stack project designed to connect students who share similar routes and timings. It enables students to create trips, find overlapping commutes, and coordinate ride-sharing in a safe and anonymous way.

This project demonstrates system design, architectural thinking, and trade-offs in building scalable web applications.

🎯 Problem Statement

Many students travel daily from similar origins to destinations, but lack an easy way to coordinate.
This leads to:

Higher travel costs

Longer commute times

Missed opportunities for sustainable travel

Goal: Build a platform where students can post trips, find matches with overlapping routes, chat anonymously, and confirm rides.

🏗 System Design
High-Level Architecture
[Frontend: React / React Native]
        |
        v
[Backend API: Node.js / NestJS]
   ├── Auth Service
   ├── Trip Service
   ├── Matching Engine
   ├── Chat Service (WebSockets)
   └── Notifications
        |
   -------------------------------
   |             |              |
[Postgres+PostGIS]   [Redis]   [Routing Engine (OSRM/Mapbox)]
       |                |                |
   [Trips/Users/     [Cache +        [Route & detour
   Matches/Chats]     Pub/Sub]        calculations]

Key Components

Frontend: React (Web) / React Native (Mobile)

Backend: Node.js (NestJS/Express)

Database: PostgreSQL with PostGIS (geospatial queries)

Cache & Queue: Redis + BullMQ

Routing Engine: OSRM (self-hosted) or Mapbox Directions API

Real-time Chat: Socket.io (WebSockets)

Storage: S3/MinIO for static assets

🗄 Data Model

Users

id, username (anonymous), email, password hash, created_at

Trips

id, user_id, origin (point), destination (point), route (polyline), start_time, seats

Matches

trip_a, trip_b, overlap_score, detour_estimate, status

Messages

id, match_id, sender_id, body, created_at

🔑 Core Features (MVP)

Student sign-up & login with anonymous display names

Trip creation with origin, destination, time

Geospatial matching based on route overlap and time compatibility

Anonymous in-app chat between matched students

Accept/reject match suggestions

📐 Algorithm (Pseudo-code)
function find_matches(new_trip):
    new_route = compute_route(new_trip.origin, new_trip.destination)
    candidates = SELECT trips
                 WHERE ST_DWithin(trips.route, new_route, 1000m)
                   AND abs(trips.start_time - new_trip.start_time) < 30min

    scored = []
    for c in candidates:
        inter_len = ST_Length(ST_Intersection(c.route, new_route))
        pct_overlap = inter_len / ST_Length(new_route)
        detour = estimate_detour(new_route, c.route)
        score = 0.6 * pct_overlap - 0.2 * detour - 0.2 * time_diff
        if pct_overlap > 0.2 and detour < 15min:
            scored.append({trip: c, score: score})

    return top 5 by score

⚖️ Design Trade-offs

Database:

Chosen: Postgres + PostGIS (powerful geospatial queries).

Alternative: MongoDB with GeoJSON (simpler, but weaker for route overlap).

Routing:

Chosen: OSRM (open-source, free).

Alternative: Google Maps API (easier but costly at scale).

Chat:

Chosen: Socket.io (real-time + anonymous).

Alternative: Firebase Realtime DB (managed, but vendor lock-in).

Hosting:

Chosen: Vercel (frontend), Render/Heroku (backend).

Alternative: AWS/GCP (scalable, but more setup overhead).

🔐 Privacy & Security Considerations

Store anonymous display names, not real identities by default.

Exact pickup points should be obfuscated (e.g., round coordinates to 50m).

Secure communication with HTTPS + JWT.

Chat messages stored with match_id, not personal identifiers.

🚀 Tech Stack

Frontend: React / React Native

Backend: Node.js (NestJS/Express)

Database: PostgreSQL + PostGIS

Cache/Queue: Redis + BullMQ

Routing: OSRM / Mapbox Directions

Chat: Socket.io

Deployment: Vercel + Render/Heroku

🛠 How to Run (Placeholder for future code)
# Backend
cd backend
npm install
npm run dev

# Frontend
cd frontend
npm install
npm start

🌟 Future Enhancements

Mobile-first PWA

Push notifications for new matches

ML-based smart ranking for better matches

Payment integration (split fuel cost)

Gamification (green commute badges)