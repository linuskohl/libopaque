FROM python:3.9-slim

ENV PYTHONUNBUFFERED 1
ENV LD_LIBRARY_PATH $LD_LIBRARY_PATH:/usr/local/lib

EXPOSE 8000
WORKDIR /code

RUN apt-get update &&\
    apt-get install -y --no-install-recommends libsodium-dev git gcc pkgconf build-essential &&\
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* &&\
    mkdir /tmp/libraries && cd /tmp/libraries &&\
    git clone https://github.com/stef/liboprf && cd liboprf && make && PREFIX="/usr" make install && cd .. &&\
    git clone https://github.com/stef/libopaque && cd libopaque/src && git submodule update --init --recursive --remote && make && PREFIX="/usr" make install 
COPY ./requirements.txt ./
RUN pip install --no-cache-dir --upgrade -r /code/requirements.txt
RUN pip install gunicorn
RUN mkdir -p /code/static /code/templates
COPY app.py /code

COPY ./static /code/static/
COPY ./templates /code/templates/

CMD ["gunicorn"  , "-b", "0.0.0.0:8000", "--workers", "1", "--threads", "1", "app:app"]
