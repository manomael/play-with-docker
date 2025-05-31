# play-with-docker

Play With Docker provides a free, in-browser Alpine Linux Virtual Machine, allowing you to build and run Docker containers and create clusters using Docker Swarm Mode. Under the hood, it utilizes DIND (Docker-in-Docker) to simulate multiple VMs/PCs.

A live version is available at: [http://play-with-docker.com/](http://play-with-docker.com/)

## Features

- Free Alpine Linux Virtual Machine in the cloud
- Build and run Docker containers
- Create clusters with Docker Swarm Mode
- DIND (Docker-in-Docker) for multiple VMs/PCs effect

## Table of Contents

- [Requirements](#requirements)
- [Development](#development)
  - [Port Forwarding](#port-forwarding)
  - [Building the DIND Image Myself](#building-the-dind-image-myself)
- [FAQ](#faq)
- [Contributing](#contributing)
- [License](#license)

## Requirements

Docker 1.13+ is required.

The Docker daemon must be running in Swarm Mode because PWD utilizes overlay attachable networks. To enable Swarm Mode, run `docker swarm init` on the destination daemon.

It is also necessary to manually load the IPVS kernel module, as the daemon will not load it automatically when swarms are created in DIND. Run the following command:
```bash
sudo modprobe xt_ipvs
```

## Development

To get started with development:

1. Ensure the Docker daemon is running on your machine.
2. Pull the DIND image: `docker pull franela/dind`.
3. Install Go 1.7.1+ (e.g., using `brew` on macOS or your system's package manager).
4. Install [dep](https://github.com/golang/dep) for dependency management and run `dep ensure` to download dependencies.
5. Start PWD as a container using `docker-compose up`.
6. Open [http://localhost](http://localhost) in your browser and click "New Instance".

**Notes:**

*   There is a hard-coded limit of 5 Docker playgrounds per session. Sessions are deleted after 4 hours.
*   To override the DIND version or image, set the `DIND_IMAGE` environment variable (e.g., `DIND_IMAGE=franela/docker<version>-rc:dind`). Note that only [franela](https://hub.docker.com/r/franela/) DIND images are compatible, not standard `dind` images.

### Port Forwarding

For port forwarding to work correctly during development, `*.localhost` must resolve to `127.0.0.1`. This ensures that when you access a URL like `pwd10-0-0-1-8080.host1.localhost`, you are correctly forwarded to your local PWD server.

You can achieve this by setting up a `dnsmasq` server (which can also run in a Docker container) with the following configuration:

```
address=/localhost/127.0.0.1
```

Remember to configure your computer's default DNS to use the dnsmasq server for resolution.

### Building the DIND Image Myself

If you need to modify the DIND image:

1. Make your changes to the `Dockerfile.dind` file.
2. Build the image using the command:
   ```bash
   docker build --build-arg docker_storage_driver=vfs -f Dockerfile.dind -t franela/dind .
   ```

## FAQ

### How can I connect to a published port from the outside world?

To access your services from outside, use the following URL pattern: `http://ip<hyphen-ip>-<session_id>-<port>.direct.labs.play-with-docker.com` (e.g., `http://ip-2-135-3-b8ir6vbg5vr00095iil0-8080.direct.labs.play-with-docker.com`).

### Why is PWD running on ports 80 and 443? Can I change that?

No, PWD must run on these ports for DNS resolution to work correctly. We welcome ideas or suggestions on how to improve this.

## Contributing

We welcome contributions to Play With Docker! If you'd like to contribute, please follow these general guidelines:

1.  Fork the repository.
2.  Create a new branch for your changes.
3.  Make your changes, ensuring they are well-tested and documented.
4.  Submit a pull request for review.

If you find any bugs or have feature requests, please open an issue on our [GitHub Issues page](https://github.com/play-with-docker/play-with-docker/issues).

## License

This project is licensed under the Apache License 2.0. See the [LICENSE](LICENSE) file for details.
