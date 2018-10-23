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

#### 运行

git clone https://gitee.com/quazero/cdn-manager
cd cdn-manager
pm2 start index.js
