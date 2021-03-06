FROM centos:7.7.1908

# Variable for ansible user
ARG WORKUSER=ansible
ARG USER_UID=1001
ARG USER_GID=$USER_UID

# Install ssh server, sudo vim
RUN yum update -y \
    && yum install -y openssh-server sudo vim \
    && yum clean all

# change root password
RUN echo 'root:p@ssw0rd' | chpasswd \
    # Add group & user for ansible user
    && groupadd --gid $USER_GID $WORKUSER \
    && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $WORKUSER \
    # Set sudo for ansible user
    && echo $WORKUSER ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$WORKUSER \
    && chmod 0440 /etc/sudoers.d/$WORKUSER

# Copy SSH key
COPY ./ansible_rsa.pub /root/.ssh/authorized_keys
COPY ./ansible_rsa.pub /home/$WORKUSER/.ssh/authorized_keys

# Setup ansible user permission
RUN chown root:root -R /root/.ssh \
    && chown $WORKUSER:$WORKUSER -R /home/$WORKUSER/.ssh \
    && chmod 500 /root/.ssh \
    && chmod 500 /home/$WORKUSER/.ssh \
    && chmod 400 /root/.ssh/* \
    && chmod 400 /home/$WORKUSER/.ssh/* \
    && mkdir /var/run/sshd \
    # Setup sshd
    && sed -ri 's/^#PermitRootLogin yes/PermitRootLogin yes/' /etc/ssh/sshd_config \
    && sed -ri 's/^#PermitEmptyPasswords no/PermitEmptyPasswords yes/' /etc/ssh/sshd_config \
    && sed -ri 's/^PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config \
    && sed -ri 's/HostKey/#HostKey/' /etc/ssh/sshd_config \
    && ssh-keygen -t rsa -b 4096 -N "" -f /etc/ssh/ssh_host_rsa_key \
    && echo -e "\n\
    alias ls='ls -GF'\n\
    alias ll='ls -GFlrt'\n\
    " >> /home/$WORKUSER/.bashrc

EXPOSE 22

# Run sshd
CMD ["/usr/sbin/sshd", "-D"]