# Server Requirements

- PHP >= 7.2.0
- Redis
- Redis PHP Extension

# Installation

```bash
composer install
npm install
# copy ".env.example" to the current folder and name it ".env"
# set BROADCAST_DRIVER=log in ".env" 
php artisan key:generate
npm run watch
laravel-echo-server start
```

# Test

- Open the link [http://yoururl.com/test.html](http://yoururl.com/test.html) and wait for it to be broadcast.
- Open the link [http://yoururl.com/broadcast](http://yoururl.com/broadcast) to broadcast, then you will receive a message on the link link [http://yoururl.com/test.html](http://yoururl.com/test.html).

# From Fresh Laravel To This Sample

> 配套视频地址：[https://www.bilibili.com/video/av78577184](https://www.bilibili.com/video/av78577184 "https://www.bilibili.com/video/av78577184")

----

## 安装与配置

```php
composer create-project --prefer-dist laravel/laravel . "6.*" 
在 .env 中配置驱动 "pusher", "redis", "log", "null"        
```

```bash
npm set registry https://registry.npm.taobao.org/

npm install --save socket.io-client |  echo 'websocket 客户端'
npm install --save laravel-echo     |  echo 'websocket 客户端封装'
npm install -g laravel-echo-server  |  echo 'websocket 服务端'
npm install                         |  echo '安装所有其他依赖'

npm run watch                        |  echo '监控文件变化编译前端资源'

laravel-echo-server init             |  echo '初始化 websocket 服务端'
laravel-echo-server start            |  echo '启动 websocket 服务端'
```

###### **初始化 websocket 客户端**

```php
// bootstrap.js
import Echo from "laravel-echo"

window.io = require('socket.io-client');

window.Echo = new Echo({
    broadcaster: 'socket.io',
    host: window.location.hostname + ':6001'
});
```

###### **事件**

```bash
php artisan make:event NewMessage
```

```php
<?php

namespace App\Events;

use Illuminate\Broadcasting\Channel;
use Illuminate\Broadcasting\InteractsWithSockets;
use Illuminate\Broadcasting\PresenceChannel;
use Illuminate\Broadcasting\PrivateChannel;
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;

class NewMessage implements ShouldBroadcast
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    public $message;

    /**
     * Create a new event instance.
     *
     * @param $message
     */
    public function __construct($message)
    {
        $this->message = $message;
    }

    /**
     * Get the channels the event should broadcast on.
     *
     * @return \Illuminate\Broadcasting\Channel|array
     */
    public function broadcastOn()
    {
        return new Channel('official');
    }
}
```

###### **设置客户端监听广播 `public/test.html`**

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="js/app.js"></script>
    <script>
        window.Echo.channel('laravel_database_official')
            .listen('NewMessage', (e) => {
                alert(e.message);
            });
    </script>
</head>
<body>

</body>
</html>
```

###### **触发事件，广播开始**

```php
Route::get('/broadcast', function () {
    event(new \App\Events\NewMessage('hi'));
});
```




