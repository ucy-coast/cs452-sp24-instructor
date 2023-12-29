# A collection of common build dependencies used for installing various modules
FROM buildpack-deps:bullseye

# Versions of Nginx and nginx-rtmp-module to use
ENV NGINX_VERSION nginx-1.23.2
ENV NGINX_RTMP_MODULE_VERSION 1.2.2

# Install dependencies
RUN apt-get update && \
    apt-get install -y ca-certificates openssl libssl-dev && \
    rm -rf /var/lib/apt/lists/*

# Download and decompress Nginx
# TODO: Execute the following commands in a single line
# mkdir -p /tmp/build/nginx && \
# cd /tmp/build/nginx && \
# wget -O ${NGINX_VERSION}.tar.gz https://nginx.org/download/${NGINX_VERSION}.tar.gz && \
# tar -zxf ${NGINX_VERSION}.tar.gz

# Download and decompress RTMP module
# TODO: Execute the following commands in a single line
# mkdir -p /tmp/build/nginx-rtmp-module && \
# cd /tmp/build/nginx-rtmp-module && \
# wget -O nginx-rtmp-module-${NGINX_RTMP_MODULE_VERSION}.tar.gz https://github.com/arut/nginx-rtmp-module/archive/v${NGINX_RTMP_MODULE_VERSION}.tar.gz && \
# tar -zxf nginx-rtmp-module-${NGINX_RTMP_MODULE_VERSION}.tar.gz && \
# cd nginx-rtmp-module-${NGINX_RTMP_MODULE_VERSION}

# Build and install Nginx
# The default puts everything under /usr/local/nginx, so it's needed to change
# it explicitly. Not just for order but to have it in the PATH
# TODO: Execute the following commands in a single line
# cd /tmp/build/nginx/${NGINX_VERSION} && \
# ./configure \
#     --sbin-path=/usr/local/sbin/nginx \
#     --conf-path=/etc/nginx/nginx.conf \
#     --error-log-path=/var/log/nginx/error.log \
#     --pid-path=/var/run/nginx/nginx.pid \
#     --lock-path=/var/lock/nginx/nginx.lock \
#     --http-log-path=/var/log/nginx/access.log \
#     --http-client-body-temp-path=/tmp/nginx-client-body \
#     --with-http_ssl_module \
#     --with-threads \
#     --with-ipv6 \
#     --add-module=/tmp/build/nginx-rtmp-module/nginx-rtmp-module-${NGINX_RTMP_MODULE_VERSION} --with-debug && \
# make -j $(getconf _NPROCESSORS_ONLN) && \
# make install && \
# mkdir /var/lock/nginx && \
# rm -rf /tmp/build

# Forward logs to Docker
# TODO: Execute the following commands in a single line
# ln -sf /dev/stdout /var/log/nginx/access.log && \
# ln -sf /dev/stderr /var/log/nginx/error.log

# Set up config file
# TODO: Copy configuration file nginx.conf to /etc/nginx/nginx.conf

# Expose network ports 
# TODO: Expose network ports 80 and 1935 

# Execute command
# TODO: Execute command: nginx -g daemon off;