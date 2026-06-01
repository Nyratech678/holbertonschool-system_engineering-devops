```mermaid
graph TD
    User[User]
    Internet[Internet]

    subgraph LB_Cluster[Load Balancer Cluster]
        LB1[HAProxy LB 1]
        LB2[HAProxy LB 2]
        LB1 <--> LB2
    end

    subgraph Web_Tier[Web Tier]
        Web[Web Server: Nginx]
    end

    subgraph App_Tier[Application Tier]
        App[Application Server]
    end

    subgraph DB_Tier[Database Tier]
        DB[Database Server: MySQL]
    end

    User --> Internet
    Internet --> LB1
    Internet --> LB2
    LB1 --> Web
    LB2 --> Web
    Web --> App
    App --> DB
```

Scale up overview
------------------
- Two load balancers are used as a cluster so the entry point is highly available.
- One web server handles HTTP requests and static content.
- One application server runs the application logic.
- One database server stores the data separately from the application layer.
