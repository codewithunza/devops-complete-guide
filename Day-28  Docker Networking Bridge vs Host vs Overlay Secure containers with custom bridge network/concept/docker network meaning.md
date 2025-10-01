Docker network is a feature that allows Docker containers to communicate with each other and with external systems. It provides a way to manage how containers connect and interact over the network. Here are the key points about Docker networks:

1. **Isolation**: Each Docker network provides isolation for containers. Containers on one network cannot communicate with containers on another network unless explicitly allowed.

2. **Types of Networks**:
   - **Bridge Network**: This is the default network type. It allows containers on the same host to communicate with each other. It acts like a private network for containers.
   - **Host Network**: In this mode, containers share the host's network stack. They can communicate directly with the host and other containers without any isolation.
   - **Overlay Network**: This type is used for multi-host networking, allowing containers running on different Docker hosts to communicate with each other. It is commonly used in Docker Swarm setups.
   - **Macvlan Network**: This allows containers to have their own MAC addresses, making them appear as physical devices on the network.

3. **Communication**: Containers can communicate with each other using their container names or IP addresses. Docker automatically manages the DNS resolution for container names.

4. **Configuration**: You can create, manage, and configure networks using Docker commands. This includes specifying network drivers, subnet configurations, and more.

In summary, Docker networks are essential for enabling communication between containers and managing how they interact with each other and the outside world.
