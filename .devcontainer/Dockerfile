# Verwende das offizielle Ubuntu-Basisimage
FROM ubuntu:22.04

# Setze eine nicht-interaktive Umgebung für die Installation
ENV DEBIAN_FRONTEND=noninteractive

# Aktualisiere die Paketliste und installiere die Abhängigkeiten
RUN apt-get update && apt-get install -y \
    sudo \
    git \
    wget \
    flex \
    bison \
    gperf \
    python3 \
    python3-pip \
    python3-venv \
    cmake \
    ninja-build \
    ccache \
    libffi-dev \
    libssl-dev \
    dfu-util \
    libusb-1.0-0 \
    zsh \
    curl \
    locales \
    udev \
    keyboard-configuration

# Bereinige den Paket-Cache, um den Container klein zu halten
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Stelle sicher, dass python3 als Standard-Python verwendet wird
RUN update-alternatives --install /usr/bin/python python /usr/bin/python3 1

# Installiere ESP-IDF für alle "targets"
ENV IDF_TOOLS_PATH "/esp/.espressif"
RUN mkdir -p /esp && cd /esp && git clone --recursive https://github.com/espressif/esp-idf.git
RUN /esp/esp-idf/install.sh all

# Setze die IDF_PATH Umgebungsvariable
ENV IDF_PATH /esp/esp-idf/

# Installiere Oh My Zsh
RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" || true

# Setze Zsh als Standardshell für den Containerbenutzer
RUN chsh -s $(which zsh)

# Füge den ESP-IDF Export-Befehl zu .zshrc hinzu
RUN echo "source /esp/esp-idf/export.sh" >> ~/.zshrc

# Konfiguriere Locale für Deutsch (Deutschland)
RUN locale-gen de_DE.UTF-8
ENV LANG de_DE.UTF-8
ENV LANGUAGE de_DE:de
ENV LC_ALL de_DE.UTF-8

# Setze die Standard-Tastaturbelegung auf Deutsch
RUN echo "keyboard 'de'" | sudo tee /etc/default/keyboard > /dev/null

# Definiere den Standardarbeitsordner
WORKDIR /workspace

# Starte den Container in Zsh
CMD ["zsh"]
