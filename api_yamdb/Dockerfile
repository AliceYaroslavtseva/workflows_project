FROM python:3.7-slim
 
WORKDIR /app

RUN mkdir /app

COPY /api_yamdb/requirements.txt /app

RUN pip3 install -r /api_yamdb/requirements.txt --no-cache-dir

COPY . /app

CMD ["gunicorn", "api_yamdb.wsgi:application", "--bind", "0:8000" ]
