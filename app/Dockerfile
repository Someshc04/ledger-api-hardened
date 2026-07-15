FROM python:3.12-slim AS builder

WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir --user -r requirements.txt

FROM python:3.12-slim

RUN groupadd -r appgroup && useradd -r -g appgroup -u 10001 appuser

WORKDIR /app
COPY --from=builder /root/.local /home/appuser/.local
COPY app.py .

RUN chown -R appuser:appgroup /app

ENV PATH=/home/appuser/.local/bin:$PATH
USER 10001

EXPOSE 8080

CMD ["python", "app.py"]
