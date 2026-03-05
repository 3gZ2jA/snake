# Snake (贪吃蛇) Web

一个零依赖、单文件的贪吃蛇网页游戏（HTML/CSS/JS）。

## Run / 运行

在项目目录启动一个静态服务器（推荐 Python）：

```bash
cd /root/code/snake
python3 -m http.server 8000 --bind 0.0.0.0
```

然后访问：

- 本机：`http://127.0.0.1:8000/index.html`
- 其他机器：`http://<服务器IP>:8000/index.html`

> 如需从外部访问，请确保安全组/防火墙放行 8000 端口。

## Controls / 操作

- 键盘：方向键 / WASD
- 触屏：屏幕方向键（D-pad）
- 触屏：在画布上滑动（上下左右）
- Space：开始/暂停
- R：重新开始

## Rules / 规则

- 支持穿墙：撞到边框不会结束，会从对向边框出来
- 只有撞到自己才会结束
- 吃到食物加分并变长，分数越高速度越快
- 最高分保存在浏览器本地（localStorage）
