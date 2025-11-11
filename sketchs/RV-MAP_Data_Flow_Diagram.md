Here is the data flow diagram for the RV-MAP project.

## RV-MAP Data Flow Diagram

```mermaid
graph LR
    subgraph "RV-MAP System: Replanning Loop"
        
        %% --- INITIALIZATION PHASE ---
        A[Mission Start: Drone at Position A] --> B("Load Street Graph (OSMnx/NetworkX)");
        B --> C("PR Module: Calculate Optimal Route A-B (Dijkstra | weight = length)");
        
        %% --- FLIGHT LOOP START ---
        C --> D[Control Module: Start route flight];
        D -- Flying Street N --> E[Camera: Capture Image of Street N];
        E --> F(RV Module: Process with YOLOv8-seg);
        
        %% Corrected line: no quotes in edge label
        F -- OBSTACLE and CLEAR_ZONE masks --> G(Geo-Referencer: Calculate % Blockage);
        
        %% --- DECISION AND UPDATE PHASE ---
        G --> H{Blockage Detected > 0% ?};
        H -- NO --> L[Control Module: Continue on same route];
        
        H -- YES --> I(PR Module: Calculate 'Risk Cost v3.0' for Street N);
        I --> J["PR Module: Update Graph (NetworkX) with new weight"];
        J --> K(PR Module: Replan Route from Current Position to B);
        K --> L[Control Module: Follow New Route];
        
        %% --- LOOP END ---
        L --> M{Destination B Reached?};
        M -- NO --> D;
        M -- YES --> N[Mission End];

    end
