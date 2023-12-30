FROM ubuntu:22.04

LABEL maintainer="@k33g"

# Set environment variables
ARG GO_ARCH=${GO_ARCH}
ARG GO_VERSION=${GO_VERSION}
ARG TINYGO_ARCH=${TINYGO_ARCH}
ARG TINYGO_VERSION=${TINYGO_VERSION}

ARG USER_NAME=${USER_NAME}

ARG DEBIAN_FRONTEND=noninteractive

ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US.UTF-8
ENV LC_COLLATE=C
ENV LC_CTYPE=en_US.UTF-8

# ------------------------------------
# Install Tools
# ------------------------------------
RUN <<EOF
apt-get update 
apt-get install -y curl wget git sudo gopls delve pkg-config 
apt-get install -y build-essential xz-utils software-properties-common
#apt-get install -y sshpass gettext openssh-server libssl-dev
apt-get install -y nano bat exa
ln -s /usr/bin/batcat /usr/bin/bat
add-apt-repository ppa:maveonair/helix-editor
apt update
apt install helix
EOF

# ------------------------------------
# Install Go
# ------------------------------------
RUN <<EOF
wget https://golang.org/dl/go${GO_VERSION}.linux-${GO_ARCH}.tar.gz
tar -xvf go${GO_VERSION}.linux-${GO_ARCH}.tar.gz
mv go /usr/local
rm go${GO_VERSION}.linux-${GO_ARCH}.tar.gz
EOF

# ------------------------------------
# Set Environment Variables for Go
# ------------------------------------
ENV PATH="/usr/local/go/bin:${PATH}"
ENV GOPATH="/home/${USER_NAME}/go"
ENV GOROOT="/usr/local/go"

RUN <<EOF
go version
go install -v golang.org/x/tools/gopls@latest
go install -v github.com/ramya-rao-a/go-outline@latest
go install -v github.com/stamblerre/gocode@v1.0.0
EOF

# ------------------------------------
# Install TinyGo
# ------------------------------------
RUN <<EOF
wget https://github.com/tinygo-org/tinygo/releases/download/v${TINYGO_VERSION}/tinygo_${TINYGO_VERSION}_${TINYGO_ARCH}.deb
dpkg -i tinygo_${TINYGO_VERSION}_${TINYGO_ARCH}.deb
rm tinygo_${TINYGO_VERSION}_${TINYGO_ARCH}.deb
EOF

# ------------------------------------
# Install Wasmedge
# ------------------------------------
RUN <<EOF
curl -sSf https://raw.githubusercontent.com/WasmEdge/WasmEdge/master/utils/install.sh | bash -s -- -p /usr/local
EOF

# ------------------------------------
# Create a new user
# ------------------------------------
# Create new regular user `${USER_NAME}` and disable password and gecos for later
# --gecos explained well here: https://askubuntu.com/a/1195288/635348
RUN adduser --disabled-password --gecos '' ${USER_NAME}

#  Add new user `${USER_NAME}` to sudo group
RUN adduser ${USER_NAME} sudo

# Ensure sudo group users are not asked for a password when using 
# sudo command by ammending sudoers file
RUN echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

# Set the working directory
WORKDIR /home/${USER_NAME}

# Set the user as the owner of the working directory
RUN chown -R ${USER_NAME}:${USER_NAME} /home/${USER_NAME}

# Switch to the regular user
USER ${USER_NAME}

# Avoid the message about sudo
RUN touch ~/.sudo_as_admin_successful

# ------------------------------------
# Install Rust + Wasm Toolchain
# ------------------------------------
RUN <<EOF
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
export RUSTUP_HOME=~/.rustup
export CARGO_HOME=~/.cargo
export PATH=$PATH:$CARGO_HOME/bin
curl https://rustwasm.github.io/wasm-pack/installer/init.sh -sSf | sh
rustup target add wasm32-wasi
EOF

ENV PATH="/home/${USER_NAME}/.cargo/bin:$PATH"

# ------------------------------------
# Install OhMyBash
# ------------------------------------
RUN <<EOF
bash -c "$(curl -fsSL https://raw.githubusercontent.com/ohmybash/oh-my-bash/master/tools/install.sh)"
EOF

CMD ["/bin/bash"]
