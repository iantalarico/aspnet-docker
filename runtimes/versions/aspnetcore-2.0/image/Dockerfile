# Copyright 2018 Google Inc. All rights reserved.

# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at

#     http://www.apache.org/licenses/LICENSE-2.0

# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

FROM gcr.io/gcp-runtimes/ubuntu_16_0_4

# Install .NET Core dependencies
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        libc6 \
        libcurl3 \
        libgcc1 \
        libicu55 \
        liblttng-ust0 \
        libssl1.0.0 \
        libstdc++6 \
        libtinfo5 \
        libunwind8 \
        libuuid1 \
        zlib1g \
        ca-certificates \
        curl \
    && apt-get clean

# Install the package.
RUN mkdir -p /usr/share/dotnet && \
    curl -sL https://storage.googleapis.com/gcp-aspnetcore-packages/dotnet-sdk-2.1.105-linux-x64.tar.gz | tar -xz -C /usr/share/dotnet/ && \
    ln -s /usr/share/dotnet/dotnet /usr/bin/dotnet
    
RUN mkdir -p /usr/share/dotnet-debugger && \
    curl -sL https://storage.googleapis.com/dotnet-debugger/package-7925264.tar.gz | tar -xz -C /usr/share/dotnet-debugger/
  
RUN echo $'start_command= \n\
while (( "$#" )); do \n\
  start_command="$start_command $1" \n\
  shift \n\
done \n\
$start_command & \n\
APP_PID=$(echo $! \n\
/usr/share/dotnet-debugger/agent/Google.Cloud.Diagnostics.Debug --application-id=$APP_PID \n\
' > /usr/share/dotnet-debugger/start-debugger.sh

RUN chmod 777 /usr/share/dotnet-debugger/start-debugger.sh

# Define the environment variables
ENV PORT=8080
ENV ASPNETCORE_URLS=http://*:${PORT}
ENV LD_LIBRARY_PATH=/usr/share/dotnet-debugger/debugger
ENV STACKDRIVER_DEBUGGER_DEBUGGER=/usr/share/dotnet-debugger/debugger/google_cloud_debugger

# Expose the port for the app.
EXPOSE $PORT
