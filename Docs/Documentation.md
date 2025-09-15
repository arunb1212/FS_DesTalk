# Student Commute Optimizer 🚗🎒

A full-stack carpooling and route-sharing application designed to help students find travel matches efficiently and safely. This project is an implementation ideation based on a problem statement from AIGSpace.

## 📖 Overview

Instead of each student commuting individually, this app matches students traveling along similar routes so they can share rides. The core principles are **Privacy**, **Simplicity**, and **Efficiency**.

- **Frontend:** A simple map-based interface built with React where students enter locations, view matches, and chat anonymously.
- **Backend:** A Node.js API that handles spatial matching algorithms, user authentication, and real-time chat functionality.
- **Database:** PostgreSQL with PostGIS for performing efficient geographic queries to find nearby routes.

## 🚀 Features

-   **Anonymous Matching:** Users are identified only by unique, non-duplicatable usernames.
-   **Spatial Algorithm:** Efficient two-step algorithm to find students with overlapping commute paths.
-   **In-App Chat:** Secure messaging system to coordinate logistics without sharing personal contact info.
-   **Secure by Design:** JWT authentication, password hashing, and prepared statements to prevent SQL injection.

## 🏗️ System Design

For a detailed breakdown of the architecture, data models, algorithms, and trade-offs, please see the full **[System Design Document](./docs/system-design.md)**.

![High-Level Architecture](./docs/architecture.png)
*Figure: High-Level 3-Tier Application Architecture*

## 🛠️ Tech Stack

| Layer               | Technology                          |
| ------------------- | ----------------------------------- |
| **Frontend**        | React, Leaflet/MapLibre, Socket.io-client |
| **Backend**         | Node.js, Express.js, Socket.io      |
| **Database**        | PostgreSQL (with PostGIS Extension) |
| **Cache**           | Redis                               |
| **Authentication**  | JWT (JSON Web Tokens)               |
| **Spatial Queries** | PostGIS Functions                   |

## 📦 Installation & Setup

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/<your-username>/student-commute-optimizer.git
    cd student-commute-optimizer
    ```

2.  **Backend Setup:**
    ```bash
    cd backend
    npm install
    # Create a .env file based on .env.example and set your variables (DB_URL, JWT_SECRET, etc.)
    npm run dev
    ```

3.  **Frontend Setup:**
    ```bash
    cd ../frontend
    npm install
    npm start
    ```

4.  **Database Setup:** Ensure PostgreSQL and PostGIS are installed and running. Run the provided SQL scripts to create tables and spatial indexes.

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Problem statement provided by **[AIGSpace](https://www.aigspace.com)**.
- Icons provided by [FlatIcon](https://www.flaticon.com).