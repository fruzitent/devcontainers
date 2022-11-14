ARG VARIANT="kinetic"

FROM ubuntu:$VARIANT

RUN yes | unminimize

ARG USERNAME="user"
ARG PASSWORD="password"
ARG USER_GID=1000
ARG USER_UID=1000

RUN :                                                                       \
    && groupadd --gid $USER_GID $USERNAME                                   \
    && useradd                                                              \
        --create-home $USERNAME                                             \
        --gid $USER_GID                                                     \
        --shell /bin/bash                                                   \
        --uid $USER_UID                                                     \
    && echo "$USERNAME:$PASSWORD" | chpasswd                                \
    && mkdir -p                                    /etc/sudoers.d           \
    && echo "$USERNAME ALL=(root) NOPASSWD: ALL" > /etc/sudoers.d/$USERNAME \
    && chmod 0440                                  /etc/sudoers.d/$USERNAME

RUN :                                             \
    && apt-get update                             \
    && apt-get install -y --no-install-recommends \
        apt-utils                                 \
        coreutils                                 \
        lsb-release                               \
        software-properties-common                \
        sudo                                      \
    && apt-get autoremove -y                      \
    && apt-get clean -y                           \
    && rm -rf /var/lib/apt/lists/*

RUN :                                             \
    && apt-get update                             \
    && apt-get install -y --no-install-recommends \
        ca-certificates                           \
        curl                                      \
        git                                       \
        gnupg2                                    \
        htop                                      \
        iproute2                                  \
        less                                      \
        lsof                                      \
        ncdu                                      \
        neovim                                    \
        net-tools                                 \
        openssl                                   \
        pkg-config                                \
        procps                                    \
        psmisc                                    \
        rsync                                     \
        strace                                    \
        tree                                      \
        unzip                                     \
        wget                                      \
        zip                                       \
    && apt-get autoremove -y                      \
    && apt-get clean -y                           \
    && rm -rf /var/lib/apt/lists/*

ARG SSHD_PORT=22

RUN :                                             \
    && apt-get update                             \
    && apt-get install -y --no-install-recommends \
        openssh-client                            \
        openssh-server                            \
    && apt-get autoremove -y                      \
    && apt-get clean -y                           \
    && rm -rf /var/lib/apt/lists/*

RUN :                                                                  \
    && mkdir -p /var/run/sshd                                          \
    && echo "session optional pam_loginuid.so" >> /etc/pam.d/sshd      \
    && echo "PermitRootLogin yes"              >> /etc/ssh/sshd_config \
    && echo "Port            $SSHD_PORT"       >> /etc/ssh/sshd_config \
    && echo "UsePAM          yes"              >> /etc/ssh/sshd_config

RUN :                                             \
    && apt-get update                             \
    && apt-get install -y --no-install-recommends \
        locales                                   \
    && apt-get autoremove -y                      \
    && apt-get clean -y                           \
    && rm -rf /var/lib/apt/lists/*

ENV LANG="en_US.UTF-8"
ENV LANGUAGE="en_US.UTF-8"
ENV LC_ALL="C.UTF-8"

RUN :                                        \
    && echo "$LANG UTF-8" >> /etc/locale.gen \
    && locale-gen

USER $USERNAME

ARG FONTS_URL="https://github.com/ryanoasis/nerd-fonts/releases/latest/download/Meslo.zip"

RUN :                                                  \
    && sudo apt-get update                             \
    && sudo apt-get install -y --no-install-recommends \
        fontconfig                                     \
    && sudo apt-get autoremove -y                      \
    && sudo apt-get clean -y                           \
    && sudo rm -rf /var/lib/apt/lists/*

RUN :                                                                        \
    && curl -L $FONTS_URL -o              /home/$USERNAME/fonts.zip          \
    && mkdir -p                           /home/$USERNAME/.local/share/fonts \
    && unzip /home/$USERNAME/fonts.zip -d /home/$USERNAME/.local/share/fonts \
    && rm    /home/$USERNAME/fonts.zip                                       \
    && fc-cache -fv

RUN :                                                  \
    && sudo apt-get update                             \
    && sudo apt-get install -y --no-install-recommends \
        exa                                            \
        fish                                           \
        fzf                                            \
    && sudo apt-get autoremove -y                      \
    && sudo apt-get clean -y                           \
    && sudo rm -rf /var/lib/apt/lists/*                \
    && sudo chsh $USERNAME -s $(which fish)

SHELL ["fish", "-c"]

ARG FISHER_URL="https://git.io/fisher"

RUN :                                                  \
    && curl -L $FISHER_URL | source                    \
    && fisher install "edc/bass"                       \
    && fisher install "gazorby/fish-abbreviation-tips" \
    && fisher install "jethrokuan/fzf"                 \
    && fisher install "jethrokuan/z"                   \
    && fisher install "jorgebucaran/fisher"            \
    && fisher install "joseluisq/gitnow"               \
    && fisher install "laughedelic/pisces"             \
    && fisher install "markcial/upto"                  \
    && fisher install "meaningful-ooo/sponge"          \
    && fisher install "nickeb96/puffer-fish"

ARG STARSHIP_URL="https://starship.rs/install.sh"

RUN :                                                                                        \
    && sh -c "$(curl -L $STARSHIP_URL)" -s --yes                                             \
    && mkdir -p                              /home/$USERNAME/.config                         \
    && starship preset pastel-powerline    > /home/$USERNAME/.config/starship.toml           \
    && mkdir -p                              /home/$USERNAME/.config/fish/conf.d             \
    && echo "starship init fish | source" >> /home/$USERNAME/.config/fish/conf.d/config.fish

ARG PYTHON_VER="3.11"

RUN :                                                                                                 \
    && sudo apt-get update                                                                            \
    && sudo apt-get install -y --no-install-recommends                                                \
        python$PYTHON_VER                                                                             \
        python$PYTHON_VER-venv                                                                        \
    && sudo apt-get autoremove -y                                                                     \
    && sudo apt-get clean -y                                                                          \
    && sudo rm -rf /var/lib/apt/lists/*                                                               \
    && sudo update-alternatives --install /usr/local/bin/python3 python3 $(which python$PYTHON_VER) 1

ARG PIP_URL="https://bootstrap.pypa.io/get-pip.py"

RUN :                                           \
    && mkdir -p      /home/$USERNAME/.local/bin \
    && fish_add_path /home/$USERNAME/.local/bin \
    && curl -L $PIP_URL | python3 -             \
    && python3 -m pip install --upgrade --user  \
        pip                                     \
        setuptools                              \
        virtualenv                              \
        wheel

ARG POETRY_URL="https://install.python-poetry.org"

RUN :                                                                                 \
    && curl -L $POETRY_URL | python3 -                                                \
    && mkdir -p                  /home/$USERNAME/.config/fish/completions             \
    && poetry completions fish > /home/$USERNAME/.config/fish/completions/poetry.fish

RUN :                                                  \
    && sudo apt-get update                             \
    && sudo apt-get install -y --no-install-recommends \
        ccache                                         \
        cmake                                          \
        cmake-curses-gui                               \
        cppcheck                                       \
        make                                           \
        ninja-build                                    \
    && sudo apt-get autoremove -y                      \
    && sudo apt-get clean -y                           \
    && sudo rm -rf /var/lib/apt/lists/*

ARG GCC_VER="12"

RUN :                                                                                    \
    && sudo apt-get update                                                               \
    && sudo apt-get install -y --no-install-recommends                                   \
        g++-$GCC_VER                                                                     \
        gcc-$GCC_VER                                                                     \
        gdb                                                                              \
    && sudo apt-get autoremove -y                                                        \
    && sudo apt-get clean -y                                                             \
    && sudo rm -rf /var/lib/apt/lists/*                                                  \
    && sudo update-alternatives --install /usr/local/bin/g++ g++ $(which g++-$GCC_VER) 1 \
    && sudo update-alternatives --install /usr/local/bin/gcc gcc $(which gcc-$GCC_VER) 1

ARG LLVM_URL="https://apt.llvm.org/llvm.sh"
ARG LLVM_VER="15"

RUN :                                                                                                                                                                                                                                                                                    \
    && curl -L $LLVM_URL | sudo bash -s $LLVM_VER all                                                                                                                                                                                                                                    \
    && echo "update-alternatives --install /usr/local/bin/clang       clang       /usr/bin/clang-$LLVM_VER       1 $(find -L "/usr/lib/llvm-$LLVM_VER/bin" -type f -not -name "clang"       -not -name "llvm*" -printf "--slave /usr/local/bin/%f %f /usr/bin/%f-$LLVM_VER ")" | sudo sh \
    && echo "update-alternatives --install /usr/local/bin/llvm-config llvm-config /usr/bin/llvm-config-$LLVM_VER 1 $(find -L "/usr/lib/llvm-$LLVM_VER/bin" -type f -not -name "llvm-config"      -name "llvm*" -printf "--slave /usr/local/bin/%f %f /usr/bin/%f-$LLVM_VER ")" | sudo sh

RUN :                                \
    && python3 -m pip install --user \
        cpplint                      \
        cmakelang

RUN :                                                      \
    && sudo mkdir -p                            /workspaces \
    && sudo chown -R $USERNAME:$USERNAME        /workspaces \
    && git config --global --add safe.directory /workspaces

WORKDIR /workspaces

CMD sudo service ssh start -D
