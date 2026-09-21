FROM python:3.12-slim

WORKDIR /app

COPY memory_info.py requirements.txt ./
RUN python3 -m pip install psutil -U
RUN mkdir -p /app/data
VOLUME ["/app/data"]

ENTRYPOINT ["python3", "memory_info.py"]
