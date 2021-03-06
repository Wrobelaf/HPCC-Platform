##############################################################################
#
#    HPCC SYSTEMS software Copyright (C) 2020 HPCC Systems®.
#
#    Licensed under the Apache License, Version 2.0 (the "License");
#    you may not use this file except in compliance with the License.
#    You may obtain a copy of the License at
#
#       http://www.apache.org/licenses/LICENSE-2.0
#
#    Unless required by applicable law or agreed to in writing, software
#    distributed under the License is distributed on an "AS IS" BASIS,
#    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
#    See the License for the specific language governing permissions and
#    limitations under the License.
##############################################################################

# Build container image for a git commit, based on an earlier build
# For use when developing/testing the system only
# See incr.sh for how this is used

ARG PREV_LABEL
FROM hpccsystems/platform-build:${PREV_LABEL}

RUN apt-get install -y \
    dnsutils \
    nano 

RUN curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.17.0/bin/linux/amd64/kubectl && chmod +x ./kubectl && mv ./kubectl /usr/local/bin

ARG USER
WORKDIR /hpcc-dev/HPCC-Platform
RUN if ! git config remote.${USER}.url > /dev/null ; then git remote add ${USER} https://github.com/${USER}/HPCC-Platform.git ; fi

ARG COMMIT

RUN git fetch ${USER} && git checkout ${COMMIT} && git submodule update --init --recursive

WORKDIR /hpcc-dev/build
RUN make -j$(($(nproc)*3/2)) install
