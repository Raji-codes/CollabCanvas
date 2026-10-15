## 📐 System Architecture

CollabCanvas is built on a serverless, reactive event-driven architecture designed for ultra-low latency real-time collaboration, generative vector synthesis, and client-side vector manipulation.

```mermaid
graph TD
    subgraph Client ["Client Layer (Browser / SPA)"]
        UI[React 18 + Tailwind UI]
        AuthCtx[Auth Context & Session Manager]
        CanvasEngine[Retained-Mode SVG Canvas Engine]
        CoordTransform[Coordinate Transform: Screen ⟷ World Space]
        GenAIService[Gemini GenAI Service]
    end

    subgraph Firebase ["Backend & Real-Time Sync (Google Firebase)"]
        FirebaseAuth["Firebase Authentication (Email/Password)"]
        Firestore["Cloud Firestore (Real-Time NoSQL)"]
        SecurityRules["Firestore Security Rules (RBAC & Validation)"]
    end

    subgraph GeminiAI ["AI Layer (Google Gemini)"]
        GeminiFlash["gemini-2.5-flash Model"]
    end

    subgraph Hosting ["Infrastructure & Deployment"]
        Docker["Multi-Stage Docker Container (Nginx Alpine)"]
    end

    %% Interactions
    UI --> AuthCtx
    AuthCtx <-->|ID Token & State| FirebaseAuth
    UI --> CanvasEngine
    CanvasEngine <--> CoordTransform
    CanvasEngine -->|Optimistic Write / Sync| Firestore
    Firestore -->|onSnapshot WebSockets Push| CanvasEngine
    Firestore --- SecurityRules
    CanvasEngine -->|Prompt & Canvas Context| GenAIService
    GenAIService <-->|Structured JSON Schema| GeminiFlash
    Docker -->|Serves Production Bundle| Client
