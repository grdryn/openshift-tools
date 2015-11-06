FROM centos:latest

RUN echo "root:redhat" | chpasswd

# creature comforts (make it feel like a normal linux environment)
ENV LANG en_US.UTF-8
ENV CONTAINER docker
ENV USER root
ENV HOME /root
ENV TERM xterm
WORKDIR /root
ADD bashrc /root/.bashrc

# Make working in Python nice
ADD pythonrc /root/.pythonrc
ENV PYTHONSTARTUP=/root/.pythonrc

RUN yum clean metadata && \
    yum install -y epel-release && \
    yum -y install cronie ansible screen vim procps less ack tar psmisc && \
    yum -y update && \
    yum clean all

# Python requires the following packages in order to work with SSL and SNI
RUN yum -y install python-pyasn1 python-ndg_httpsclient pyOpenSSL &&  \
    yum clean all

# Setup locales.  It comes broken (probably a size issue) by default in the docker image.
RUN yum -y reinstall glibc-common &&  \
    yum clean all  && \
    mv -f /usr/lib/locale/locale-archive /usr/lib/locale/locale-archive.tmpl  && \
    /usr/sbin/build-locale-archive

# Setup Cron
ADD pamd.crond /etc/pam.d/crond

# Setup Screen
RUN chmod 777 /var/run/screen

# Setup Ansible
ADD ansible.cfg /etc/ansible/ansible.cfg
RUN echo -e '[local]\nlocalhost       ansible_connection=local\n' > /etc/ansible/hosts

# Make the container work more consistently in and out of openshift
# BE CAREFUL!!! If you change these, you may bloat the image! Use 'docker history' to see the size!
RUN chmod -R g+rwX /root/
