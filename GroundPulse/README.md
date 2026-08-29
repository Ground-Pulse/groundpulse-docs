# GroundPulse 🌐
> **The 3-Step Remote Property Diagnostic & Health Monitoring Platform.**

[![OJT Project](https://img.shields.io/badge/OJT%20Project-Phase%201%20Validation-blue)](#)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](#)

## 📌 Executive Summary
GroundPulse solves the physical visibility and trust gap for out-of-city property owners and NRIs. Built around a friction-free **3-Step Mobile Workflow**, GroundPulse delivers standardized 40-point diagnostic audits with tamper-proof, geofenced proof of inspection.

## 🚩 The Problem
- **Unmonitored Vacant Assets:** Millions of out-of-city owners cannot physically inspect property integrity (water seepage, roof damage, boundary encroachment).
- **The Rental Broker Bias:** Existing platforms prioritize rental commissions, leaving vacant homes and plots neglected.
- **App Download Friction:** Owners avoid heavy native apps for infrequent (quarterly) property audits.

## 💡 The GroundPulse Solution
- **Zero-Friction Owner Portal:** Mobile-first responsive web app (PWA) with instant WhatsApp/SMS report links.
- **Geofenced Field Inspector App:** Dedicated on-site audit workflow with tamper-proof GPS and offline sync.
- **Standardized 40-Point Diagnostic:** Objective structural, electrical, plumbing, and perimeter scoring.

## 🧱 Repository Structure
- `docs/` - PRD, system architecture, competitor analysis, and customer discovery logs.
- `apps/owner-web/` - Mobile-first Next.js PWA for property owners (3-step flow).
- `apps/inspector-app/` - Field inspection client with camera and GPS geofencing.
- `apps/server/` - Backend REST API, auth (RBAC), and automated PDF report engine.
- `packages/database/` - PostgreSQL schema via Prisma ORM.
- `packages/shared-types/` - Shared TypeScript interfaces.

## 🗺️ 14-Week OJT & Startup Roadmap
- [x] **Week 1–2:** Problem Discovery, Competitor Gap Analysis & Monorepo Setup
- [ ] **Week 3–4:** 40-Point Audit Engine Specs, Wireframes & Prisma DB Schema
- [ ] **Week 5–8:** Core MVP Build (Auth, Property Registration, Inspector Workflow)
- [ ] **Week 9–11:** Automated Health Scoring & Certified PDF Report Generation
- [ ] **Week 12–14:** Field Pilot (5 Live Properties) & Demo Day Pitch Deck

## 🛠️ Tech Stack
- **Owner Interface:** Next.js (Mobile-First PWA), TailwindCSS, TypeScript
- **Inspector App:** React Native (Expo) / Mobile Web PWA
- **Backend:** Node.js, Express / NestJS, TypeScript
- **Database & Storage:** PostgreSQL (Prisma ORM), Cloud Object Storage