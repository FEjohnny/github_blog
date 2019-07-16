---
title: PWA技术之推送通知
date: 2018-06-24 22:07:17
tags: [PWA, javascript]
---

&emsp;&emsp;消息推送，特别是离线消息推送，一直以来都是native专有到东西，而且有着广泛到应用场景：

      1. 新品上架，推送消息给用户，点击即进入商品详情页面。

      2. 用户很久没有使用过应用，推送消息给用户，告知我们应用的更新。

      3. 像天气应用，可以在恶劣气候到来之前，推送消息给用户，让其知晓。

      4. 在用户没有登陆应用的时候，推送收到的最新消息给用户。

      .......
<!--more-->

&emsp;&emsp;使用推送消息通知，能够让我们的应用像 Native App 一样，提升用户体验（不过目前该功能的客户端浏览器的支持度并不是太高）。

push api兼容性图

![](/images/15.png)

# 一、推送通知的流程

推送流程图

![](/images/16.png)

&emsp;&emsp;发送推送通知需要三个步骤。首先我们需要提示用户并获得他们的订阅详细信息，然后将这些详细信息保存在服务器上，最后在需要时发送任何消息。       

&emsp;&emsp;首先，浏览器会显示一个提示以询问用户是否愿意接受通知。如果接受，我们便可以将用户的订阅详细信息保存在服务器上，稍后会使用它们来发送通知。请注意，这些订阅详细信息对于每个用户、设备和浏览器来说是唯一的，所以如果一个用户使用多个设备登录了你的网站，那么每台设备都会提醒该用户是否接受通知。

# 二、 获取授权

&emsp;&emsp;在能开始向用户发送通知前，我们需要通过显示提示的方式来征求他们的允许。这个提示功能是浏览器默认自带的，但首先我们需要添加一些代码来确保提示已经启用。如果用户接受提示的话，你将获得一个包含该用户订阅信息的订阅对象。可如果用户拒绝的话，你将无法向其发送任何消息，并且他们不会再被提示。这样会确保用户不会每次访问我们的网站时都被提示。那样可能会让用户非常反感！

![](/images/17.png)

&emsp;&emsp;使用Notification.requestPermission的方式中，由于通知API还不稳定，需要兼容新旧版本的返回值：

```
function askPermission() {
    return new Promise(function (resolve, reject) {
        var permissionResult = Notification.requestPermission(function (permission) {
            // 旧版本
            resolve(permission);
        });
        if (permissionResult) {
            permissionResult.then(resolve, reject);
        }
    }).then(function (permissionResult) {
        // permissionResult的值为granted或者denied
        if(permissionResult !== 'granted'){
            //用户未授权
            console.log('未获得通知权限');
        }
    });
}
```

# 三、 订阅通知

&emsp;&emsp;如同卖家只有获取了买家的地址簿，才能委托快递公司发货。服务端作为消息来源，委托推送服务发送消息给订阅消息的浏览器，也必须知道浏览器的具体地址。这个具体的地址是推送服务产生的，不同的服务端和不同的浏览器都会产生不同的地址。那么在订阅时服务端（也就是我们的应用）就需要一个唯一标识的身份。这里就需要引入applicationServerKey的概念，它又被称作VAPID，这就是我们应用的唯一标识。

&emsp;&emsp;VAPID 是“自主应用服务器标识” ( Voluntary Application Server Identification ) 的简称。它是一个规范，它本质上定义了应用服务器和推送服务之间的握手，并允许推送服务确认哪个站点正在发送消息。这很重要，因为这意味着应用服务器能够包含有关其自身的附加信息，可用于联系应用服务器的操作人员。拥有像 VAPID 这样的标准就是向前迈出了一大步，因为这意味着最终所有浏览器都将遵循这个单一标准，即无论什么浏览器，开发者都可以使用 Web 推送进行无缝工作。

&emsp;&emsp;生成applicationServerKey:

&emsp;&emsp;node.js服务端使用 web-push 生成，关键代码：

```
const webpush = require('web-push');
```

```
var vapidKeys = webpush.generateVAPIDKeys(); // 生成公私钥
```

```
//设置 VAPID 详情,设置公私钥
webpush.setVapidDetails(
    'mailto:web-push-book@haorooms.com',
    vapidKeys.publicKey,
    vapidKeys.privateKey
);
webpush.setGCMAPIKey('AIzaSyBygaa3EvEQboDzNdNez9EodI9xPjXLPFA');
```

&emsp;&emsp;在注册serviceWorkers成功之后，我们就可以调用pushManager.getSubscription()方法来获取客户端对订阅对象，如果没有该对象，则调用pushManager.subscribe()方法来实现订阅，此方法需要用户对通知进行授权，授权成功后即可获得一个订阅对象，然后将其发送至服务端来保存，该对象包含来客户端对订阅信息。

```
navigator.serviceWorker.register('/sw.js').then(function (registration) {
    // 获取任何已存在的订阅
    return registration.pushManager.getSubscription().then(function (subscription) {
        //如果已经订阅过了，则无需再次注册
        if (subscription) {
            console.log('已经订阅过了');
            return;
        }
        // 还没有订阅过，则显示一个提示
    // 由于我这里订阅的是google的服务，所以国内的话需要翻墙才能成功订阅
    registration.pushManager.subscribe({
            userVisibleOnly: true, // 保证推送对用户可见
       applicationServerKey: urlBase64ToUint8Array(vapidPublicKey) //推送服务器用以识别应用服务器的密钥vapidKeys.publicKey,
    }).then(function (sub) {
            //需要从订阅对象中获取 key 和 authSecret
            var rawKey = sub.getKey ? sub.getKey('p256dh') : '';
            key = rawKey ? btoa(String.fromCharCode.apply(null, new Uint8Array(rawKey))) : '';
            var rawAuthSecret = sub.getKey ? sub.getKey('auth') : '';

            authSecret = rawAuthSecret ? btoa(String.fromCharCode.apply(null, new Uint8Array(rawAuthSecret))) : '';
            endpoint = sub.endpoint;

            //最后，将详细信息发送给服务器以注册用户
       return fetch('./register', {
                    method: 'post',
                    headers: new Headers({
                        'content-type': 'application/json'
                    }),
                    body: JSON.stringify({
                    endpoint: sub.endpoint,
                    key: key,//key 和 authSecret 将用于存储用户的详细信息
           authSecret: authSecret,
                }),
            });
        }).catch(function (reason) {
            console.log('获取权限失败：'+reason);
        });
    });
}).catch(function (err) {
    // 注册失败 :(
    console.log('ServiceWorker registration failed: ', err);
});
```

# 四、 推送通知

&emsp;&emsp;服务端获取用户的订阅对象之后，我们就可以将它保存起来，在以后需要的时候，就可以拿出来给客户端推送通知消息。订阅对象包含来三个字段，

&emsp;&emsp;endpoint：推送服务器地址；它完全由客户端决定，比如chrome浏览器的推送服务器地址大概就是这样的"https://fcm.googleapis.com/fcm/send/..."。

&emsp;&emsp;还有key和authSecret将用来存储订阅用户的详细信息。示例代码：

```
var vapidKeys = webpush.generateVAPIDKeys(); // 生成公私钥

//设置 VAPID 详情,设置公私钥
webpush.setVapidDetails(
    'localhost:3000',
    vapidKeys.publicKey,
    vapidKeys.privateKey
);
// webpush.setGCMAPIKey('AIzaSyBygaa3EvEQboDzNdNez9EodI9xPjXLPFA');

app.get('/', function (req, res) {
    res.sendFile(path.join(__dirname + '/index.html'));
});

app.get('/publicKey', function (req, res) {
    res.send(vapidKeys.publicKey);
})

app.post('/register', function (req, res) {
    var endpoint = req.body.endpoint;
    var key = req.body.key;
    var authSecret = req.body.authSecret;

    //保存用户注册详情，这样我们可以在稍后阶段向他们发送消息
    saveRegistrationDetails(endpoint, key, authSecret);

    //建立 pushSubscription 对象
    const pushSubscription = {
        endpoint: req.body.endpoint,
        keys: {
            auth: req.body.authSecret,
            p256dh: req.body.key
        }
    };
    var body = 'Thank you for registering';
    var iconUrl = 'homescreen.png';

    //发送 Web 推送消息
  webpush.sendNotification(pushSubscription,
      JSON.stringify({
          msg: body,
          url: 'http://localhost:3000/',
          icon: iconUrl
      })
    ).then(function (res) {
        res.sendStatus(201);
    }).catch(function (err) {
        console.log(err);
    });
    res.send('success');
});
```

# 五、 显示通知

&emsp;&emsp;在服务端将同送通知发送到浏览器推送服务器之后，我们还需要在serviceWorkers中接受推送消息来展示给用户。这样的话，只要浏览器的serviceworders一经注册开始工作之后，即便用户没有访问我们的应用，只要打开浏览器，就可以接受到该应用到消息通知。在sw.js中到接受通知：

```
self.addEventListener('push', function (event) {
    //检查服务端是否发来了任何有效载荷数据
    var payload = event.data ? JSON.parse(event.data.text()) : 'no payload';
    var title = 'Progressive Times';
    event.waitUntil(
        //使用提供的信息来显示 Web 推送通知
        self.registration.showNotification(title, {
            body: payload.msg,
            url: payload.url,
            icon: payload.icon
        })
    );
});
```