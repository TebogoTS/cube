# Cube Operator

## Overview
The Cube Operator is a container orchestrator designed to manage and run Docker containers on Linux VMs. It automates the scheduling, execution, and monitoring of containerized tasks across multiple worker nodes, ensuring efficient resource utilization and scalability. The operator consists of a Manager component that handles task scheduling, storage, and metrics, working in tandem with distributed Workers to execute tasks.

## Architecture
The Cube Operator operates within an orchestration system with the following components:
- **Manager**: The central component responsible for:
  - **Scheduling**: Evaluates task feasibility, scores tasks based on resource availability, and assigns them to Workers.
  - **API**: Provides an interface for task submission, status queries, and metrics access.
  - **Task Storage**: Stores task definitions and states (e.g., running tasks).
  - **Workers**: Manages the execution of tasks on distributed Linux VMs.
  - **Metrics**: Collects and exposes performance metrics for system monitoring.
- **Workers**: Linux VMs that execute Docker containers, receiving tasks from the Manager via the API.
- **Storage**: Persistent storage for task data and states, accessible by both Managers and Workers.

The Manager assesses task feasibility, scores them, and assigns them to Workers. Workers then run the tasks as Docker containers and report status back to the Manager, as depicted in the orchestration system diagram.

## Features
- **Task Scheduling**: Automatically schedules Docker container tasks based on feasibility and resource scoring.
- **Scalability**: Supports multiple Managers and Workers for distributed task execution across Linux VMs.
- **Monitoring**: Provides metrics for task performance and system health.
- **Persistence**: Stores task states and data in a dedicated storage layer.

## Installation
### Prerequisites
- Linux VMs running a compatible OS (e.g., Ubuntu 20.04 or later)
- Docker installed on all VMs (`docker` command-line tool)
- SSH access between the Manager and Worker VMs
- A shared storage solution (e.g., NFS) for task data persistence

### Steps
1. **Clone the Repository**
   ```
   git clone https://github.com/cube-operator/cube-operator.git
   cd cube-operator
   ```

2. **Set Up the Manager**
   On the VM designated as the Manager, install dependencies and start the Manager service:
   ```
   ./scripts/install-manager.sh
   ./bin/cube-manager --config config/manager.yaml
   ```

3. **Set Up Workers**
   On each Worker VM, install Docker (if not already installed) and the Worker service:
   ```
   ./scripts/install-worker.sh
   ./bin/cube-worker --manager-url http://<manager-ip>:8080
   ```

4. **Configure Shared Storage**
   Ensure the shared storage is mounted on all VMs (e.g., `/mnt/cube-storage`) and update the `config/manager.yaml` with the storage path.

5. **Verify Setup**
   Check the Manager’s API to confirm Workers are registered:
   ```
   curl http://<manager-ip>:8080/workers
   ```

## Usage
1. **Define a Task**
   Create a task definition in a JSON file (e.g., `task.json`):
   ```json
   {
     "name": "example-task",
     "image": "nginx:latest",
     "command": ["nginx", "-g", "daemon off;"]
   }
   ```

2. **Submit the Task**
   Submit the task to the Manager via the API:
   ```
   curl -X POST http://<manager-ip>:8080/tasks -d @task.json -H "Content-Type: application/json"
   ```

3. **Monitor Task Status**
   Check the task status via the API:
   ```
   curl http://<manager-ip>:8080/tasks/example-task
   ```

4. **View Metrics**
   Access system metrics via the Manager’s API:
   ```
   curl http://<manager-ip>:8080/metrics
   ```

## Contributing
Contributions are welcome! Please submit a pull request or open an issue on the [GitHub repository](https://github.com/cube-operator/cube-operator).

## License
This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.