FROM python:3.15.2-slim-bookworm@sha256:96599df822d0e1c6bb700014a05c7f3c8cabc6e0f7ff3c4f9f69cee033b4b5d9
ENV PYTHONDONTWRITEBYTECODE=1 PYTHONUNBUFFERED=1
WORKDIR /app
RUN apt-get update && apt-get install -y --no-install-recommends libpcre2-8-0 perl-base \
    && dpkg --compare-versions "$(dpkg-query -W -f='${Version}' libpcre2-8-0)" ge 10.42-1+deb12u2 \
    && dpkg --compare-versions "$(dpkg-query -W -f='${Version}' perl-base)" ge 5.36.0-7+deb12u4 \
    && rm -rf /var/lib/apt/lists/*
COPY bots/requirements.lock /tmp/requirements.lock
RUN pip install --no-cache-dir -r /tmp/requirements.lock
COPY packages/calendar /app/packages/calendar
COPY packages/contracts /app/packages/contracts
COPY bots /app/bots
RUN pip install --no-cache-dir --no-deps ./packages/calendar ./packages/contracts ./bots \
    && useradd --create-home --uid 10001 portal
RUN python -m pip check \
    && python -c "from portal_bots.runtime import BotRuntime" \
    && python -m pip uninstall -y setuptools pip \
    && rm -rf /usr/local/lib/python3.13/ensurepip
USER portal
LABEL org.opencontainers.image.source="https://github.com/stupidprogrammer4/rick-and-morty"
CMD ["python", "-m", "portal_bots"]
