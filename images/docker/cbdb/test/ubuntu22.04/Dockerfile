# --------------------------------------------------------------------
#
# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements. See the NOTICE file distributed
# with this work for additional information regarding copyright
# ownership. The ASF licenses this file to You under the Apache
# License, Version 2.0 (the "License"); you may not use this file
# except in compliance with the License. You may obtain a copy of the
# License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or
# implied. See the License for the specific language governing
# permissions and limitations under the License.
#
# --------------------------------------------------------------------
#
# Apache Cloudberry (incubating) is an effort undergoing incubation at
# the Apache Software Foundation (ASF), sponsored by the Apache
# Incubator PMC.
#
# Incubation is required of all newly accepted projects until a
# further review indicates that the infrastructure, communications,
# and decision making process have stabilized in a manner consistent
# with other successful ASF projects.
#
# While incubation status is not necessarily a reflection of the
# completeness or stability of the code, it does indicate that the
# project has yet to be fully endorsed by the ASF.
#
# --------------------------------------------------------------------
# Dockerfile for Apache Cloudberry Base Environment
# --------------------------------------------------------------------
# This Dockerfile sets up a Ubuntu jammy 22.04 -based container to serve as
# a base environment for evaluating the Apache Cloudberry. It installs
# necessary system utilities, configures the environment for SSH access,
# and sets up a 'gpadmin' user with sudo privileges. The Apache
# Cloudberry DEB can be installed into this container for testing and
# functional verification.
#
# Key Features:
# - Locale setup for en_US.UTF-8
# - SSH daemon setup for remote access
# - Essential system utilities installation
# - Separate user creation and configuration steps
#
# Security Considerations:
# - This Dockerfile prioritizes ease of use for functional testing and
#   evaluation. It includes configurations such as passwordless sudo access
#   for the 'gpadmin' user and SSH access with password authentication.
# - These configurations are suitable for testing and development but
#   should NOT be used in a production environment due to potential security
#   risks.
#
# Usage:
#   docker build -t cloudberry-db-base-env .
#   docker run -h cdw -it cloudberry-db-base-env
# --------------------------------------------------------------------

FROM ubuntu:jammy

ARG accessKeyId
ARG secretAccessKey
ARG bucketName

# Argument for configuring the timezone
ARG TIMEZONE_VAR="Europe/London"


SHELL ["/bin/bash", "-o", "pipefail", "-c"]
ENV DEBIAN_FRONTEND=noninteractive

RUN stat -fc %T /sys/fs/cgroup/

RUN ln -snf /usr/share/zoneinfo/Europe/London /etc/localtime && echo $TIMEZONE_VAR > /etc/timezone

# --------------------------------------------------------------------
# Install Development Tools and Utilities
# --------------------------------------------------------------------
RUN apt-get update -o Acquire::AllowInsecureRepositories=true && apt-get install -y --no-install-recommends --allow-unauthenticated \
        file \
        gdb \
        glibc-locale-source \
        make \
        openssh \
        openssh-clients \
        openssh-server \
        procps-ng \
        sudo \
        which

RUN apt-get install -y locales && \
    locale-gen "en_US.UTF-8" && \
    update-locale LC_ALL="en_US.UTF-8"


# --------------------------------------------------------------------
# Copy Configuration Files and Setup the Environment
# --------------------------------------------------------------------

RUN ln -s /usr/bin/python2.7 /usr/bin/python

COPY ./configs/* /tmp/

RUN echo ${TIMEZONE_VAR} > /etc/timezone && \
    chmod 777 /tmp/init_system.sh && \
    groupadd gpadmin && \
    useradd -rm -d /home/gpadmin -s /bin/bash -g root -G sudo -u 1001 gpadmin && \
    echo 'gpadmin ALL=(ALL) NOPASSWD:ALL' > /etc/sudoers && \
    locale-gen "en_US.UTF-8" && \
    update-locale LC_ALL="en_US.UTF-8"

USER gpadmin
WORKDIR /home/gpadmin

CMD ["bash","-c","/tmp/init_system.sh"]
