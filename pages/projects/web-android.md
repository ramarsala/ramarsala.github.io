---
layout: page
title: Web & Android Programming
subtitle: Full-stack web applications, statistics tracking platforms, and native mobile Android development
---

This page showcases projects I have built in web development and mobile application programming. While much of my background is rooted in low-level systems and computer architecture, I also enjoy building modern, responsive, and data-driven user interfaces. These projects reflect my experience with full-stack web architectures, native mobile platforms, and containerized deployment.

---

## Personal Statistics Tracking & Analytics Platform (Vite, TypeScript, Docker)

To track personal performance and analytical metrics across gaming sessions (such as rhythm game scores and skill ratings), I created a customized statistics tracking website built with agentic workflows, Vite, TypeScript, and modern component frameworks, self-hosted on a local Linux server.

Key features and architectural highlights include:
- **Interactive Data Visualization:** Implemented responsive charts and visual dashboards—including scatter plots, performance progression curves, and score distribution histograms—allowing deep analysis of scoring trends, timing accuracy, and rating milestones over time.
- **Strict Type Safety & Component Design:** Developed using TypeScript and React/Next.js component structures to maintain modular state management, robust data contracts, and predictable component lifecycles.
- **RESTful Data Ingestion & API Integration:** Built endpoints and client service layers to ingest, parse, and synchronize gameplay score records, with validation pipelines to prevent malformed or duplicate submissions.
- **Containerized Self-Hosting:** Packaged the entire web application and database environment using Docker containers running on a dedicated local server. This ensures reproducible builds, easy environment management, automated backups, and 24/7 availability on the local network.

This project combined modern web frontend engineering with practical server administration, delivering a fast, responsive, and personally valuable analytics tool.

---

## Native Android Application Development (Kotlin & Java, Android Jetpack)

Through my mobile programming coursework and personal projects, I developed native Android applications emphasizing clean architectural patterns, responsive UI design, and robust lifecycle handling.

The applications were built using Kotlin and Java, adhering to Google's recommended Android Jetpack architecture guidelines:
- **MVVM Architecture:** Separating concerns into Model-View-ViewModel layers. UI controllers (Activities and Fragments) observe `LiveData` and `StateFlow` streams exposed by ViewModels, guaranteeing that UI states remain decoupled from business logic and survive runtime configuration changes such as device rotation.
- **Local Persistence with Room:** Integrating the Room persistence library over SQLite to provide robust offline caching. Data models are mapped cleanly to relational tables with compile-time SQL verification, and database interactions run on background threads using Kotlin Coroutines and Dispatchers.
- **Material Design & Dynamic Views:** Constructing responsive user interfaces using Material Components, ConstraintLayouts, and optimized `RecyclerView` implementations with custom `DiffUtil` adapters for smooth list animations and low memory overhead.
- **Asynchronous Networking:** Utilizing Retrofit and OkHttp to communicate with external RESTful web services, parsing JSON responses with Moshi/Gson, and handling error states, network disconnects, and loading indicators gracefully.

Working on native Android reinforced the importance of asynchronous programming, UI thread responsiveness (keeping the main thread free from blocking I/O), and careful memory management on resource-constrained mobile devices.

---

## Full-Stack Dynamic Web Application (LAMP Stack: PHP, MySQL, JavaScript)

In my web programming coursework, I developed a complete full-stack web application from the ground up using the LAMP (Linux, Apache, MySQL, PHP) stack with interactive client-side JavaScript.

The application was designed with security, relational data integrity, and user experience in mind:
- **Secure Authentication & Session Management:** Built a complete user management system featuring secure registration and login flows. Implemented salted password hashing via `bcrypt`, HTTP-only session cookies, and role-based access control to protect restricted application routes.
- **Relational Database Design:** Engineered a normalized (3NF) MySQL relational schema with foreign key constraints, cascading updates/deletes, and indexing on high-frequency query paths to ensure fast retrieval times and data consistency.
- **Protection Against Vulnerabilities:** Implemented comprehensive input validation and output encoding to guard against Cross-Site Scripting (XSS), used PHP Data Objects (PDO) with prepared statements to completely eliminate SQL injection vectors, and included CSRF tokens on state-changing forms.
- **Asynchronous Client-Side Interactivity:** Enhanced server-rendered pages with vanilla JavaScript and AJAX `fetch` calls, allowing users to submit data, update preferences, and filter tables dynamically without requiring full-page reloads.

---
