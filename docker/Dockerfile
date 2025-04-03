FROM rayproject/ray:2.9.3-py310

# Set working directory
WORKDIR /app

# Install additional Python dependencies
RUN pip install --no-cache-dir \
    "ray[data]>=2.9.0" \
    vllm \
    pandas \
    numpy \
    torch \
    transformers \
    openai \
    tqdm \
    jupyterlab \
    jupyter \
    matplotlib \
    scikit-learn \
    huggingface_hub \
    datasets

# Copy requirements file if needed
COPY requirements.txt /app/
RUN pip install --no-cache-dir -r requirements.txt

# Copy project files to the container
COPY . /app/

# Set environment variables
ENV RAY_ADDRESS=auto

# Expose Ray ports
EXPOSE 6379 8265 10001 8000 8888

# Default command
CMD ["ray", "start", "--head", "--dashboard-host=0.0.0.0", "--port=6379", "--dashboard-port=8265", "--block"] 