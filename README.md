graph TD
    %% Entidad Origen
    subgraph Entidad 1 [Entidad: Conservación Edilicia]
        A([Inicio: Usuario crea Requerimiento]) --> B[Ticket nace en Conservación]
        B --> C{¿Lo resuelve el área?}
        C -- SÍ --> D[Asignar a Grupo interno y Resolver]
        C -- NO --> E[Derivar a otra Entidad Ej. Informática]
    end

    %% Capa de Integración API
    subgraph Lógica Backend Laravel (API GLPI)
        E --> F[PUT Ticket: <br>entities_id = ID_Informatica <br>groups_id_assign = 0]
        F --> G[POST ITILFollowup: <br>'Requerimiento derivado a Informática']
    end

    %% Entidad Destino
    subgraph Entidad 2 [Entidad: Dirección de Informática]
        G --> H((Bandeja de Entrada <br> Entidad Informática))
        
        subgraph Rol: Despachador (Perfil con visión global)
            H --> I[Despachador visualiza tickets sin asignar]
            I --> J[Analiza requerimiento]
            J --> K[Deriva al Grupo correspondiente Ej. Redes]
        end
        
        %% Capa de Integración API 2
        K -. "Llamada API" .-> L[PUT Ticket: <br>groups_id_assign = ID_Redes]
        
        subgraph Rol: Técnico (Perfil restringido a sus grupos)
            L --> M[Ticket ingresa a la bandeja del Grupo Redes]
            M --> N[Usuario de Redes visualiza su pendiente]
            N --> O([Fin: Técnico trabaja y Resuelve])
        end
    end

    %% Estilos de los nodos
    classDef inicio fin fill:#c8e6c9,stroke:#2e7d32,stroke-width:2px;
    classDef api fill:#ffe0b2,stroke:#ef6c00,stroke-width:2px;
    classDef decision fill:#fff9c4,stroke:#fbc02d,stroke-width:2px;
    
    class A,O inicio fin;
    class F,G,L api;
    class C decision;
