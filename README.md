# baseAli
阿里云盘获取共享下载连接的说明

<pre>
阿里的分享连接大概是这样的
https://www.aliyundrive.com/s/h87E19v6L8Z
其中https://www.aliyundrive.com/s/ 是固定的 h87E19v6L8Z是share_id 有的连接会需要密码share_pwd

alist 上分享的数据核心也就是一堆 share_id  和share_pwd，然后把这一大堆数据整合起来


阿里的连接过程是这样的
第一步 ，获取通过 share_token，和share_pwd 获取密码
剩下的几步获取数据都要用到这一步取到的 share_token，并把token放到请求头里面 header.put("x-share-token",share_token);

请求 URL: https://api.aliyundrive.com/v2/share_link/get_share_token
请求方法: POST
状态代码: 200 
远程地址: 47.94.245.197:443
引用者策略: origin

{"share_id":"Dz5KUjNgnPV","share_pwd":""}
<--
{"share_token":"eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJjdXN0b21Kc29uIjoie1wiZG9tYWluX2lkXCI6XCJiajI5XCIsXCJzaGFyZV9pZFwiOlwiRHo1S1VqTmduUFZcIixcImNyZWF0b3JcIjpcIjBiMjQwNWIxMDYyZjQzODdhZjUyZTRkMTQyNTMwZmJlXCIsXCJ1c2VyX2lkXCI6XCJhbm9ueW1vdXNcIn0iLCJjdXN0b21UeXBlIjoic2hhcmVfbGluayIsImV4cCI6MTY3NTg3MDM0NSwiaWF0IjoxNjc1ODYzMDg1fQ.LLS2T3FZTv0BaZbHMGPwWLw01vsE5gzln_KGHgekMa7o_7tsMG1BTYSIxmt360QlH5P35QEOh4bezH1ISXzC-wW9XtVABBdiba7QRaBgeCVHVRqkgBa-8k1tqlLeDGZ6ZERwVPvZAYezXo3imblYsCvSfLThLOLJSRx1uNzvwVg","expire_time":"2023-02-08T15:32:25.523Z","expires_in":7200}


第二步获取信息或浏览

请求 URL: https://api.aliyundrive.com/adrive/v2/file/get_by_share
请求方法: POST
状态代码: 200 
远程地址: 47.94.245.197:443
引用者策略: origin

header.put("x-share-token",share_token);get_share_token 获取的

{"share_id":"Dz5KUjNgnPV","file_id":"62d178172ff48b99a27a457aa25ba275842fbadd","fields":"*","image_thumbnail_process":"image/resize,w_400/format,jpeg","image_url_process":"image/resize,w_375/format,jpeg","video_thumbnail_process":"video/snapshot,t_1000,f_jpg,ar_auto,w_375"}
<--
{"drive_id":"67296335","domain_id":"bj29","file_id":"62d178172ff48b99a27a457aa25ba275842fbadd","share_id":"Dz5KUjNgnPV","name":"美食纪录片大合集（469GB）","type":"folder","created_at":"2022-07-15T14:22:15.579Z","updated_at":"2022-10-31T16:55:19.210Z","parent_file_id":"62d43b0ae9796975f5bb4b23a65db29c81401617","ex_fields_info":{"story_ids":"[]","story_image_score":0}}


请求 URL: https://api.aliyundrive.com/adrive/v2/file/list_by_share
请求方法: POST
状态代码: 200 
远程地址: 47.94.245.197:443
引用者策略: origin

header.put("x-share-token",share_token);get_share_token 获取的

{"share_id":"Dz5KUjNgnPV","parent_file_id":"62d178172ff48b99a27a457aa25ba275842fbadd","limit":20,"image_thumbnail_process":"image/resize,w_256/format,jpeg","image_url_process":"image/resize,w_1920/format,jpeg/interlace,1","video_thumbnail_process":"video/snapshot,t_1000,f_jpg,ar_auto,w_256","order_by":"name","order_direction":"DESC"}
<--
{"items":[{"drive_id":"67296335","domain_id":"bj29","file_id":"62d1705f366f5d63e7724e4a9deb1387a9b1d838","share_id":"Dz5KUjNgnPV","name":"44.食通街 26集 8.5G","type"
。。。。。。。。。。。。。。。。。。。。

,"type":"folder","created_at":"2022-07-15T13:49:17.524Z","updated_at":"2022-07-15T14:34:32.468Z","p22-07-15T14:34:30.482Z","parent_file_id":"62d178172ff48b99a27a457aa25ba275842fbadd"}],"next_marker":"WyI2MmQxNzgxNzJmZjQ4Yjk5YTI3YTQ1N2FhMjViYTI3NTg0MmZiYWRkIiwibiIsIm4iLDEsLTEsIjI0Lua+s+mXqOmjn+WFiSIsIjYyZDE3MDUzMmZjYWYxODdiYjgwNGYyOWI3Njg1NjlhMGEzODlmMGQiXQ==","punished_file_count":0}


获取下载连接，这里需要用户用自己的token登录，得到一个访问token（两个小时过期），再加上之前获取的x-share-token 放到请求头里面

登录连接"https://auth.aliyundrive.com/v2/account/token"

post 数据 
        {"refresh_token":这里填你自己的refreshToken,"grant_type", "refresh_token"}
        会返回一个新的refresh_token，（可以下次使用），和access_token
        请求连接的时候head加上 header.put("Authorization","Bearer "+loginret.getString("access_token"));
        
        


Request URL: https://api.aliyundrive.com/v2/file/get_share_link_download_url
Request Method: POST
Status Code: 200 
Remote Address: 47.94.245.197:443
Referrer Policy: origin
header.put("content-type", "application/json");
header.put("Authorization","Bearer "+用户登录的access_token); 注意Bearer后面要有空格
header.put("x-share-token",share_token);get_share_token 获取的

        
{"drive_id":"67296335","share_id":"Dz5KUjNgnPV","file_id":"62d170588f1c44555d3348fa97f4dd036d5063eb","image_url_process":"image/resize,w_1920/format,jpeg","image_thumbnail_process":"image/resize,w_1920/format,jpeg","expire_sec":600,"get_streams_url":true}
<---
{"download_url":"https://pdsapi.aliyundrive.com/v2/redirect?id=032f3c65a886499b868d71205c1998ca1675873435017663730","url":"https://pdsapi.aliyundrive.com/v2/redirect?id=c3c3c2d52b534deeb64d72ebc3b860b61675873435017637110","thumbnail":"https://pdsapi.aliyundrive.com/v2/redirect?id=a8e3cf5d20f14f3eb33250ed1ff31a051675873435017656233"}



这里有个坑是，获取的下载连接，最长十分钟有效。 https://pdsapi.aliyundrive.com/v2/redirect?id=032f3c65a886499b868d71205c1998ca1675873435017663730 ，这个连接本身是重定向的，
播放器有一个统一的解决办法是，自己用NanoHTTPD搭建一个服务器，给播放器的连接是本地连接 比如http://127.0.0.1/play302?fileid=62d170588f1c44555d3348fa97f4dd036d5063eb&size=xxxx
然后在NanoHTTPD 服务器重新调用get_share_link_download_url，然后返回更新的重定向url给播放器



参考
小白羊接口<br>
https://github.com/liupan1890/aliyunpan/tree/v3/adrive%20sdk
alist
https://github.com/alist-org/alist/tree/main/drivers/aliyundrive_share


</pre>

