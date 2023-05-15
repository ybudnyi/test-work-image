FROM python:3.9-alpine as base

ENV USER=app
ENV GROUP=app
ENV UID=1001

COPY ./requirements.txt /tmp/requirements.txt
RUN pip3 install -r /tmp/requirements.txt
COPY ./main.py /app/main.py

EXPOSE 9090

# FROM base
# COPY --from=aquasec/trivy:latest /usr/local/bin/trivy /usr/local/bin/trivy
# RUN trivy rootfs /

RUN addgroup -g $UID $GROUP && adduser --disabled-password --ingroup $GROUP --uid $UID $USER
RUN chmod +x /app/main.py \
    && chown -R $USER:$GROUP /app/
    
USER $USER
CMD ["python3", "/app/main.py"]