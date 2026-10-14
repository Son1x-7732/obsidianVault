## 2. Install Docker

```
sudo pacman -S docker
```

This installs:

- Docker Engine
- Docker CLI
- `containerd`

## 3. Enable and start the Docker service

```
sudo systemctl enable docker.service
sudo systemctl start docker.service
```

Or do both at once:

```
sudo systemctl enable --now docker.service
```

## 4. Verify Docker is running

Check the service status:

```
systemctl status docker
```

Or:

```
sudo docker version
```

## 5. Allow your user to run Docker without `sudo`

Create (or use) the `docker` group and add yourself:

```
sudo usermod -aG docker $USER
```

Then either:

- Log out and log back in, or
- Run:

```
newgrp docker
```

Verify:

```
docker run hello-world
```

You should now be able to run Docker without `sudo`.

## 6. Test Docker

Run the official test image:

```
docker run hello-world
```

You should see:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

## 7. Useful Docker commands

List images:

```
docker images
```

List running containers:

```
docker ps
```

List all containers:

```
docker ps -a
```

Pull an image:

```
docker pull ubuntu
```

Run Ubuntu interactively:

```
docker run -it ubuntu bash
```

Stop a container:

```
docker stop <container_id>
```

Remove a container:

```
docker rm <container_id>
```

Remove an image:

```
docker rmi <image_name>
```

## 8. (Optional) Install Docker Compose

Recent Docker versions include the Compose plugin.

Install it:

```
sudo pacman -S docker-compose
```

Or, if the Compose plugin package is available on your system:

```
sudo pacman -S docker-compose
```

Check:

```
docker compose version
```

Notice the command is **`docker compose`** (with a space), not necessarily `docker-compose`.

## 9. Common issues

### Permission denied

If you get:

```
permission denied while trying to connect to the Docker daemon socket
```

Make sure:

```
groups
```

includes `docker`.

If not, log out and back in after running:

```
sudo usermod -aG docker $USER
```

### Docker service isn't running

```
sudo systemctl status docker
```

Start it:

```
sudo systemctl start docker
```

Enable it on boot:

```
sudo systemctl enable docker
```

### Check Docker info

```
docker info
```

This prints details about the daemon, storage driver, networking, and available resources.