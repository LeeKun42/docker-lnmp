# docker-lnmp

#### 介绍
docker搭建linux、nginx、mysql、php、redis环境，包含composer、npm

#### 软件架构



#### 使用说明
    首先安装docker-compose
    切换到本项目根目录，执行 docker-compose up -d
    docker run -v $(pwd):/www -w /www -it --rm node npm install