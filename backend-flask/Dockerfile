FROM python:3.10-slim-buster

# Inside Container, shared kernel os
WORKDIR /backend-flask

# Outside Container
COPY requirements.txt requirements.txt

# Inside Container
RUN pip3 install -r requirements.txt

COPY . .

# Set Env variables inside the container
ENV FLASK_ENV=development

EXPOSE ${PORT}
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]