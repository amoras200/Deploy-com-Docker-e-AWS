FROM python:3.9-slim
COPY . /usr/share/nginx/html
EXPOSE 80
CMD ["python", "-m", "http.server", "80", "--directory", "/usr/share/nginx/html"]
