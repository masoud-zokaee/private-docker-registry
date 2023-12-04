# Private Docker Registry

Building up a private docker registry with a web UI, using docker.

## Project Description 📝

The repository includes a docker-compose.yml file for running and configuring a private Docker registry, along with a web-based user interface for monitoring and interacting with it.

## Files Description 🗂

- docker-compose.yml: A Docker Compose file containing two services with their corresponding environment variables and volumes.
- config.yml: A configuration file for the registry service that enables basic settings, including the required access permissions for the web interface service.

## Prerequisites and Run 📦

**Required steps to deploy and run the registry**

### Step-by-step commands to generate a self-signed SSL key with DNS:

1. Generate a private key by running the following command:

   ```
   openssl genrsa -out registry.key 2048
   ```
   This will generate a 2048-bit RSA private key and save it in a file named "registry.key"

2. Next, generate a certificate signing request (CSR) using the private key:

   ```
   openssl req -new -key registry.key -out registry.csr
   ```
   Follow the prompts and provide the requested information, such as your organization details and the DNS name (e.g. https://dockerregistry.example.com) for which you want to generate the SSL key.

3. Finally, generate a self-signed certificate using the private key and CSR:

   ```
   openssl x509 -req -days 365 -in registry.csr -signkey registry.key -out registry.crt
   ```
   This will create a self-signed certificate named "registry.crt", valid for 365 days.

4. Move the two generated files, namely registry.key and registry.csr, to the registry_certs directory to ensure a proper configuration for the registry service.

### Allow docker registry from client docker engine:

If you are employing a self-signed SSL key (for experimental purposes), it is essential to grant the client Docker engine (whether on your computer or any other machine communicating with the registry) access to the registry.

Open and edit:

```
sudo nano /etc/docker/daemon.json
```

Add the following JSON:

```
{
   "insecure-registries": ["https://dockerregistry.example.com"],
   "registry-mirrors": ["https://dockerregistry.example.com"]
}
```

Finally:

```
sudo systemctl restart docker.service docker.socket
```

### Allow docker registry from browser:

Again If you are using a self-signed SSL key, make sure to grant access to the docker registry from your browser to properly utilize the web interface.


### Wrapping up:

Run the command **docker compose up -d** to pull the images and run the services.

---

📌 **Note**: When you delete an image through the web UI, only the reference is removed, leaving the content intact. To completely remove the dangling images, execute the registry's garbage collector using the following command:

```
docker exec { registry container id or name } registry garbage-collect /etc/docker/registry/config.yml -m
```