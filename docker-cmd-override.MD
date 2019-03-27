今天运行 image: stefanprodan/podinfo:0.0.1 时发现一个小问题

**CMD 指定程序名时候 无法运行** 
```
docker run -it --rm  -p 9898:9898   stefanprodan/podinfo:0.0.1   ./podinfo -port=9898  -logtostderr=true  -v=2
```
下面这样是ok 的
```
docker run -it --rm  -p 9898:9898   stefanprodan/podinfo:0.0.1   -port=9898  -logtostderr=true  -v=2
```
查看这个dockfiler：
```
FROM alpine:3.7

RUN addgroup -S app \
    && adduser -S -g app app \
    && apk --no-cache add \
    curl openssl netcat-openbsd

WORKDIR /home/app
COPY ./ui ./ui
ADD podinfo .

RUN chown -R app:app ./

USER app

CMD ["./podinfo"]
```
显然 image 有默认的 CMD，安装 [Docker run reference  ：Overriding Dockerfile image defaults ](https://docs.docker.com/engine/reference/run/)   _"just by specifying a new COMMAND"_  **not working!**   
于是使用**ENTRYPOINT**override :  
```
docker run -it --rm  -p 9898:9898    --entrypoint=./podinf  stefanprodan/podinfo:0.0.1  -port=9898 -logtostderr=true  -v=2
```
PS: 程序 podinfo 默认的端口是 8989 所以即使 不进入容器
```
curl localhost:9898/healthz
```
就可以很好的验证。
