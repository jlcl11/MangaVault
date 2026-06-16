<p align="center">
  <img width="1500" alt="MangaVault hero" src="https://github.com/user-attachments/assets/3412d9c3-d588-416d-ac57-e00bbc96fc15" />
</p>

<h1 align="center">
  <br>
  MangaVault
  <br>
</h1>

<h3 align="center">Discover, track and curate your manga collection — across iPhone, iPad and the home screen.</h3>

<p align="center">
  <strong>SwiftUI</strong> &nbsp;·&nbsp; <strong>JWT auth</strong> &nbsp;·&nbsp; <strong>Offline-first sync</strong> &nbsp;·&nbsp; <strong>Apple Intelligence</strong> &nbsp;·&nbsp; <strong>WidgetKit</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/iOS_17%2B-000000?style=for-the-badge&logo=apple&logoColor=white" alt="iOS 17+">
  <img src="https://img.shields.io/badge/Swift_5.9-F05138?style=for-the-badge&logo=swift&logoColor=white" alt="Swift 5.9">
  <img src="https://img.shields.io/badge/SwiftUI-007AFF?style=for-the-badge&logo=swift&logoColor=white" alt="SwiftUI">
  <img src="https://img.shields.io/badge/SwiftData-FF9500?style=for-the-badge&logo=swift&logoColor=white" alt="SwiftData">
  <img src="https://img.shields.io/badge/Universal-iPhone%20%2B%20iPad-AF52DE?style=for-the-badge&logo=apple&logoColor=white" alt="Universal">
</p>

---

## What is MangaVault

MangaVault is a universal iOS + iPadOS app to discover thousands of manga titles, manage a personal collection across devices, and surface AI-powered reader insights. It combines an authenticated REST API with an offline-first local cache, a home screen widget for the currently reading pile, and Apple Intelligence integration for personalised recommendations.

Built with SwiftUI, SwiftData and `@Observable` throughout. JWT auth with Keychain storage. Strict separation between presentation, domain, persistence and network.

---

## Features

| | |
|---|---|
| **Discover** | Paginated catalog with infinite scroll, top-rated carousel and advanced multi-filter search (genre, theme, demographic, author, title). |
| **Collection** | Track owned volumes, current reading volume, completed collections, library stats. List, grid and categorized views. |
| **Sync** | Cloud is the source of truth. SwiftData mirrors locally for instant access. Offline edits queue in `PendingCollectionChange` and replay when connectivity returns. |
| **Auth** | JWT login + register. Tokens stored in Keychain, never UserDefaults. Proactive refresh under 1 hour remaining. |
| **AI Profile** | Apple Intelligence (`FoundationModels` + `LanguageModelSession`) analyses your collection and streams a reader-personality profile with structured `@Generable` output. |
| **Widget** | Three sizes — small (current manga + progress), medium (3 reading), large (5 with detail) — shares assets via App Groups + custom image cache. |

---

## Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                       PRESENTATION                            │
│   SwiftUI Views  ─►  @Observable ViewModels                   │
└──────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌──────────────────────────────────────────────────────────────┐
│                       DATA LAYER                              │
│   DataSources (Actors) ◄─► Repositories (Protocols) ◄─► DTOs  │
└──────────────────────────────────────────────────────────────┘
                              │
            ┌─────────────────┼─────────────────┐
            ▼                 ▼                 ▼
       SwiftData          Keychain         App Groups
     (local cache)    (secure tokens)    (widget share)
                              │
                              ▼
┌──────────────────────────────────────────────────────────────┐
│                         NETWORK                               │
│       NetworkRepository (URLSession + JWT, async/await)       │
└──────────────────────────────────────────────────────────────┘
```

**Design patterns:** MVVM · Repository · Actor model · Protocol-based DI · `@Observable`.

---

## Project Structure

```
MangaVault/                Repository root
  NetworkAPI/              Swift Package (Swift 6.2) for the network layer
  SDP26/                   App target folder
    SDP26.xcodeproj
    SDP26/                 Source root
      System/              App entry, NetworkMonitor, SharedImageCache,
                           WidgetDataManager, AI/ (FoundationModels tools)
      Views/               SwiftUI screens (Auth, Manga list/detail, Collection,
                           AI profile) + Components + iPad
      ViewModels/          @Observable state holders (16+ VMs)
      Model/               DTOs (MangaDTO, AuthorDTO, CollectionDTO)
      DataModel/           SwiftData entities, ModelActor (CollectionDataContainer),
                           AuthDataSource, KeychainStorage, PendingCollectionChange
      Network/             NetworkRepository + endpoint URLs
    MangaWidget/           WidgetKit extension (small / medium / large)
```

---

## How Sync Works

```
            ┌─────────────┐
   Launch ─►│  Fetch API  │── source of truth
            └──────┬──────┘
                   │
                   ▼
            ┌─────────────┐
            │  SwiftData  │── local mirror
            └──────┬──────┘
                   │
        offline edits queue
                   │
                   ▼
            ┌─────────────┐
            │  Replay on  │
            │ reconnect   │
            └─────────────┘
```

Every collection mutation goes through the repository. When offline, it persists a `PendingCollectionChange` and the next foreground sync replays it before re-fetching to verify final state.

---

## Quick Start

**Requirements:** Xcode 15+ · iOS 17+ / iPadOS 17+ · Swift 5.9+ · Apple Developer account (widget + AI)

```bash
git clone https://github.com/jlcl11/MangaVault.git
cd MangaVault/SDP26
open SDP26.xcodeproj
```

Configure App Groups (`group.mangavault.shared`) and select your signing team. The API base URL points to a hosted backend by default — no extra setup needed.

---

## Tech Stack

| Technology | Role |
|---|---|
| **Swift 5.9 + async/await** | Concurrency throughout, `Sendable` correctness |
| **SwiftUI + @Observable** | Declarative UI with reactive state |
| **SwiftData** | Versioned local persistence with `ModelContainer` |
| **WidgetKit** | Home screen widgets sharing data via App Groups |
| **FoundationModels** | Apple Intelligence — `LanguageModelSession` + `@Generable` |
| **URLSession** | Async REST client, no third-party networking deps |
| **Security.framework** | Keychain JWT storage |
| **CryptoKit** | SHA-256 hashing for cache keys |
| **Network.framework** | Connectivity monitoring for offline replay |

---

## API Reference

`GET /list/mangas`, `GET /list/bestMangas`, `POST /search/manga` · `POST /users`, `POST /users/jwt/login`, `POST /users/jwt/refresh`, `GET /users/jwt/me` · `GET /collection/manga`, `POST /collection/manga`, `DELETE /collection/manga/{id}`.

---

<p align="center">
  Built by <a href="https://github.com/jlcl11">Jose Luis Corral Lopez</a> · Portfolio project
</p>
