ARG BASE_IMAGE=php:8.2-rc-fpm-alpine3.19
FROM ${BASE_IMAGE}

ARG XLSWRITER_VERSION=1.5.5

LABEL description="构建php-fpm运行环境"

RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/g' /etc/apk/repositories 
RUN apk update --no-cache && apk add --no-cache libzip-dev zip 
RUN apk add --no-cache tzdata
RUN apk add --no-cache --virtual .phpize-deps $PHPIZE_DEPS 
RUN apk add --no-cache autoconf gcc g++ make pkgconfig libtool nasm 
RUN apk add --no-cache libjpeg libjpeg-turbo libjpeg-turbo-dev 
RUN apk add --no-cache libpng-dev 
RUN apk add --no-cache libmcrypt-dev 
RUN apk add --no-cache freetype-dev 
RUN apk add --no-cache imagemagick 
RUN apk add --no-cache imagemagick-dev 
RUN apk add --no-cache php-dom 
RUN pecl install -o -f imagick 
RUN pecl install -o -f igbinary 
RUN pecl install -o -f redis 
RUN docker-php-ext-enable redis imagick igbinary 
RUN docker-php-ext-install bcmath 
RUN docker-php-ext-install zip  
RUN docker-php-ext-install pcntl 
RUN docker-php-ext-install pdo_mysql 
RUN docker-php-ext-install opcache 
RUN docker-php-ext-configure gd --with-freetype --with-jpeg 
RUN docker-php-ext-install gd 
RUN curl -fsSL "https://pecl.php.net/get/xlswriter-${XLSWRITER_VERSION}.tgz" -o xlswriter.tgz \
    && mkdir -p /tmp/xlswriter \
    && tar -xf xlswriter.tgz -C /tmp/xlswriter --strip-components=1 \
    && rm xlswriter.tgz \
    && cd /tmp/xlswriter \
    && phpize && ./configure --enable-reader && make && make install \
    && echo 'extension=xlswriter'>/usr/local/etc/php/conf.d/docker-php-ext-xlswriter.ini 
RUN rm -rf /usr/src/* && rm -rf /tmp/*


FROM ${BASE_IMAGE}
ENV TZ=Asia/Shanghai
COPY --from=0 /usr/local/lib/php/extensions/no-debug-non-zts-20220829 /usr/local/lib/php/extensions/no-debug-non-zts-20220829
COPY --from=0 /usr/local/etc/php/conf.d /usr/local/etc/php/conf.d
COPY --from=0 /usr/lib/libstdc++.so.6 /usr/lib/libzip.so.5 /usr/lib/libbz2.so.1 /usr/lib/libzstd.so.1 /usr/lib/libpng16.so.16 /usr/lib/libjpeg.so.8 /usr/lib/libfreetype.so.6 /usr/lib/libgomp.so.1 /usr/lib/libMagickWand-7.Q16HDRI.so.10 /usr/lib/libMagickCore-7.Q16HDRI.so.10 /usr/lib/libX11.so.6 /usr/lib/liblcms2.so.2 /usr/lib/libfontconfig.so.1 /usr/lib/libXext.so.6 /usr/lib/libltdl.so.7 /usr/lib/libxcb.so.1 /usr/lib/libexpat.so.1 /usr/lib/libXau.so.6 /usr/lib/libXdmcp.so.6 /usr/lib/
COPY --from=0 /usr/lib/php82/modules /usr/lib/php82/modules
COPY --from=0 /etc/php82/conf.d /etc/php82/conf.d
COPY --from=0 /usr/local/include/php/ext /usr/local/include/php/ext
COPY --from=0 /etc/ImageMagick-7 /etc/ImageMagick-7
COPY --from=0 /usr/include/ImageMagick-7 /usr/include/ImageMagick-7
COPY --from=0 /usr/local/lib/php/doc/imagick /usr/local/lib/php/doc/imagick
COPY --from=0 /usr/lib/ImageMagick-7.1.1 /usr/lib/ImageMagick-7.1.1
COPY --from=0 /usr/lib/libMagickWand-7.Q16HDRI.so.10.0.1 /usr/lib/libMagick++-7.Q16HDRI.so /usr/lib/libMagickCore-7.Q16HDRI.so.10.0.1 /usr/lib/libMagickCore-7.Q16HDRI.so /usr/lib/libMagickWand-7.Q16HDRI.so /usr/lib/libMagick++-7.Q16HDRI.so.5.0.0 /usr/lib/libMagickWand-7.Q16HDRI.so.10 /usr/lib/libMagick++-7.Q16HDRI.so.5 /usr/lib/
COPY --from=0 /usr/lib/pkgconfig/MagickCore.pc /usr/lib/pkgconfig/MagickWand-7.Q16HDRI.pc /usr/lib/pkgconfig/ImageMagick-7.Q16HDRI.pc /usr/lib/pkgconfig/ImageMagick.pc /usr/lib/pkgconfig/MagickWand.pc /usr/lib/pkgconfig/Magick++-7.Q16HDRI.pc /usr/lib/pkgconfig/Magick++.pc /usr/lib/pkgconfig/MagickCore-7.Q16HDRI.pc /usr/lib/pkgconfig/
COPY --from=0 /usr/share/ImageMagick-7 /usr/share/ImageMagick-7/
COPY --from=0 /usr/bin/magick /usr/bin/magick-script /usr/bin/MagickWand-config /usr/bin/MagickCore-config /usr/bin/Magick++-config /usr/bin/
COPY --from=0 /usr/share/zoneinfo/Asia/Shanghai /usr/share/zoneinfo/Asia/Shanghai

RUN echo "Asia/Shanghai">/etc/timezone && chmod -R 755 /usr/local/lib/php/extensions/no-debug-non-zts-20220829 && rm -rf /usr/src/* && rm -rf /tmp/*
