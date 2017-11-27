Architecture
------------
The MultiScanner architecture is shown in Figure 1. Task management components (Celery/RabbitMQ Server and Postgres Server) provide task assignment and tracking. Data storage is available for both malware samples (distributed file system) and analysis results (Elasticsearch cluster). Worker nodes execute tasks.

![architecture1](../img/arch1.png "Figure 1")

Workflow
--------
The MultiScanner workflow is shown in Figure 2. Each step is described below the figure.

![architecture2](../img/arch2.png "Figure 2")