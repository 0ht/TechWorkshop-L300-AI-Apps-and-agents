
FROM python:3.12-slim

# Upgrade pip and system packages to reduce vulnerabilities
RUN apt-get update && apt-get upgrade -y && pip install --upgrade pip && rm -rf /var/lib/apt/lists/*

# Set work directory
WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y \
    build-essential unixodbc-dev gcc g++ \
    libgl1 libglib2.0-0 libsm6 libxext6 libxrender1 \
    && rm -rf /var/lib/apt/lists/*

# Copy requirements and install
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt


# Copy the rest of the code
COPY . .

# Accept ENV_CONTENT as a build argument (passed securely from GitHub Actions)
# This allows the .env file to be created at build time without committing it to the repo
ARG ENV_CONTENT
# Write the ENV_CONTENT to .env file if provided
# This approach ensures secrets are not checked into source control
# Using printf instead of echo for better security (avoids process list exposure)
# The %s format preserves multi-line content without adding extra newlines
RUN if [ -n "$ENV_CONTENT" ]; then printf '%s' "$ENV_CONTENT" > .env; fi

# Expose the port (default FastAPI/uvicorn port)
EXPOSE 8000

# Set environment variables (override in production)
ENV PORT=8000

# Start the FastAPI app
CMD ["uvicorn", "chat_app:app", "--host", "0.0.0.0", "--port", "8000"]
