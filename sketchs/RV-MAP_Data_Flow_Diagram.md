```mermaid
graph TD
    subgraph Sistema_RV_MAP_Bucle_Replanificacion
        %% --- FASE DE INICIALIZACIÓN ---
        A[Inicio Misión: Dron en Posición A] --> B[Cargar Grafo de Calles (OSMnx/NetworkX)];
        B --> C[Módulo PR: Calcular Ruta Óptima A-B (Dijkstra | peso = longitud)];

        %% --- INICIO DEL BUCLE DE VUELO ---
        C --> D[Módulo Control: Iniciar vuelo por ruta];
        D -->|Vuela por Calle N| E[Cámara: Capturar Imagen de Calle N];
        E --> F[Módulo RV: Procesar con YOLOv8-seg];
        F -->|Máscaras OBSTÁCULO/ZONA_VIABLE| G[Geo-Referenciador: Calcular % de Bloqueo];

        %% --- FASE DE DECISIÓN Y ACTUALIZACIÓN ---
        G --> H{¿Bloqueo > 0%?};
        H -->|NO| L[Módulo Control: Continuar por la misma ruta];

        H -->|SI| I[Módulo PR: Calcular Coste de Riesgo v3.0 para Calle N];
        I --> J[Módulo PR: Actualizar Grafo con nuevo peso];
        J --> K[Módulo PR: Replanificar Ruta desde Posición Actual hasta B];
        K --> L;

        %% --- FIN DEL BUCLE ---
        L --> M{¿Destino B Alcanzado?};
        M -->|NO| D;
        M -->|SI| N[Fin de Misión];
    end
'''