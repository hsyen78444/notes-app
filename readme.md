# 📝 Full Stack Cloud Notes App

A modern, decoupled web application for managing notes. Built with **Vue.js 3** on the frontend and **Spring Boot 3** on the backend, fully containerized with **Docker** and deployed to the cloud.





## 🚀 Live Demo

* **Frontend (Vercel):** https://notes-app-git-main-hsyen78444s-projects.vercel.app/
* **Backend API (Render):** https://notes-app-71cr.onrender.com/api/notes

---

## 🛠 Tech Stack

### **Frontend**
* **Framework:** Vue.js 3
* **Build Tool:** Vite
* **Styling:** Tailwind CSS
* **HTTP Client:** Native Fetch API
* **Deployment:** Vercel

### **Backend**
* **Framework:** Spring Boot 3
* **Language:** Java 21 (JDK 21)
* **Database:** MySQL (Cloud Hosted via TiDB)
* **ORM:** Spring Data JPA (Hibernate)
* **Containerization:** Docker
* **Deployment:** Render.com

---

## ✨ Features

* **CRUD Operations:** Create, Read, Update, and Delete notes seamlessly.
* **Cloud Persistence:** Data is stored securely in a managed MySQL database.
* **Responsive Design:** Fully optimized for mobile and desktop screens.
* **Environment Aware:** Automatically detects Local vs. Production environments.
* **Decoupled Architecture:** Frontend and Backend are completely separate services communicating via REST API.

> **Note:** This is currently designed as a "Community Board." All visitors share the same database view and can see each other's notes.

---

## 🏗 Architecture

The application follows a standard **Client-Server** architecture:

1.  **Browser** loads the Vue.js app from the Vercel CDN.
2.  **Vue.js** makes HTTP requests to the Render Backend (Spring Boot).
3.  **Spring Boot** processes logic and queries the MySQL Database.
4.  **MySQL** returns data, which travels back up the chain to the UI.

---

## ⚙️ Local Setup Guide

If you want to run this project on your local machine:

### 1. Backend Setup
**Prerequisites:** Java 21, Maven.

1.  Navigate to the backend folder.
2.  Configure your database in `src/main/resources/application.properties`:
    ```properties
    spring.datasource.url=jdbc:mysql://localhost:3306/your_db_name
    spring.datasource.username=root
    spring.datasource.password=your_password
    ```
3.  Run the application:
    ```bash
    mvn spring-boot:run
    ```
    *Server starts at `http://localhost:8080`*

### 2. Frontend Setup
**Prerequisites:** Node.js.

1.  Navigate to the frontend folder.
2.  Install dependencies:
    ```bash
    npm install
    ```
3.  Run the development server:
    ```bash
    npm run dev
    ```
    *App starts at `http://localhost:5173`*

---

## 🔌 API Endpoints

The backend exposes the following REST endpoints:

| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `GET` | `/api/notes` | Fetch all notes |
| `POST` | `/api/notes` | Create a new note |
| `PUT` | `/api/notes/{id}` | Update an existing note |
| `DELETE` | `/api/notes/{id}` | Delete a note |

---

## 🔮 Future Improvements

* [ ] **User Authentication:** Add Spring Security + JWT to allow private user accounts.
* [ ] **Search:** Add ability to filter notes by title/content.
* [ ] **Categories:** Add tags or folders for organization.

---

### Author
Built by **HS YEN**.
