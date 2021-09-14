# (零) 要点提炼
1. http服务：
1. http_server统计信息网站
1. http_api信令与统计信息接口等

# (一) 下载编译运行srs

1. 下载编译运行
    ```
    git clone https://github.com/ossrs/srs.git
    cd srs/trunk
    make -j8
    ./objs/srs -c conf/srs.conf
    ps -ef | grep srs
    netstat -anp | grep srs
    lsof -i:1935
    tail -f ./objs/srs.log
    cat ./objs/srs.pid
    
    ps -ef | grep srs | grep -v grep | awk '{print $2}' | xargs kill -9
    
    ```
2. 推拉流测试
    ```
    ffmpeg -re -i bipbop.mp4 -vcodec libx264 -acodec aac -f flv -y rtmp://192.168.0.200/live/livestream
    ```
    ```
    rtmp://192.168.0.200/live/livestream
    http://192.168.0.200:8080/live/livestream.flv
    http://192.168.0.200:8080/live/livestream.m3u8
    ```
3. 录像
    
2. 看看配置文件都有啥
    - rtmp监听1935，最多1000个连接
    - 在1985端口开启了一个http api服务。
    - 在8080端口开启了一个http server服务。
    - 其他略过。
![xxx](C:/Users/MoMing/Desktop/learn_srs/images/configfile.png)
3. 用浏览器访问两个http服务试试：


--ignore-certificate-errors --allow-running-insecure-content --unsafely-treat-insecure-origin-as-secure="http://192.168.0.200:8080"

chrome://flags/#unsafely-treat-insecure-origin-as-secure

# (二) 代码预览

2. 看看目录结构
![xxx](C:/Users/MoMing/Desktop/learn_srs/images/file_list.png)

# (三) 编译运行


![xxx](C:/Users/MoMing/Desktop/learn_srs/images/build.png)


# SRS资料汇总：
1. 代码
1. wiki：1.0，2.0，3.0，4.0
1. 视频：作者b站，零声两门，其他一些。
1. 公众号
1. 知识星球