# cdn-manager

#### 项目介绍

根据@dadi/cdn 制作的脚手架，专门处理云转码上边的图片，自动剪切处理，标准化海报。

#### 关键配置

workspace/recipes/poster.json

```
{
  "recipe": "poster",
  "settings": {
    "format": "jpg",
    "quality": "90",
    "height": "240",
    "ratio": "16-9",
    "resizeStyle": "entropy"
  }
}
```

设置预处理模块，将图片处理成 16：9 的比例，高度 240PX，并且格式为 jpg，质量为 90%的格式。
config/config.production.json

```
{
  "server": {
    "host": "127.0.0.1",
    "port": 8001
  },
  "images": {
    "remote": {
      "enabled": true,
      "path": "#"
    }
  }
}
```

设置运行端口 8001，设置远程连接 path，比如云转码 express-ffmpeg 绑定的域名https://www.moejj.com.

#### 运行效果

比如https://www.moejj.com/videos/5bc721ef87af60065e31735e/1.jpg 是云转码上边的图片。
访问http://127.0.0.1:8001/poster/videos/5bc721ef87af60065e31735e/1.jpg 则会直接返回自动处理成 16：9 格式为 jpg 质量为 90%，高度为 240px，智能剪切的图片。
预处理设置的链接为 poster，详见上方 recipes 的配置，可以自行进行更改。

#### 更多用法

https://github.com/dadi/cdn 参考此库。

#### 重要的参数配置

config/config.production.json 运行环境配置文件

##### 缓存设置：

```
"caching": {
  "ttl": 3600,
  "expireAt": "0 5 0 * * *"
}
```

expireAt 配置解释

- "0 5 0 \* \* \_" 每天凌晨 12 点 5 分刷新缓存
- "0 30 11 \_ \_ 1-5" 每周星期一到星期五 早上 11 点 30 刷新缓存
- "0 15 14 1 \_ \_" 每个月一号下午 2 点 15 刷新缓存
- "0 22 \_ \_ 1-5" 周一到周五晚上 10 点刷新缓存
- "\_ 5 4 \* \* 0" 每周日凌晨 4 点 5 分刷新缓存
  ttl 配置解释
- 单位秒，默认 3600 秒，一个小时缓存失效，可以自行设置时长。
- /api/flush 可以通过这个 API 手动刷新缓存文件。

##### image 配置

```
"images": {
  "directory": {
    "enabled": true,
    "path": "relative/path/to/your/images"
  },
  "remote": {
    "enabled": true,
    "path": "https://server1.somedomain.tech/images"
  },
  "s3": {
    "enabled": true,
    "accessKey": "your-access-key",
    "secretKey": "your-secret",
    "bucketName": "your-bucket",
    "region": "your-region",
    "endpoint": "ams3.digitaloceanspaces.com"
  }
}
```

images 配置项
一共有三种用法，一种是直接服务同主机图片，服务本地图片，一种是服务远程图片，最后一种是亚马逊和 Digital Ocean Space 云储存。

**directory**
服务本地文件，直接指定 path 为文件夹地址即可直接处理本地图片。例如/www/express-ffmpeg/public，然后将图片地址 host 更改为 CDN 地址即可。

**remote**
服务远程文件，直接设置 path 为远程 url，例如 www.moejj.com，最后把地址替换成 cdn 地址即可。

**s3 云储存**
可扩展的云储存，可以直接缓存各种云储存上边的图片。

##### assets 配置

```
"assets": {
  "directory": {
    "enabled": true,
    "path": "/Users/absolute/path/to/your/assets"
  }
}
```

除了把 images 换成 assets，其他用法一样，设置之后会缓存除了 jpg 的各种文件，包括视频文件。

workspace/recipes/recipe.json 配置文件

```
{
  "recipe": "poster",
  "settings": {
    "format": "jpg",
    "quality": "90",
    "height": "240",
    "ratio": "16-9",
    "resizeStyle": "entropy"
  }
}
```

recipe 文件是预配置格式处理文件，在 workspace/recipes 文件夹中建立。

- "recipe"参数设置必须和文件名一致。
  settings 中可以设置的选项：
- blur 模糊
- filter 设置裁剪处理算法
- flip 翻动
- format 格式
- gravity 设置裁剪区域
- ratio 比例
- rotate 旋转
- width 宽度
- height 高度
- resizeStyle 裁剪模式 推荐 entropy
  等，详见 dadi/cdn 文档

设置完成之后，比如 recipe 为 poster，则访问链接为 cdnhost/poster/yourpath/1.jpg

#### 运行

- git clone https://gitee.com/quazero/cdn-manager
- cd cdn-manager
- npm install
- pm2 start index.js
