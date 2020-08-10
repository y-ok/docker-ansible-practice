FROM python:3.8.5-slim-buster

# install ssh, ansible
RUN apt-get update && apt-get install -y ssh vim git

WORKDIR /tmp

# remove chardet first!
# install python libs for use aws api, ansible
COPY ./requirements.txt /tmp/
RUN if [ -f "requirements.txt" ]; then pip install -r requirements.txt && rm requirements.txt*; fi \
    # make work dir
    && mkdir /workspace

# add ansible hosts
ADD ./config/ansible_hosts /etc/ansible/hosts

# add ansible config to user dir
ADD ./config/ansible.cfg /root/.ansible.cfg

# copy ssh keys
ADD ./ssh/ansible_rsa /root/.ssh/
ADD ./ssh/ansible_rsa.pub /root/.ssh/
RUN chmod 600 /root/.ssh
RUN chmod 400 /root/.ssh/*

# setup /root/.bashrc
RUN sed -i "s/^# export LS_OPTIONS='--color=auto'/export LS_OPTIONS='--color=auto'/" /root/.bashrc \
    && sed -i "s/^# alias ls='ls \$LS_OPTIONS'/alias ls='ls \$LS_OPTIONS -GF'/" /root/.bashrc \
    && sed -i "s/^# alias ll='ls \$LS_OPTIONS -l'/alias ll='ls \$LS_OPTIONS -GFlrt'/" /root/.bashrc \
    && sed -i "s/^# alias l='ls \$LS_OPTIONS -lA'/alias l='ls \$LS_OPTIONS -GFAlrt'/" /root/.bashrc