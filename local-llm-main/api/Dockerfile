FROM python:3.8-buster AS chat

WORKDIR /App

RUN adduser --home ${APP_PATH} --system --group linux_user
ENV PATH=$APP_PATH/.local/bin:$PATH
RUN mkdir .local .cache \
    && chown linux_user:linux_user .local \
    && chown linux_user:linux_user .cache
USER linux_user
WORKDIR ${APP_PATH}
COPY --chown=linux_user:linux_user . ${APP_PATH}
RUN python -m pip install --upgrade pip \
    && python -m pip install --upgrade setuptools \
    && python -m pip install --upgrade wheel

RUN --mount=type=cache,mode=0755,target=/root/.cache/pip pip install -r requirements.txt

CMD ["python", "app.py"]