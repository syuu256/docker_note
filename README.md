
  sudo vim Dockerfile

    FROM centos
    MAINTAINER vagrant
    RUN yum install -y passwd sudo openssh-server  
    RUN useradd docker; passwd -f -u docker;
    RUN mkdir -p /home/docker/.ssh; chown docker /home/docker/.ssh; chmod 700 /home/docker/.ssh  
    RUN echo "ssh-rsa " >> /home/docker/.ssh/authorized_keys  
    RUN chown docker /home/docker/.ssh/authorized_keys;  chmod 600 /home/docker/.ssh/authorized_keys
    RUN echo "docker    ALL=(ALL)       ALL" >> /etc/sudoers.d/docker
    RUN sed -ri 's/#PermitRootLogin yes/PermitRootLogin yes/g' /etc/ssh/sshd_config
    RUN sed -ri 's/UsePAM yes/#UsePAM yes/g' /etc/ssh/sshd_config
    RUN sed -ri 's/#UsePAM no/UsePAM no/g' /etc/ssh/sshd_config
    RUN /etc/init.d/sshd start


====

    sudo yum install docker
    sudo service docker start
    sudo docker images | awk '{print $3}' | xargs docker rmi
    sudo docker build -t centos:build .
    sudo docker images
    sudo docker run -i -t -d -p 22 centos:build /usr/sbin/sshd -D
    sudo docker ps #ポート確認
    ssh -l docker -p ${PORTS} -i /home/vagrant/.ssh/id_rsa localhost
