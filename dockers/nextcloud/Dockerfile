FROM nextcloud:27.1.6-fpm

RUN apt update && apt install -y libbz2-dev \
    && docker-php-ext-configure bz2 --with-bz2 \
    && docker-php-ext-install -j$(nproc) bz2 \
    && docker-php-ext-configure sysvsem --enable-sysvsem \
    && docker-php-ext-install -j$(nproc) sysvsem \
    && apt purge -y libbz2-dev \
    && apt autoremove -y \
    && rm -rf /var/lib/apt/lists/*

RUN apt update && apt install -y libopenblas0 liblapack3 cmake libopenblas-dev liblapack-dev \
    && mkdir -p dlib \
    && curl https://codeload.github.com/davisking/dlib/tar.gz/refs/tags/v19.24 | tar zxvf - -C dlib --strip-components=1 \
    && ( \
        cd dlib/dlib \
        && cmake . -B build -DBUILD_SHARED_LIBS=ON -DCMAKE_INSTALL_PREFIX=/usr/local \
        && cmake --build build --config Release -- -j$(nproc) \
        && cmake --build build --config Release --target install \
    ) \
    && rm -rf dlib \
    && apt purge -y cmake libopenblas-dev liblapack-dev \
    && apt autoremove -y \
    && rm -rf /var/lib/apt/lists/*

RUN mkdir pdlib \
    && curl https://codeload.github.com/goodspb/pdlib/tar.gz/refs/tags/v1.0.2 | tar zxvf - -C pdlib --strip-components=1 \
    && ( \
        cd pdlib \
        && phpize \
        && ./configure \
        && make -j$(nproc) CXXFLAGS="-g -O2 -DDLIB_GUI_WIDGETs_ -DDLIB_RENDER_FACE_DeTECTIONS_H_" \
        && make install \
    ) \
    && rm -rf pdlib \
    && docker-php-ext-enable pdlib

RUN apt update && apt install -y ffmpeg \
    && rm -rf /var/lib/apt/lists/*
