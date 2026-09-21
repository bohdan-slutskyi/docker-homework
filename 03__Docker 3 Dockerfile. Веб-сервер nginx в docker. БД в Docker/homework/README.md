# Docker 3

В этой папке находятся файлы домашнего задания:

- `Dockerfile`;
- `memory_info.py`;
- `requirements.txt`;
- `data/output.txt`.

Команды выполнять из этой папки:

```bash
mkdir -p data
docker build -t docker-homework-lesson3 .
docker run --rm \
  --name docker-homework-lesson3-run \
  -v "$(pwd)/data:/app/data" \
  docker-homework-lesson3
cat data/output.txt
```
