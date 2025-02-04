# OmniRealms: Full Specification & Architecture

This document provides a **comprehensive specification** for the OmniRealms project—a large-scale, modular Java 23 game framework designed to support multiple "realms," procedurally generated worlds, advanced AI, and a robust financial system leveraging blockchain-based microtransactions. It outlines the **modules**, **package structures**, **core interfaces/classes**, **service architecture**, and **extensibility** considerations.

## Table of Contents

1. [Introduction & Goals](#introduction--goals)
2. [Overall Architecture](#overall-architecture)
3. [JPMS & Maven Structure](#jpms--maven-structure)
4. [Module Overview](#module-overview)
   - [1. omnirealms-parent (BOM + Aggregator)](#1-omnirealms-parent-bom--aggregator)
   - [2. omnirealms-core-model](#2-omnirealms-core-model)
   - [3. omnirealms-core-worldgen](#3-omnirealms-core-worldgen)
   - [4. omnirealms-core-backend](#4-omnirealms-core-backend)
   - [5. omnirealms-services-api & omnirealms-services-impl](#5-omnirealms-services-api--omnirealms-services-impl)
   - [6. omnirealms-account-api & omnirealms-account-impl](#6-omnirealms-account-api--omnirealms-account-impl)
   - [7. omnirealms-financial-api & omnirealms-financial-impl](#7-omnirealms-financial-api--omnirealms-financial-impl)
   - [8. omnirealms-ai-api & omnirealms-ai-impl](#8-omnirealms-ai-api--omnirealms-ai-impl)
   - [9. omnirealms-client-engine](#9-omnirealms-client-engine)
   - [10. omnirealms-client-ui](#10-omnirealms-client-ui)
   - [11. omnirealms-editor (Optional)](#11-omnirealms-editor-optional)
5. [Detailed Package Structures & Key Classes](#detailed-package-structures--key-classes)
6. [Service Loader Approach](#service-loader-approach)
7. [Blockchain-Based Financial System](#blockchain-based-financial-system)
8. [AI Integration](#ai-integration)
9. [Realm/Mod/Editor Tools](#realmmodeditor-tools)
10. [Conclusion & Next Steps](#conclusion--next-steps)

## Introduction & Goals

OmniRealms is a **multi-module** Java 23 project, aiming to provide:

* **Procedural & Scalable Worlds**
  * Chunk-based 3D environments that can represent planets, galaxies, microscopic realms, and more
* **Multiple Rendering Styles & Client**
  * A client engine capable of realistic 3D, cartoonish, or isometric rendering via LWJGL
* **Server-Side Authority**
  * A backend module that handles persistence, advanced logic, and optional multiplayer support
* **Hierarchical Account System**
  * Parent/child accounts, tutor/monitor, verified/unverified, with user profiles and permissions
* **Blockchain-Based Microtransactions**
  * Payment channels, escrow, and smart contracts for in-game economies
* **AI Integration**
  * AI players/objects, arbitration for contract disputes, tutoring, or monitoring systems
* **Modding & Realm Editing Tools**
  * A realm editor or mod tools for creating and customizing worlds, blocks, biomes, and more

## Overall Architecture

OmniRealms employs a **layered** approach:

1. **Core Model (Domain Objects)** – Shared between client and server
2. **Backend Logic** – Authoritative game logic, persistence, data management
3. **Services (API & Impl)** – Defines interfaces for cross-module communication
4. **Accounts & Financial** – Specialized modules handling user accounts and blockchain transactions
5. **AI** – For NPC behavior, contract arbitration, content generation, or tutoring
6. **Client Engine & UI** – LWJGL-based rendering, UI overlays, input, and game loop
7. **Editor Tools** – (Optional) Tools for realm creation, block editing, or modding

## JPMS & Maven Structure

### JPMS
Each module has a `module-info.java`, declaring `exports`, `requires`, and `provides`/`uses` for service loading.

### Maven Structure
A single **`omnirealms-parent`** POM acts as both a **BOM** and an **aggregator**. Example directory:

```
omnirealms-parent/
├── pom.xml
├── omnirealms-core-model/
├── omnirealms-core-worldgen/
├── omnirealms-core-backend/
├── omnirealms-services-api/
├── omnirealms-services-impl/
├── omnirealms-account-api/
├── omnirealms-account-impl/
├── omnirealms-financial-api/
├── omnirealms-financial-impl/
├── omnirealms-ai-api/
├── omnirealms-ai-impl/
├── omnirealms-client-engine/
├── omnirealms-client-ui/
└── omnirealms-editor/
```

## Module Overview

### 1. omnirealms-parent (BOM + Aggregator)

* **Role**
  * Manages versions for LWJGL, JUnit, etc.
  * Defines plugin management (compiler, surefire)
  * Aggregates all submodules under `<modules>`
* No Java code in this module

### 2. omnirealms-core-model

* **Purpose**
  * Shared domain objects and interfaces
  * `Realm`, `World`, `Chunk`, `Block`, `Gateway`, `PlayerProfile`, `Vessel`
* No client-rendering or server-specific code
* Referenced by both client and server logic

### 3. omnirealms-core-worldgen

* **Purpose**
  * Procedural generation: noise maps, terrain generation, erosion systems
* Depends on `omnirealms-core-model` to manipulate world/chunk data

### 4. omnirealms-core-backend

* **Purpose**
  * Server-side or backend logic: chunk persistence, realm management
* References domain objects in `core-model`
* May reference `core-worldgen` for server-side generation

### 5. omnirealms-services-api & omnirealms-services-impl

* **API**
  * Defines service interfaces for cross-module functionality
  * Uses domain objects from `core-model`
* **Impl**
  * Implements interfaces with `Local` or `Remote` providers
  * Declares service providers in `module-info.java`

### 6. omnirealms-account-api & omnirealms-account-impl

* **Purpose**
  * Handle account management—parent/child relationships, verification levels
* **API**
  * Interfaces: `AccountService`, `VerificationService`
  * Domain classes: `Account`, `SubAccount`, `VerificationLevel`
* **Impl**
  * `LocalAccountService` (offline) or `RemoteAccountService`

### 7. omnirealms-financial-api & omnirealms-financial-impl

* **Purpose**
  * Blockchain-based microtransactions, escrow, smart contracts
* **API**
  * Interfaces: `FinancialService`, `TransactionService`, `EscrowService`
  * Domain classes: `Transaction`, `EscrowContract`, `TaxInfo`
* **Impl**
  * Multiple providers: `LocalFinancialService`, `HyperledgerFinancialService`

### 8. omnirealms-ai-api & omnirealms-ai-impl

* **Purpose**
  * Integrate AI: NPC controllers, arbitration, tutoring
* **API**
  * Interfaces: `AIPlayerController`, `AIArbitrationService`, `AITutorService`
* **Impl**
  * `LocalAIPlayerController`, `GptArbitrationService`, `NeuralTutorService`

### 9. omnirealms-client-engine

* **Purpose**
  * LWJGL rendering, multiple cameras, chunk meshing, game loop
* Depends on `core-model` for domain data
* Implements different rendering styles

### 10. omnirealms-client-ui

* **Purpose**
  * User interface, 2D menus, HUD overlays, input handling
* Depends on `client-engine` for rendering context

### 11. omnirealms-editor (Optional)

* **Purpose**
  * Tools for realm or mod editing: chunk/block painting
* May rely on `client-engine` for 3D visualization

## Detailed Package Structures & Key Classes

### Core Model
```java
com.novaforge.omnirealms.core.model
    .realm     // Realm, World, Chunk
    .world     // Block, GameObject
    .player    // PlayerProfile, Vessel
    .objects   // Various game objects
```

### Backend
```java
com.novaforge.omnirealms.core.backend
    .persistence   // ChunkManager, RealmManager
    .serverlogic   // BackendGameLoop
    .manager      // Various managers
```

### Services
```java
com.novaforge.omnirealms.services
    .realm     // RealmService
    .player    // PlayerService
    .chunk     // ChunkService
```

### Client
```java
com.novaforge.omnirealms.client
    .engine    // RenderEngine, Camera
    .ui        // MainMenu, HUDOverlay
    .input     // InputManager
```

## Service Loader Approach

### API Example
```java
// In services-api
public interface RealmService {
    Realm getRealmById(String realmId);
    void saveRealm(Realm realm);
}

module com.novaforge.omnirealms.services.api {
    exports com.novaforge.omnirealms.services.realm;
}
```

### Implementation Example
```java
// In services-impl
public class LocalRealmService implements RealmService {
    public Realm getRealmById(String realmId) {
        // Implementation
    }
}

module com.novaforge.omnirealms.services.impl {
    provides RealmService with LocalRealmService;
}
```

## Blockchain-Based Financial System

* **Core Features**
  * Payment channels for microtransactions
  * Smart contracts for escrow and bounties
  * Multiple blockchain provider support
  * Tax and revenue splitting

* **Implementation Strategy**
  * Local testing provider
  * Production blockchain integration
  * Off-chain transaction batching
  * On-chain settlement

## AI Integration

### Core Systems
* NPC behavior controllers
* Contract arbitration
* Tutoring and monitoring
* Content generation

### Implementation Options
* Rule-based local AI
* External ML framework integration
* LLM-powered interactions

## Realm/Mod/Editor Tools

### Features
* Block and chunk editing
* Realm import/export
* Real-time preview
* Permission management

### Integration
* Client engine visualization
* Account system permissions
* World generation tools

## Conclusion & Next Steps

This modular specification provides a scalable foundation for OmniRealms with:

* **Separation of Concerns**
  * Core model for domain objects
  * Backend for server logic
  * Services for abstraction
  * AI and financial systems

* **Flexibility via ServiceLoader**
  * Local or remote implementations
  * Seamless service discovery

* **Next Steps**
  1. Implement core interfaces
  2. Establish test coverage
  3. Prototype service patterns
  4. Develop AI components
  5. Create basic editor interface

This architecture ensures OmniRealms remains organized, extensible, and maintainable as new features are added.