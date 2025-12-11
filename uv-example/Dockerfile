FROM ghcr.io/astral-sh/uv:bookworm AS builder

ENV UV_COMPILE_BYTECODE=1 UV_LINK_MODE=copy
ENV UV_PYTHON_INSTALL_DIR=/python
ENV UV_PYTHON_PREFERENCE=only-managed

WORKDIR /app

COPY pylock.toml pylock.toml

RUN uv venv

RUN uv pip sync pylock.toml

FROM debian:bookworm-slim

COPY --from=builder /python /python

COPY --from=builder /app /app

ENV PATH="/app/.venv/bin:$PATH"

WORKDIR /app

COPY . .

RUN if [ -f test.py ]; \
    then echo "#!/bin/bash\npython test.py" >> run.sh; \
    else echo "#!/bin/bash\npython --version" >> run.sh; \ 
    fi; \ 
    chmod +x run.sh;

CMD ["./run.sh"]