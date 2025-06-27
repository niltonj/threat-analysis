graph TD
    subgraph Client-Side (Browser)
        A[User] --> B{React Application};
        B --> C[React Components];
        C --> D{State Management (e.g., Redux, Context API)};
        C --> E{Routing (e.g., React Router)};
        B --> F[API Layer (e.g., Axios, Fetch)];
    end

    subgraph Server-Side
        G[Java Backend (e.g., Spring Boot)] --> H{Controller Layer (REST APIs)};
        H --> I{Service Layer (Business Logic)};
        I --> J{Data Access Layer (e.g., Spring Data JPA)};
        J --> K[Database (e.g., PostgreSQL, MySQL)];
    end

    subgraph Deployment Environment
        L[Web Server (e.g., Nginx, Apache)] --> B;
        M[Application Server (e.g., Tomcat, Undertow)] --> G;
        B -.-> L;
        G -.-> M;
    end

    F --> H;

    subgraph Authentication & Security
        O[Authentication Server (e.g., Auth0, Okta, or custom Spring Security)]
        B --> O;
        G --> O;
    end


    style B fill:#61DAFB,stroke:#333,stroke-width:2px
    style G fill:#f89820,stroke:#333,stroke-width:2px
    style K fill:#d3d3d3,stroke:#333,stroke-width:2px
    style L fill:#90ee90,stroke:#333,stroke-width:2px
    style M fill:#add8e6,stroke:#333,stroke-width:2px
    style O fill:#lightgrey,stroke:#333,stroke-width:2px
