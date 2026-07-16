FROM python:3.12-alpine AS builder

WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir --user -r requirements.txt

FROM python:3.12-alpine

RUN addgroup -g 10001 -S appgroup && adduser -u 10001 -S appuser -G appgroup

WORKDIR /app
COPY --from=builder /root/.local /home/appuser/.local
COPY app.py .

RUN chown -R appuser:appgroup /app

ENV PATH=/home/appuser/.local/bin:$PATH
USER 10001

EXPOSE 8080

CMD ["python", "app.py"]
