# Role Index

Quick-reference index for matching user requests to role categories.

| # | File | Category | Keywords | Roles |
|---|------|----------|----------|-------|
| 01 | `01-software-development.md` | Software Development | coding, code, frontend, backend, full-stack, mobile, API, REST, GraphQL, app, application, development, developer, react, vue, angular, next, node, python, java, typescript, javascript, C#, .NET, Go, Rust, PHP, Laravel, Django, Flask, Spring, Ruby, Rails, Swift, Kotlin, Flutter, React Native, ERP, SAP, CRM, Salesforce, blockchain, smart contract, Solidity, embedded, IoT, firmware, microcontroller, low-code, plugin, extension, desktop, Electron | 10 |
| 02 | `02-design-ux.md` | Design & UX | design, UI, UX, user interface, user experience, usability, accessibility, wireframe, prototype, mockup, figma, sketch, adobe, responsive, interaction design, information architecture, design system, visual design, graphic design, motion design, brand design | 7 |
| 03 | `03-data-ai.md` | Data & AI | data, machine learning, ML, AI, artificial intelligence, analytics, data science, NLP, natural language processing, deep learning, neural network, model, dataset, pipeline, ETL, data warehouse, BI, business intelligence, big data, Spark, TensorFlow, PyTorch, LLM, GPT, RAG, vector database, data engineering, data visualization, statistics, prediction, recommendation | 10 |
| 04 | `04-cybersecurity.md` | Cybersecurity | security, vulnerability, penetration testing, pentest, firewall, GDPR, encryption, compliance, audit, SOC, SIEM, incident response, threat, malware, phishing, authentication, authorization, OAuth, JWT, OWASP, zero trust, red team, blue team, forensics, cryptography, IAM, identity, access control | 10 |
| 05 | `05-infrastructure-operations.md` | Infrastructure & Ops | cloud, DevOps, server, network, database, SQL, PostgreSQL, MySQL, MongoDB, Redis, Kubernetes, K8s, deploy, deployment, AWS, Azure, GCP, CI/CD, Docker, container, terraform, ansible, infrastructure, monitoring, logging, Prometheus, Grafana, Linux, nginx, load balancer, CDN, DNS, SSL, SRE, reliability, scaling, performance, migration, backup | 9 |
| 06 | `06-management-organization.md` | Management | project management, agile, scrum, kanban, leadership, strategy, product, product management, roadmap, sprint, backlog, stakeholder, OKR, KPI, budget, team, hiring, CTO, VP engineering, program management, delivery, release planning, estimation | 14 |
| 07 | `07-quality-testing.md` | QA & Testing | test, testing, quality, QA, quality assurance, test automation, Cypress, Playwright, Selenium, Jest, pytest, performance testing, load testing, regression, E2E, end-to-end, unit test, integration test, TDD, BDD, coverage, bug, defect, SDET | 7 |
| 08 | `08-marketing-digital-content.md` | Marketing & Content | marketing, SEO, SEM, social media, content, content strategy, campaigns, brand, branding, ads, advertising, Google Ads, Facebook Ads, email marketing, newsletter, growth hacking, copywriting, blogging, video marketing, influencer, analytics, conversion, funnel, landing page, A/B test, CMO | 13 |
| 09 | `09-ecommerce-marketplace.md` | E-commerce | e-commerce, ecommerce, marketplace, Amazon, Shopify, WooCommerce, Magento, catalog, online store, online sales, pricing, inventory, fulfillment, payment gateway, Stripe, PayPal, checkout, cart, product listing, dropshipping | 7 |
| 10 | `10-sales-customer-success.md` | Sales & CS | sales, CRM, customer success, onboarding, revenue, churn, retention, renewal, pre-sales, account management, upsell, cross-sell, pipeline, lead generation, customer support, NPS, CSAT, RevOps, sales engineering, demo | 7 |
| 11 | `11-it-support-administration.md` | IT Support | support, help desk, service desk, IT assets, licenses, IT compliance, ITIL, procurement, sysadmin, system administration, Active Directory, Office 365, endpoint, MDM, ticket, SLA, IT audit, network admin | 7 |
| 12 | `12-consulting-training.md` | Consulting & Training | architecture, enterprise architecture, solution architecture, consulting, training, documentation, technical writing, developer relations, DevRel, evangelist, workshop, mentoring, knowledge base, onboarding docs, API documentation | 10 |
| 13 | `13-specialized-roles.md` | Specialized | digital transformation, DPO, data protection, accessibility, WCAG, open source, localization, i18n, l10n, translation, ethics, AI ethics, GIS, geospatial, quantitative analysis, bioinformatics, vendor management, recruiting | 12 |
| 14 | `14-game-development.md` | Game Development | game, videogame, video game, game design, level design, Unity, Unreal, Godot, game AI, multiplayer, netcode, 3D modeling, sprites, pixel art, VFX, shader, game engine, NPC, physics, ragdoll, HUD, gamedev, indie game, AAA, game jam, Steam, console, mobile game, RPG, platformer, FPS, MMO, battle royale | 20 |

## Intent Patterns

Natural-language patterns that map to categories when keyword matching fails. Use these in Pass 1.5 (before semantic inference).

| Intent Pattern | Maps To | Primary Role |
|---------------|---------|-------------|
| "my site/app is slow", "page takes long to load" | Infrastructure & Ops | SRE |
| "it doesn't work", "something broke", "getting an error" | Software Development | Full-Stack Developer (then narrow by context) |
| "login doesn't work", "can't authenticate", "session expired" | Cybersecurity | IAM Specialist |
| "make it look better", "the UI is ugly", "redesign" | Design & UX | UI Designer |
| "how do I deploy", "put it online", "go live" | Infrastructure & Ops | DevOps Engineer |
| "we need more users", "grow our audience", "get more traffic" | Marketing & Content | Growth Hacker / SEO Specialist |
| "customers are leaving", "churn is high", "retention" | Sales & CS | Customer Success Manager |
| "it's not secure", "we got hacked", "data breach" | Cybersecurity | Incident Response Analyst |
| "need a database", "store the data", "data model" | Infrastructure & Ops | Database Administrator |
| "write tests", "it keeps breaking", "quality" | QA & Testing | QA Automation Engineer |
| "make it accessible", "screen reader", "WCAG" | Specialized | Accessibility Engineer |
| "sell online", "add payments", "shopping cart" | E-commerce | E-commerce Director |
| "train the team", "documentation", "onboarding guide" | Consulting & Training | Technical Writer / Technical Trainer & Coach |
| "manage the project", "we're behind schedule", "sprint planning" | Management | Scrum Master / Project Manager |
| "analyze the data", "dashboard", "metrics", "KPIs" | Data & AI | Data Analyst |
| "build a game", "game mechanics", "level design" | Game Development | Game Designer |
| "automate this", "repetitive task", "workflow" | Software Development | Low-Code/No-Code Developer |
| "AI feature", "chatbot", "recommendation engine" | Data & AI | Machine Learning Engineer / NLP Engineer |
| "mobile app", "iOS/Android", "app store" | Software Development | Mobile Developer |
| "API integration", "connect to", "webhook" | Software Development | Backend Developer |

**Usage**: When a user request doesn't match any keyword in the main index, scan this table for semantic similarity. The "Maps To" column identifies the category file to read; the "Primary Role" gives a starting point (refine by reading the full role file).

**Total: 143 roles across 14 categories**
