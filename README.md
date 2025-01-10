# docker vdev

This is intended to be pulled and built so you don't need to install 5000 dependancies on your computer and have it get broken when a system updates.

The following will be built:

- Python3.11
- Uv for better python package manager
- Ansible
- Kubectl for k3s/k8s management
- Network tools: dns lookup, mtr, nmap, traceroute
- Linux system tools: wget, curl, git

```bash
git clone
cd vdev
docker build -t devenv .
```

Wait for the build to complete:

```bash
# bryanlurer @ m4-mbp in ~/dev/vdev on git:main x [6:22:33] C:1
$ docker build -t devenv .
[+] Building 77.9s (12/12) FINISHED                                                                docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                               0.0s
 => => transferring dockerfile: 1.64kB                                                                             0.0s
 => [internal] load metadata for docker.io/library/ubuntu:22.04                                                    0.4s
 => [internal] load .dockerignore                                                                                  0.0s
 => => transferring context: 2B                                                                                    0.0s
 => CACHED [1/8] FROM docker.io/library/ubuntu:22.04@sha256:0e5e4a57c2499249aafc3b40fcd541e9a456aab7296681a3994d6  0.0s
 => => resolve docker.io/library/ubuntu:22.04@sha256:0e5e4a57c2499249aafc3b40fcd541e9a456aab7296681a3994d63158720  0.0s
 => [2/8] RUN apt-get update && apt-get install -y     python3.11     python3.11-venv     python3.11-distutils    40.7s
 => [3/8] RUN curl -LsSf https://astral.sh/uv/install.sh | sh                                                      1.7s 
 => [4/8] RUN python3 -m pip install --no-cache-dir ansible                                                       10.6s 
 => [5/8] RUN curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | gpg --dearmor -o /etc/apt/keyr  7.8s 
 => [6/8] WORKDIR /workspace                                                                                       0.0s 
 => [7/8] RUN python3.11 -m venv /opt/venv                                                                         1.2s 
 => [8/8] RUN echo "source /opt/venv/bin/activate" >> ~/.bashrc                                                    0.1s 
 => exporting to image                                                                                            15.2s 
 => => exporting layers                                                                                            9.8s 
 => => exporting manifest sha256:0ab3a34f228cd95b1dc740a84d76ac3629bad50f71761ddf4545eabdd4646849                  0.0s 
 => => exporting config sha256:f67fc50dc033fd51bdad24495eef3d76ac603dbeb37335b45ee4cbb4ba7272eb                    0.0s
 => => exporting attestation manifest sha256:8ae3791964fff34aab8d283a98807a775c81e13f74d0176c3c251099735b5b45      0.0s
 => => exporting manifest list sha256:ba6e1e8916fe871386c1793dadd6b0274d1504209f816a0d9dfc189f467b70ed             0.0s
 => => naming to docker.io/library/devenv:latest                                                                   0.0s
 => => unpacking to docker.io/library/devenv:latest          
 ```

## Simplest way

docker run -it devenv

## Full development setup

docker run -it \
  -v "$(pwd):/workspace" \          # Mount current directory
  -v "${HOME}/.kube:/root/.kube" \  # For kubectl config
  --net=host \                      # For networking tools
  --name devenv \                   # Give it a name
  devenv

## Step by step

```bash
# 1. Clone your repo with the Dockerfile
git clone https://github.com/blurer/vdev.git
cd vdev

# 2. Build the container image
docker build -t devenv .

# 3. Go to their actual project directory
cd ~/projects/your-project/

# 4. Run the dev container with their current directory mounted
docker run -it \
  -v "$(pwd):/workspace" \
  -v "${HOME}/.kube:/root/.kube" \ #if you need k3s/k8s
  --net=host \
  --name devenv \
  devenv
```