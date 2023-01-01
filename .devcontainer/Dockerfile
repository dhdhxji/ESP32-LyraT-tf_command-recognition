FROM espressif/idf:release-v4.4

ARG DEBIAN_FRONTEND=nointeractive

RUN apt-get update \
  && apt install -y -q \
  cmake \
  git \
  hwdata \
  libglib2.0-0 \
  libnuma1 \
  libpixman-1-0 \
  linux-tools-virtual \
  && rm -rf /var/lib/apt/lists/*

RUN update-alternatives --install /usr/local/bin/usbip usbip `ls /usr/lib/linux-tools/*/usbip | tail -n1` 20

# QEMU
ENV QEMU_REL=esp-develop-20220919
ENV QEMU_SHA256=f6565d3f0d1e463a63a7f81aec94cce62df662bd42fc7606de4b4418ed55f870
ENV QEMU_DIST=qemu-${QEMU_REL}.tar.bz2
ENV QEMU_URL=https://github.com/espressif/qemu/releases/download/${QEMU_REL}/${QEMU_DIST}

ENV LC_ALL=C.UTF-8
ENV LANG=C.UTF-8

RUN wget --no-verbose ${QEMU_URL} \
  && echo "${QEMU_SHA256} *${QEMU_DIST}" | sha256sum --check --strict - \
  && tar -xf $QEMU_DIST -C /opt \
  && rm ${QEMU_DIST}

ENV PATH=/opt/qemu/bin:${PATH}

ENV ADF_REL=v2.4.1
RUN git clone --recursive --depth 1 --branch $ADF_REL https://github.com/espressif/esp-adf.git /opt/esp/adf
ENV ADF_PATH=/opt/esp/adf

RUN mkdir /components
RUN cd /components && git clone https://github.com/espressif/esp-nn.git
RUN cd /components/esp-nn && git checkout b219491494325c8130ae0aecac4b11ed6bf79885

RUN cd /tmp && git clone https://github.com/espressif/tflite-micro-esp-examples.git
RUN cd /tmp/tflite-micro-esp-examples && git checkout e6e9001b45c0e1cf6a4455712a0b24bcab0b50bb
RUN cd /tmp/tflite-micro-esp-examples && cp -r components/tflite-lib /components/

RUN echo "source /opt/esp/idf/export.sh > /dev/null 2>&1" >> ~/.bashrc

ENTRYPOINT [ "/opt/esp/entrypoint.sh" ]

CMD ["/bin/bash"]