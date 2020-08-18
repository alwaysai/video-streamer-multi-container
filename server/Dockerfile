FROM balenalib/amd64-debian-python:3.6.9-stretch-run
WORKDIR /app
COPY requirements.txt requirements.txt
RUN pip3 install -r requirements.txt
COPY . ./
CMD ["python", "-u", "app.py"]
