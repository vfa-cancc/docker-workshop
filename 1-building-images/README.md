# Building images

## Tạo Dockerfile

Để build Docker image, bạn cần phải tạo một file text với tên là Dockerfile. File này đặc tả và quy định các tham số, để build Docker image:

* **FROM** -- cài đặt Docker image sẽ build dựa trên base image nào
* **RUN** -- thực thi command trong container
* **ENV** -- cài đặt biến môi trường
* **WORKDIR** -- cài đặt working directory
* **VOLUME** -- quy định mount thư mục nào thành volume
* **CMD** -- cài đặt command chính khi start container

Tham khảo thêm [Dockerfile reference](https://docs.docker.com/engine/reference/builder/).

Ví dụ dưới đây, chúng ta sẽ tạo một image, với nhiệm vụ lấy dữ liệu từ một website sử dụng curl, sau đó lưu vào file html. Địa chỉ website sẽ được lưu vào biến môi trường **SITE_URL**. Kết quả sẽ lưu vào một thư mục được mounted dưới dạng volume.

Tạo file **Dockerfile**, để trong thư mục bất kỳ chẳng hạn **examples/curl/Dockerfile**, với nội dung:

```dockerfile
FROM ubuntu:latest
RUN apt-get update \
    && apt-get install --no-install-recommends --no-install-suggests -y curl \
    && rm -rf /var/lib/apt/lists/*
ENV SITE_URL https://google.com
WORKDIR /data
VOLUME /data
CMD sh -c "curl -Lk $SITE_URL > /data/results.html"
```

## Build

Khi tạo xong Dockerfile, ta sẽ bắt đầu build Docker image

Gõ lệnh dưới đây:

```bash
docker build . -t test-curl
```

Console output:

```
Sending build context to Docker daemon  3.584kB
Step 1/6 : FROM ubuntu:latest
 ---> 113a43faa138
Step 2/6 : RUN apt-get update     && apt-get install --no-install-recommends --no-install-suggests -y curl     && rm -rf /var/lib/apt/lists/*
 ---> Running in ccc047efe3c7
Get:1 http://archive.ubuntu.com/ubuntu bionic InRelease [242 kB]
Get:2 http://security.ubuntu.com/ubuntu bionic-security InRelease [83.2 kB]
...
Removing intermediate container ccc047efe3c7
 ---> 8d10d8dd4e2d
Step 3/6 : ENV SITE_URL http://example.com/
 ---> Running in 7688364ef33f
Removing intermediate container 7688364ef33f
 ---> c71f04bdf39d
Step 4/6 : WORKDIR /data
Removing intermediate container 96b1b6817779
 ---> 1ee38cca19a5
Step 5/6 : VOLUME /data
 ---> Running in ce2c3f68dbbb
Removing intermediate container ce2c3f68dbbb
 ---> f499e78756be
Step 6/6 : CMD sh -c "curl -Lk $SITE_URL > /data/results"
 ---> Running in 834589c1ac03
Removing intermediate container 834589c1ac03
 ---> 4b79e12b5c1d
Successfully built 4b79e12b5c1d
Successfully tagged test-curl:latest
```

* **docker build** lệnh để build một image mới ở local.
* **-t** tham số để set tag name cho image.

Sau khi build hoàn thành, chúng ta hãy xem lại danh sách images:

```bash
docker images
```

Console output:

```
REPOSITORY  TAG     IMAGE ID      CREATED         SIZE
test-curl   latest  5ebb2a65d771  37 minutes ago  180 MB
nginx       latest  6b914bbcb89e  7 days ago      182 MB
ubuntu      latest  0ef2e08ed3fa  8 days ago      130 MB
```

## Run container từ image đã tạo

Bây giờ ta hãy thử chạy một container mới từ image đã tạo:

```bash
docker run --rm -v $(pwd)/vol:/data/:rw test-curl
```

* **--rm** -- tự xóa container sau khi stop
* **-v** -- chỉ định mount volume
* **$(pwd)** -- thư mục hiện tại

Chạy lệnh bên dưới để xem kết quả, trang web có được lưu thành file hay không?:

```bash
cat ./vol/results.html
```

## Thêm ví dụ Dockerfile

Trong Dockerfile bên dưới, ta sẽ tạo một file ảnh dựa trên base image ubuntu:18.04, sau đó cài đặt apache và php; EXPOSE port 80, mount volume `/var/www/htmnl`. Tiếp theo copy file `index.html` và thư mục mặc định của webserver apache `/var/www/html`; cuối cùng cài đặt start apache ở background.

```dockerfile
FROM ubuntu:18.04

MAINTAINER <cancc@vitalify.asia>
  
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -yq --no-install-recommends \
    apt-utils \
    # Install apache
    apache2 \
    # Install php
    php \
    libapache2-mod-php \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

EXPOSE 80 443

WORKDIR /var/www/html

COPY index.html /var/www/html/

VOLUME /var/www/html

CMD apachectl -D FOREGROUND
```