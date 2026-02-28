FROM python:3.10

WORKDIR /app

COPY . .

RUN pip install --no-cache-dir -requirements.txt

EXPOSE 5000

CMD ["python", "app.py"]