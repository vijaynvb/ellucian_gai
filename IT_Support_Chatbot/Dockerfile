# Use Ubuntu-based Python image
FROM ubuntu:22.04

# Set the working directory
WORKDIR /app

# Install Python and required system packages
RUN apt-get update && apt-get install -y \
    python3.9 \
    python3-pip \
    python3-venv \
    build-essential \
    wget \
    && rm -rf /var/lib/apt/lists/*

# Set default Python to Python 3.9
RUN ln -s /usr/bin/python3.9 /usr/bin/python

# Copy the requirements file
COPY requirements.txt .

# Upgrade pip and install dependencies
RUN pip install --no-cache-dir --upgrade pip
RUN pip install --no-cache-dir torch torchvision torchaudio faiss-cpu sentence-transformers
RUN pip install --no-cache-dir -r requirements.txt

# Copy the rest of the application
COPY . .

# Expose the Streamlit port
EXPOSE 8081

# Run Streamlit app
CMD ["streamlit", "run", "streamlit.py", "--server.port", "8081", "--server.address", "0.0.0.0"]
