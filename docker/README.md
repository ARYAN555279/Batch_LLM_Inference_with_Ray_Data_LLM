# Docker Setup for Ray Data LLM

This directory contains Docker configuration files for running the Batch LLM Inference with Ray Data LLM project.

## Requirements

- Docker
- Docker Compose
- NVIDIA Container Toolkit (for GPU support)

## Quick Start

1. Build and start the containers:

```bash
docker-compose up -d
```

2. Access the Jupyter Lab interface:
   - Open your browser and navigate to `http://localhost:8888`
   - No password is required (configured for development)

3. Access the Ray Dashboard:
   - Open your browser and navigate to `http://localhost:8265`

## Configuration

The Docker setup includes:

- **ray-head**: The main Ray head node that coordinates all computation
- **jupyter**: A Jupyter Lab environment connected to the Ray cluster

## GPU Support

The setup is configured to use GPUs if available. Make sure you have the NVIDIA Container Toolkit installed:

```bash
# Check if NVIDIA Container Toolkit is installed
sudo docker run --gpus all nvidia/cuda:11.8.0-base-ubuntu22.04 nvidia-smi
```

## Data Persistence

Data is persisted through a Docker volume:

- **ray-data**: Stores Ray temporary data

## Customization

You can customize the setup by:

1. Modifying the `Dockerfile` to add additional dependencies
2. Adjusting the `docker-compose.yml` file to change port mappings or resource allocations
3. Updating the `requirements.txt` file to include specific package versions

## Troubleshooting

- If you encounter issues with GPU access, make sure your NVIDIA drivers and NVIDIA Container Toolkit are properly installed.
- If Ray cannot access enough resources, try adjusting the memory and CPU allocations in the `docker-compose.yml` file. 