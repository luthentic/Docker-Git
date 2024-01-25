# syntax=docker/dockerfile:1.4

# First Stage (Builder):
# Multi Platform: will be determined during the build process 
FROM --platform=$BUILDPLATFORM node:17.0.1-bullseye-slim as builder 

# Creates a directory named "project" in the container.
RUN mkdir /project

# Sets the working directory for subsequent commands to "/project".
WORKDIR /project

# Installs Angular CLI globally within the container.
RUN npm install -g @angular/cli@13

# Copies the package.json and package-lock.json files from the local directory to the container's "/project" directory.
COPY package.json package-lock.json ./

# Installs project dependencies using npm's CI (Continuous Integration) mode to ensure deterministic dependency resolution.
RUN npm ci

# Copies the entire local directory to the container's "/project" directory.
COPY . .

#Specifies the default command to run when the container starts
#which is to serve the Angular application using ng serve on all available network interfaces.
CMD ["ng", "serve", "--host", "0.0.0.0"]


# Second Stage (Dev Environments):
FROM builder as dev-envs

# Updates the package lists for repositories configured in the system
# Installs the "git" package using the package manager "apt-get".
RUN <<EOF
apt-get update
apt-get install -y --no-install-recommends git
EOF

# Creates a new user named "vscode" and creates a home directory for the user
# Creates a new group named "docker"
# Adds the user "vscode" to the "docker" group
RUN <<EOF
useradd -s /bin/bash -m vscode
groupadd docker
usermod -aG docker vscode
EOF


# install Docker tools (cli, buildx, compose)
COPY --from=gloursdocker/docker / /

# as the "builder" stage
CMD ["ng", "serve", "--host", "0.0.0.0"]