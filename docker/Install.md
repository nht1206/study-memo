# Installing Docker on Ubuntu
- Remove old version of Docker

    ```sh
    sudo apt-get remove docker docker-engine docker.io containerd runc
    ```
- Install Docker Engine

    ```sh
    sudo apt-get install \
        ca-certificates \
        curl \
        gnupg \
        lsb-release
    ```
- Set up repository for Docker installation

    ```sh
    sudo mkdir -p /etc/apt/keyrings
    ```
    ```sh
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    ```
    ```sh
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    ```
- Install Docker Engine

    ```sh
    sudo apt-get update
    ```
    ```sh
    sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
    ```
- Add the current user to `docker` group allows that user to run docker commands

    ```sh
    sudo groupadd docker
    ```
    ```sh
    sudo usermod -aG docker $USER
    ```
