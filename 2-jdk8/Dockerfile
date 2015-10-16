FROM localhost:5000/java:jdk8

# Configuration variables.
ENV CROWD_HOME     /var/local/atlassian/crowd
ENV CROWD_INSTALL  /usr/local/atlassian/crowd
ENV CROWD_VERSION  2.8.3

# Install Atlassian Crows
# directory structure.
RUN set -x \
    && apt-get update --quiet \
    && apt-get install --quiet --yes --no-install-recommends libtcnative-1 xmlstarlet curl \
    && apt-get clean \
    && mkdir -p                "${CROWD_HOME}" \
    && chmod -R 700            "${CROWD_HOME}" \
    && chown -R daemon:daemon  "${CROWD_HOME}" \
    && mkdir -p                "${CROWD_INSTALL}/conf/Catalina" \
    && curl -Ls                "https://www.atlassian.com/software/crowd/downloads/binary/atlassian-crowd-${CROWD_VERSION}.tar.gz" | tar -xz --directory "${CROWD_INSTALL}" --strip-components=1 --no-same-owner \
    && curl -Ls                "https://jdbc.postgresql.org/download/postgresql-9.4-1204.jdbc42.jar" -o "${CROWD_INSTALL}/lib/postgresql-9.4-1204.jdbc42.jar" 

RUN chmod -R 700            "${CROWD_INSTALL}/conf" \
    && chmod -R 700            "${CROWD_INSTALL}/logs" \
    && chmod -R 700            "${CROWD_INSTALL}/temp" \
    && chmod -R 700            "${CROWD_INSTALL}/work" \
    && chown -R daemon:daemon  "${CROWD_INSTALL}/conf" \
    && chown -R daemon:daemon  "${CROWD_INSTALL}/logs"

RUN chown -R daemon:daemon  "${CROWD_INSTALL}/temp" \
    && chown -R daemon:daemon  "${CROWD_INSTALL}/work" \
    && echo -e                 "\ncrowd.home=$CROWD_HOME" >> "${CROWD_INSTALL}/atlassian-crowd/WEB-INF/classes/crowd-application.properties"

# Use the default unprivileged account. This could be considered bad practice
# on systems where multiple processes end up being executed by 'daemon' but
# here we only ever run one process anyway.
USER daemon:daemon

# Expose default HTTP connector port.
EXPOSE 8080

# Set volume mount points for installation and home directory. Changes to the
# home directory needs to be persisted as well as parts of the installation
# directory due to eg. logs.
VOLUME ["/var/local/atlassian/crowd"]

# Set the default working directory as the installation directory.
WORKDIR ${CROWD_HOME}

# copy db config file into place
COPY dbconfig.xml ${CROWD_HOME}/dbconfig.xml

# Run Atlassian CROWD as a foreground process by default.
CMD ["/usr/local/atlassian/crowd/bin/start-crowd.sh", "-fg"]
