# Use Fedora as the base image
FROM fedora:latest

# Install necessary packages
RUN dnf install -y \
    procps-ng \                # Provides 'ps' command
    net-tools \                # Networking tools (e.g., ifconfig, netstat)
    openssh-server \           # SSH server
    && dnf clean all           # Clean up package manager cache

# Configure SSH server
RUN mkdir -p /run/sshd \
    && ssh-keygen -A          # Generate host keys

# Set the root password (change this for production use)
RUN echo "root:password" | chpasswd

# Expose SSH port
EXPOSE 22

# Run the SSH server process
CMD ["/usr/sbin/sshd", "-D"]

