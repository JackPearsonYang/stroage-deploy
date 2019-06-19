FROM ubuntu:xenial

# 更新数据源
WORKDIR /etc/apt
RUN echo 'deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse' > sources.list
RUN echo 'deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse' >> sources.list
RUN echo 'deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse' >> sources.list
RUN echo 'deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse' >> sources.list
RUN apt-get update

# 安装依赖
RUN apt-get install make gcc libpcre3-dev zlib1g-dev --assume-yes

# 复制工具包
ADD fastdfs-master.tar.gz /usr/local/src
ADD fastdfs-nginx-module-master.tar.gz /usr/local/src
ADD libfastcommon.tar.gz /usr/local/src
ADD nginx-1.15.1.tar.gz /usr/local/src

# 安装 libfastcommon
WORKDIR /usr/local/src/libfastcommon-master
RUN ./make.sh && ./make.sh install

# 安装 FastDFS
WORKDIR /usr/local/src/fastdfs-master
RUN ./make.sh && ./make.sh install





# 配置 FastDFS 存储
ADD storage.conf /etc/fdfs
RUN mkdir -p /fastdfs/storage

# 配置 FastDFS 客户端
ADD client.conf /etc/fdfs

# 配置 fastdfs-nginx-module
ADD config /usr/local/src/fastdfs-nginx-module-master/src

# FastDFS 与 Nginx 集成
WORKDIR /usr/local/src/nginx-1.15.1
RUN ./configure --add-module=/usr/local/src/fastdfs-nginx-module-master/src
RUN make && make install
ADD mod_fastdfs.conf /etc/fdfs

WORKDIR /usr/local/src/fastdfs-master/conf
RUN cp http.conf mime.types /etc/fdfs/

# 配置 Nginx
ADD nginx.conf /usr/local/nginx/conf


COPY entrypoint.sh /usr/local/bin/
ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]

WORKDIR /
EXPOSE 8888
CMD ["/bin/bash"]

