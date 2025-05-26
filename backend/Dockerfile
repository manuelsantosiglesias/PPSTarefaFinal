FROM debian:bullseye

# Instalar Python 3 y pip
RUN apt-get update && apt-get install -y python3 python3-pip 

WORKDIR /app

# Copiamos só requirements.txt
COPY requirements.txt .

# Instalamos requirements
RUN pip3 install --no-cache-dir -r requirements.txt

# Variable de entorno
ENV FLASK_APP=app.py

# Arrancar flask
CMD ["flask", "run", "--host=0.0.0.0", "--port=5000"]

