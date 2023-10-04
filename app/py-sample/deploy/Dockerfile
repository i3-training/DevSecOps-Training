#FROM python:3.6
FROM  python:3.11.5

ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1

RUN mkdir /app
WORKDIR /app
COPY requirements.txt /app/requirements.txt
RUN pip install -r requirements.txt
COPY ./ /app
EXPOSE 10007
CMD ["python", "routes.py"]