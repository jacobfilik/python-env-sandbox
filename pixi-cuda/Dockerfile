FROM ghcr.io/prefix-dev/pixi:noble-cuda-13.0.0 AS build

WORKDIR /app
COPY . .
RUN pixi install
RUN pixi shell-hook -s bash > /shell-hook
RUN echo "#!/bin/bash" > /app/entrypoint.sh
RUN cat /shell-hook >> /app/entrypoint.sh
RUN echo 'exec "$@"' >> /app/entrypoint.sh

FROM nvidia/cuda:13.0.0-base-ubuntu24.04 AS production
WORKDIR /app
COPY --from=build /app/.pixi/envs/default /app/.pixi/envs/default
COPY --from=build --chmod=0755 /app/entrypoint.sh /app/entrypoint.sh

RUN if [ -f test.py ]; \
    then echo "#!/bin/bash\npython test.py" >> run.sh; \
    else echo "#!/bin/bash\npython --version" >> run.sh; \ 
    fi; \ 
    chmod +x run.sh;


ENTRYPOINT [ "/app/entrypoint.sh" ]
CMD ["./run.sh"]

# EXPOSE 8000
# ENTRYPOINT [ "/app/entrypoint.sh" ]
# CMD [ "uvicorn", "my_project:app", "--host", "0.0.0.0" ]