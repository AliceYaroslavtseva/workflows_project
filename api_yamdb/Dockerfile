FROM python:3.7-slim

WORKDIR /app

COPY requirements.txt /api_yamdb

RUN pip3 install -r api_yamdb/requirements.txt

COPY ./ /app

CMD ["gunicorn", "api_yamdb.wsgi:application", "--bind", "0:8000" ]