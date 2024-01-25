FROM ghcr.io/arosbio/[SERVER-TYPE]:latest

LABEL org.opencontainers.image.source=[URL TO YOUR GITHUB REPO]
LABEL org.opencontainers.image.description=[OPTIONAL - DESCRIPTION OF YOUR MODEL]
LABEL org.opencontainers.image.url=[URL TO YOUR GITHUB REPO]
LABEL org.opencontainers.image.version=[OPTIONAL]
LABEL org.opencontainers.image.authors=[OPTIONAL - FILL IN NAMES]
LABEL org.opencontainers.image.title=[OPTIONAL - DESCRIPTIVE TITLE]

# Copy the model itself to the image
COPY [NAME OF YOUR MODEL FILE] /var/lib/jetty/model.jar

# Change to root user to create a new user and change directory ownership
USER root

ENV USER=jetty2
ENV HOME=/home/$USER

# Create the user 1000
RUN useradd -m -u 1000 $USER

# Set working directory (this is where the code should go)
WORKDIR $HOME/app
# Copy the required startup script 
COPY start-script.sh $HOME/app/start-script.sh
# Make startup script runnable and change user
RUN chmod +x start-script.sh \
    && chown -R $USER:$USER $HOME \
    && chown -R $USER:$USER /var/lib/jetty/ \
    && chown -R $USER:$USER /usr/local/jetty \
    && rm -rf /var/lib/apt/lists/*

# Set the required user
USER $USER

# Set the required entry start-script 
ENTRYPOINT ["./start-script.sh"]