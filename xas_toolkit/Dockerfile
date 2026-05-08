# using UV to build env with xas_toolkit installed

FROM ghcr.io/astral-sh/uv:bookworm AS builder

ENV UV_COMPILE_BYTECODE=1 UV_LINK_MODE=copy
ENV UV_PYTHON_INSTALL_DIR=/python
ENV UV_PYTHON_PREFERENCE=only-managed

WORKDIR /app

RUN git clone -b b18_autoprocessing https://github.com/DiamondLightSource/xas_toolbox.git

RUN uv venv --python 3.11

RUN cd xas_toolbox && uv pip install .

RUN rm -r xas_toolbox

FROM debian:bookworm

COPY --from=builder /python .python

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
