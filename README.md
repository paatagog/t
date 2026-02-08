### This repository is intented only for technical purposes

### [ლექცია Meet](https://meet.google.com/fsq-gmmg-xot)

### [ლექცია Zoom](https://us02web.zoom.us/j/3038323328?pwd=QlNxQWtoZU14Rlk0RHRFbmx1MG5PQT09)

```mermaid
flowchart LR
    A[Command Output] --> B[tee]
    B --> C[Terminal]
    B --> D[File]
```

```mermaid
flowchart LR
    A[Command] -->|stdout| B[Terminal]
    A -->|stderr| C[Error Output]
    D[Input File] -->|stdin| A
    A -->|stdout redirected| E[File]
```
