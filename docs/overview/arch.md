The MultiScanner architecture is shown in the figure below. Task management components (Celery/RabbitMQ Server and Postgres Server) provide task assignment and tracking. Data storage is available for both malware samples (distributed file system) and analysis results (Elasticsearch cluster). Worker nodes execute tasks.

![architecture](img/arch1.png)