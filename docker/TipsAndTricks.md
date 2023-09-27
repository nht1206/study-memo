# Tips And Tricks
## Use `COPY --chown` instead of `RUN chown` after `COPY` in Dockerfile
```Dockerfile
# Manually changing owner
COPY . $APP_HOME
RUN chown -r app:app $APP_HOME

# Using --chown option
COPY --chown=app:app . $APP_HOME
```
## Docker include a HEADCHECK instruction
Use `HEALTHCHECK` instructions to verify that process running inside the container healthy.
```Dockerfile
HEALTHCHECK CMD curl --fail http://localhost:8000 || exit 1
```
## Use array based CMD over string based CMD instructions

```Dockerfile
# Array (exec)
CMD ["gunicorn", "-w", "4", "-k", "uvicorn.workers.UvicornWorker", "main:app"]

# String (shell)
CMD "gunicorn -w 4 -k uvicorn.workers.UvicornWorker main:app"
```
## Pay close attention to the order of the Dockerfile commands to leverage to layer caching
```Dockerfile
# sample.py is copied before requirements.txt
# Dependencies will be installed for every change to sample.py

FROM python:3.9-slim

WORKDIR /app

COPY sample.py .

COPY requirements.txt .

RUN pip install -r /requirements.txt


# sample.py is copied after requirements.txt
# Dependencies will be installed only for changes to requirements.txt
# When there are no changes, Docker cache will be used

FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install -r /requirements.txt

COPY sample.py .
```
