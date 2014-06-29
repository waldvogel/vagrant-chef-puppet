FROM centos
MAINTAINER waldvogel "waldvogel@liip.ch"

RUN yum -y install openssh-server sudo openssh-clients wget

# create vagrant user
RUN useradd --create-home -s /bin/bash vagrant
RUN echo -n 'vagrant:vagrant' | chpasswd

# allow password-less sudo for vagrant user
RUN echo 'vagrant ALL = NOPASSWD: ALL' > /etc/sudoers.d/vagrant
RUN chmod 440 /etc/sudoers.d/vagrant
RUN sed -i -e 's/Defaults.*requiretty/#&/' /etc/sudoers

# setup the vagrant ssh-key
RUN su -c 'ssh-keygen -q -N "" -t rsa -f ~/.ssh/id_rsa' vagrant
RUN su -c 'cat > /home/vagrant/.ssh/authorized_keys <<<"ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEA6NF8iallvQVp22WDkTkyrtvp9eWW6A8YVr+kz4TjGYe7gHzIw+niNltGEFHzD8+v1I2YJ6oXevct1YeS0o9HZyN1Q9qgCgzUFtdOKLv6IedplqoPkcmF0aYet2PkEDo3MlTBckFXPITAMzF8dJSIFo9D8HfdOV0IAdx4O7PtixWKn5y2hMNG0zQPyUecp4pzC6kivAIhyfHilFR61RGL+GPXQ2MWZWFYbAGjyiYJnAmCP3NOTd0jMZEnDkbUvxhMmBYSdETk1rRgm+R4LOzFUGaHqHDLKLX+FIPKcF96hrucXzcWyLbIbEgE98OHlnVYCzRdK8jlqm8tehUc9c9WhQ== vagrant insecure public key"' vagrant
RUN chmod 600 /home/vagrant/.ssh/authorized_keys

# puppet
## downgrade needed to fix dependency issue
RUN yum -y downgrade libselinux-2.0.94-5.3.el6_4.1 libselinux-utils
RUN yum -y localinstall https://yum.puppetlabs.com/el/6/products/x86_64/puppetlabs-release-6-7.noarch.rpm
RUN yum -y install puppet

# chef
RUN wget -O - https://www.opscode.com/chef/install.sh | bash

# sshd
## generate the ssh system keys
RUN service sshd start
## disable PAM because it fails with SSH in the container
RUN sed -i -e 's/\(UsePAM \)yes/\1 no/' /etc/ssh/sshd_config
## fix delay when connecting with ssh
RUN sed -i -e 's/\(GSSAPIAuthentication \)yes/\1 no/' /etc/ssh/sshd_config
## run the deamon logging to stdout (-e)
CMD ["/usr/sbin/sshd", "-D", "-e"]

# open port 22 for easy ssh access
EXPOSE 22
