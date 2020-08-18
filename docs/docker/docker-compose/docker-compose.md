===== docker-compose.yml  =======
```
version: '3'
services:
  test-boot:
    build:
      context: ./
      dockerfile: ./Dockerfile
    image: gzxu/test-boot
    container_name: gzxu-tb
    restart: always
    ports:
      - 8080:8080
```
