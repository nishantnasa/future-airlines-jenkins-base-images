FROM 762508870528.dkr.ecr.ap-southeast-2.amazonaws.com/future-airlines-jenkins-base-images/jenkins-slave-base

USER root
WORKDIR /opt

# ensure local python is preferred over distribution python
ENV PATH /usr/local/bin:$PATH

# http://bugs.python.org/issue19846
# > At the moment, setting "LANG=C" on a Linux system *fundamentally breaks Python 3*, and that's not OK.
ENV LANG C.UTF-8

# https://github.com/docker-library/python/issues/147
ENV PYTHONIOENCODING UTF-8

#ENV GPG_KEY C01E1CAD5EA2C4F0B8E3571504C367C218ADD4FF
ENV PYTHON_VERSION 2.7.16

# if this is called "PIP_VERSION", pip explodes with "ValueError: invalid truth value '<VERSION>'"
ENV PYTHON_PIP_VERSION 19.0.3

# extra dependencies (over what buildpack-deps already includes)
RUN apt-get update \
  && apt-get install -y \
    build-essential \
    libreadline-gplv2-dev \
    libncursesw5-dev \
    libssl-dev \
    libsqlite3-dev \
    tk-dev \
    libgdbm-dev \
    libc6-dev \
    libbz2-dev \
    zlib1g-dev \
  && rm -rf /var/lib/apt/lists/*

RUN wget -O python.tgz "https://www.python.org/ftp/python/${PYTHON_VERSION}/Python-${PYTHON_VERSION}.tgz" \
  && mkdir -p /usr/src/python \
  && tar xzf python.tgz -C /usr/src/python/ --strip-components=1 \
  && rm python.tgz \
  \
  && cd /usr/src/python \
  && gnuArch="$(dpkg-architecture --query DEB_BUILD_GNU_TYPE)" \
  && ./configure \
    --build="$gnuArch" \
    --enable-shared \
    --enable-unicode=ucs4 \
    --with-zlib-dir=/usr/local/lib \
  && make -j "$(nproc)" \
  && make install \
  && ldconfig \
  \
  && find /usr/local -depth \
    \( \
      \( -type d -a \( -name test -o -name tests \) \) \
      -o \
      \( -type f -a \( -name '*.pyc' -o -name '*.pyo' \) \) \
    \) -exec rm -rf '{}' + \
  && rm -rf /usr/src/python \
  \
  && python2 --version

RUN set -ex; \
  \
  wget -O get-pip.py 'https://bootstrap.pypa.io/get-pip.py'; \
  \
  python get-pip.py \
    --disable-pip-version-check \
    --no-cache-dir \
    "pip==$PYTHON_PIP_VERSION" \
  ; \
  pip --version; \
  \
  find /usr/local -depth \
    \( \
      \( -type d -a \( -name test -o -name tests \) \) \
      -o \
      \( -type f -a \( -name '*.pyc' -o -name '*.pyo' \) \) \
    \) -exec rm -rf '{}' +; \
  rm -f get-pip.py

COPY requirements.txt /opt/

# install "virtualenv", since the vast majority of users of this image will want it
RUN pip install --no-cache-dir -r /opt/requirements.txt

USER jenkins
WORKDIR /opt/build
