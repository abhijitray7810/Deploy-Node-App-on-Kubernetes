FROM centos:7

# Install necessary packages
RUN yum install -y openssh-server openssh-clients sudo curl && \
    yum clean all

# Install Node.js and npm
RUN curl -sL https://rpm.nodesource.com/setup_14.x | bash - && \
    yum install -y nodejs && \
    yum clean all

# Configure SSH
RUN ssh-keygen -A && \
    mkdir -p /var/run/sshd && \
    echo 'root:password' | chpasswd && \
    sed -i 's/#PermitRootLogin yes/PermitRootLogin yes/' /etc/ssh/sshd_config && \
    sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config

# Create app directory
WORKDIR /app

# Copy package files
COPY package*.json ./

# Install app dependencies
RUN npm install

# Copy app source code
COPY . .

# Expose the application port and SSH port
EXPOSE 8080 22

# Create startup script
RUN echo '#!/bin/bash' > /start.sh && \
    echo '/usr/sbin/sshd' >> /start.sh && \
    echo 'npm start' >> /start.sh && \
    chmod +x /start.sh

# Start SSH and the Node.js application
CMD ["/start.sh"]
