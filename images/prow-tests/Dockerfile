# Copyright 2018 The Knative Authors
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

FROM gcr.io/k8s-testimages/kubekins-e2e:v20190103-edc7696cc-master
LABEL maintainer "Adriano Cunha <adrcunha@google.com>"

# Install extras on top of base image

ENV DEBIAN_FRONTEND noninteractive
RUN apt-get update
RUN gcloud components update

# Docker
RUN gcloud components install docker-credential-gcr
RUN docker-credential-gcr configure-docker

# Extra tools through apt-get
RUN apt-get install -y uuid-runtime  # for uuidgen
RUN apt-get install -y rubygems  # for mdl

# Update go version to 1.12rc1
RUN go get golang.org/dl/go1.12rc1
RUN go1.12rc1 download
RUN cp $(which go1.12rc1) $(which go)

# Extra tools through go get
RUN go get -u github.com/google/go-containerregistry/cmd/ko
RUN go get -u github.com/golang/dep/cmd/dep
RUN go get -u github.com/google/licenseclassifier
RUN go get -u github.com/jstemmer/go-junit-report
RUN go get -u github.com/raviqqe/liche

# Extra tools through gem
RUN gem install mixlib-config -v 2.2.4  # required because ruby is 2.1
RUN gem install mdl

# Temporarily add test-infra to the image to build custom tools
ADD . /go/src/github.com/knative/test-infra

# Build custom tools in the container
RUN make -C /go/src/github.com/knative/test-infra/tools/githubhelper
RUN cp /go/src/github.com/knative/test-infra/tools/githubhelper/githubhelper .
RUN go install github.com/knative/test-infra/tools/dep-collector

# Remove test-infra from the container
RUN rm -fr /go/src/github.com/knative/test-infra
