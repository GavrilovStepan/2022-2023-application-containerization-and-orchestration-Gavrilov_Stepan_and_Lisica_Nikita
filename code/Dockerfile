FROM ubuntu:latest
MAINTAINER Lisicsa Nikita and Gavrilov Stepan
RUN apt-get update -qy
RUN apt-get install -qy python3.11 python3-pip python3.11-dev
COPY . /app
WORKDIR /app
RUN pip install -r requirements.txt
EXPOSE 5000
CMD ["python3","app.py","0.0.0.0:5000"]