# Architecture

## Applications

```
                                                              ┌───────────┐
                                                              │           │
                                                              │           │
                                                              │  MongoDB  │
                                                              │           │
                                                              │           │
                                                              │           │
                                                              └──▲─────┬──┘
                                                                 │     │
                                                                 │     │
                                                                 │     │
                                                                 │     │
                                                         ┌───────┴─────▼─────────┐                       ┌────────────────────┐
                                                         │                       │                       │                    │
                                                         │                       │                       │                    │
          ┌──────────┐                                   │                       │                       │                    │
          │          │               notify              │                       ├───────────────────────►    climate-gui     │
          │  slack   ◄───────────────────────────────────┤    kotlin-service     │                       │                    │
          │          │                                   │                       ◄───────────────────────┤                    │
          └──────────┘                                   │                       │                       │                    │
                                                         │                       │                       │  www.oyvindis.com  │
                                                         │                       │                       │                    │
                                                         │                       │                       │                    │
                                                         └───────────┬───────────┘                       └────────────────────┘
                                                                     │
                                                                     │
                                                    consume "sensor" │  :31172
                                                                     │
                                                                     │
                                                         ┌───────────▼───────────┐
                                                         │                       │
                                                         │                       │
                                     produce "sensor"    │                       │
                                   ┌─────────────────────►         kafka         │
                                   │       :31172        │                       │
                                   │                     │                       │
┌────────────────┐                 │                     │                       │
│ airthings-api  │                 │                     └───────────────────────┘
└────────▲───────┘                 │
         │                         │
         │                         │
         │           ┌─────────────┴─────────────┐
         │   :get    │                           │
         └───────────┤  kafka-producer-climate   │
                     │                           │
                     └───────────────────────────┘
```