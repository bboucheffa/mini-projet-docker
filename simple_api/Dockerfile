FROM python:3.11-slim
LABEL maintainer="Badis Boucheffa"
COPY requirements.txt student_age.py /
RUN apt update -y && apt install -y gcc make python3-dev libsasl2-dev libldap2-dev libssl-dev
RUN pip3 install --break-system-packages -r /requirements.txt
VOLUME [ "/data" ]
EXPOSE 5000
CMD ["python3", "./student_age.py"]

