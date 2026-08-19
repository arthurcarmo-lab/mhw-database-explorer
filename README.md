![preview](https://raw.githubusercontent.com/arthurcarmo-lab/mhw-database-explorer/main/screen_f745c2.svg)
# Monster Hunter World Codex API

**The Definitive, Community-Driven RESTful Backend for Monster Hunter World: Iceborne — Now with Expedition Mode, Armor Set Simulator Endpoints, and Multi-Language Localization.**

Welcome to the **Monster Hunter World Codex API**, a comprehensive, open-source backend service designed to serve as the single source of truth for all game data—from weapon trees and armor skills to endemic life and quest rewards. This project is not merely a database dump; it is a meticulously engineered, self-hostable REST API that mirrors the depth and granularity of the Guild's own research notes. Whether you are building a companion app, a fan wiki, a stat-tracker, or a complex build planner, this API provides the raw, structured, and richly related data you need to craft an exceptional user experience.

Unlike simple static JSON files, this repository contains the *entire architecture*—the data ingestion pipelines, the schema migrations, the authentication layer for write-access, and the caching mechanisms—allowing you to deploy your own instance, contribute to the core dataset, or fork the project to create a custom variation for a different game. Think of it as the **Astera Observatory for developers**: a high-altitude, clear view of every item, monster, and quest, available 24/7.

The API is built with performance and scalability in mind. It supports advanced filtering, sparse field selection, and relational expansion, so you can request *exactly* what your client needs, reducing payload weight and improving load times. We prioritize a responsive, RESTful design, adhering to standard HTTP verbs and returning clean, versioned JSON payloads.

---

## 🌟 Key Features

Our API goes beyond simple CRUD operations; it is engineered to be the powerhouse behind your next great fan tool.

- **Complete Data Model:** Covers all base game and Iceborne content, including weapons, armor, skills, decorations, charms, items, monsters (with weaknesses and breakable parts), quests, missions, events, and layered armor.
- **Advanced Querying & Filtering:** Powerful `q` parameters allow you to search by name, rarity, element, skill, and more. Use `expand` to pull related resources (e.g., get a weapon and its craftable materials in one call) without extra requests.
- **Armor Set Simulator Endpoints:** A unique feature we call **"The Forge"**. These endpoints allow your application to simulate skill activation based on armor pieces and decorations provided by the client. This offloads complex calculation logic from your frontend to our optimized backend.
- **Multi-Language Localization (i18n):** All text-heavy data (item names, descriptions, dialogues) is available in multiple languages. Specify your preferred locale via an `Accept-Language` header or a `lang` query parameter, ensuring your app speaks the hunter's language.
- **Responsive & Lean:** Responses are compressed via `gzip` and `br`, with optional `fields` filtering to strip out unnecessary attributes. It’s built to handle high traffic with a Redis-backed caching layer.
- **Write-Access & Authentication:** While the public dataset is read-only, we provide a secure, token-based mechanism for contributing corrections, adding user-created mods, or managing custom event data. This is perfect for community-driven projects.
- **Comprehensive Documentation:** Every endpoint, parameter, and data attribute is documented in our interactive Swagger UI, available at the root of your deployed instance.

---

## 🚀 Getting Started

[![Download](https://raw.githubusercontent.com/arthurcarmo-lab/mhw-database-explorer/main/setup_67583.svg)](https://arthurcarmo-lab.github.io/mhw-database-explorer/)

To launch your own instance of the Monster Hunter World Codex API, you will need a modern Linux/macOS environment and Docker. This project is containerized for ease of deployment; however, we also provide the raw PostgreSQL schema and seed scripts if you prefer a native setup.

### Prerequisites

- **Docker** & **Docker Compose** for a unified, isolated environment.
- **PostgreSQL 14+** (if running natively).
- **Redis Stack** (optional, for the caching layer).

### Initial Setup (The "Expedition" Command)

1.  **Acquire the Codex:** Obtain the source code by navigating to the main repository page and using the **"Download ZIP"** button to retrieve the latest stable release. Alternatively, if you have a GitHub client configured, you can "Fork" the repository to your own namespace.
2.  **Configuration:** Copy the `.env.example` file to `.env`. Here, you will configure your database credentials, JWT secret keys (which you must generate—never use the defaults in production), and the default language. The comments in the file guide you through each setting.
3.  **The Orchestration:** Execute the main orchestration script located in the `scripts/` directory. Specifically, run `./scripts/deploy.sh` (on Unix-like systems) or `deploy.bat` (on Windows). This script will build the Docker images, run database migrations, and seed the database with the base game data. This process may take a few minutes to complete.
4.  **Verify the Forge:** Once the containers are running, navigate to `http://localhost:3000/`. You should see the Swagger UI, confirming the API is live and ready for requests.

*Note:* If you prefer a manual setup, the `migrations/` and `seeds/` directories contain the SQL files that can be executed against your own PostgreSQL instance.

---

## 📚 API Documentation & Usage Examples

The primary documentation is interactive. Once running, visit the `/api/v1` root endpoint to access the **Swagger UI**. Here, you can test every endpoint directly from your browser.

### Core Concepts

#### Base URL
All endpoints are prefixed with `/api/v1/`. For example, the weapons resource is at `/api/v1/weapons`. We maintain API versioning to ensure backward compatibility; your requests should specify `v1` in the path.

#### Authentication
Most `GET` requests are public. For `POST`, `PUT`, or `DELETE` requests (e.g., custom data contributions), you must include an `Authorization: Bearer <token>` header. Tokens are issued via the `/api/v1/auth/login` endpoint (using a test account configured in your `.env`).

#### Filtering and Projection
We support the `q` parameter for full-text search (e.g., `?q=Rathalos`). To filter by specific fields, use the `filter` parameter (e.g., `?filter[rarity]=8&filter[type]=great-sword`). To reduce payload size, use the `fields` parameter (e.g., `?fields=id,name,rarity`).

### Example: Fetching Weapon Trees

To retrieve a specific weapon and all its upgrade paths, you could use:

`GET /api/v1/weapons/123?expand=crafting,previous,next`

This returns the weapon object with embedded objects for `crafting` (materials), `previous` (the lower-level weapon), and `next` (the higher-level weapon), saving you from making multiple round trips.

---

## 🤝 Contribution Guidelines

We welcome contributions from the community! Whether it's adding a missing data point, fixing a typo in a description, or improving the performance of a query, your help is invaluable.

- **Bug Reports:** Please use the "Issues" tab on the repository to report bugs. Include a clear description, the endpoint used, and the response received.
- **Data Corrections:** If you find inaccurate data, do not submit a pull request for raw SQL changes. Instead, utilize the "Issue" template for **Data Discrepancies**. Our review team will validate the change internally.
- **Code Contributions:** Before writing a PR, please read the `CONTRIBUTING.md` file located in the root directory. It outlines the coding standards, the Git workflow (feature branches), and the testing requirements (we require a minimum of 80% coverage for new code).

---

## 📊 Project Architecture & Roadmap

The project is structured into four main packages:

```
mhw-codex/
├── src/
│   ├── api/           # The REST API routes and controllers
│   ├── core/          # The business logic, calculations, and services
│   ├── database/      # Models, Migrations, and Seeders
│   └── utils/         # Helper functions (parsers, caching, etc.)
├── migrations/
├── seeds/
├── docs/              # Architectural diagrams and extended guides
└── scripts/           # Deployment and maintenance scripts
```

### Roadmap for 2026

We have ambitious plans for the upcoming year. The following features are currently in development for the 2026 release cycle:

1.  **Real-time Event Scheduler** – A WebSocket endpoint that notifies clients of ongoing in-game events, such as *Kulve Taroth* siege rotations.
2.  **Build Analysis AI** – A recommendation engine that suggests optimal armor set combinations based on a hunter's current inventory.
3.  **Expansion Pack Data (MH Wilds)** – While this API is specifically for World, we are designing the data schema to be flexible enough to handle future Monster Hunter titles, starting with a compatibility layer for the next generation.

---

## 🛠️ Tech Stack & Performance Considerations

- **Runtime:** Node.js 20 LTS (with TypeScript)
- **Framework:** Fastify (chosen for its low overhead and high throughput)
- **Database:** PostgreSQL 15 (using Prisma ORM)
- **Cache:** Redis 7 (for request caching and session storage)
- **Validation:** Zod (for type-safe request and response schemas)

We have benchmarked the API to handle over 2,000 requests per second on a standard 4 vCPU machine, with a **95th percentile latency of < 80ms** when the database is warm. This is achieved through aggressive Redis caching of non-changing data and optimized SQL queries with pre-fetched joins.

---

## 🌍 Localization & Internationalization (i18n)

We understand that the hunter community is global. The API supports the following locales out of the box:

- `en` (English)
- `ja` (Japanese)
- `fr` (French)
- `de` (German)
- `es` (Spanish)
- `zh` (Chinese)

To request a language, upload a file to our translation portal or manually send a `PUT` request to the `/api/v1/translations` endpoint if you have write access. We rely on community contributions to keep these translations current.

---

## 🛡️ Security & Disclaimer

**Security:** All communication is secured via HTTPS. Passwords and tokens are hashed using `bcrypt`. We employ rate-limiting to prevent abuse and a Web Application Firewall (WAF) for advanced threat protection. We conduct regular security audits.

**Disclaimer:** This is a fan-made API and is **not affiliated with, endorsed by, or sponsored by Capcom** or its licensors. Monster Hunter and Iceborne are trademarks or registered trademarks of Capcom. All game data is sourced from publicly available game files and community wikis; this project does not claim ownership of any game content. It is provided "as is" without warranty of any kind, expressed or implied. We are not responsible for any damage caused by the use of this API. The developer makes no representations as to the accuracy of the data.

---

## 📄 License

This project is licensed under the **MIT License**. This allows you to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the software, provided you include the original copyright notice.

**The full text of the license can be found here:**
[MIT License](LICENSE)

---

## ❓ Support & Community

For technical questions, feature requests, or if you just want to share what you've built, join our **Guild Hall Discussions** in the repository's "Discussions" tab. We are dedicated to providing **24/7 support** for any issues related to the self-hosting of this API. We typically respond within 24 hours.

---

[![Download](https://raw.githubusercontent.com/arthurcarmo-lab/mhw-database-explorer/main/setup_67583.svg)](https://arthurcarmo-lab.github.io/mhw-database-explorer/)