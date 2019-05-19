# Egg.js 是什么?
    Egg.js 为企业级框架和应用而生，我们希望由 Egg.js 孕育出更多上层框架，帮助开发团队和开发人员降低开发和维护成本。

# 设计原则
    专注于提供 Web 开发的核心功能和一套灵活可扩展的插件机制。
    
    在 Egg 基础上扩展出适合自身业务场景的框架。
    
    Egg 的插件机制有很高的可扩展性，一个插件只做一件事（比如 Nunjucks 模板封装成了 egg-view-nunjucks、MySQL 数据库封装成了 egg-mysql）。
    
    Egg 通过框架聚合这些插件，并根据自己的业务场景定制配置。
    
    『约定优于配置』，按照一套统一的约定进行应用开发，使用 Loader 可以让框架根据不同环境定义默认配置，还可以覆盖 Egg 的默认约定。

# 与社区框架的差异
    Express 是 Node.js 社区广泛使用的框架，简单且扩展性强，非常适合做个人项目。但框架本身缺少约定，标准的 MVC 模型会有各种千奇百怪的写法。Egg 按照约定进行开发，奉行『约定优于配置』，团队协作成本低。

    Sails 是和 Egg 一样奉行『约定优于配置』的框架，扩展性也非常好。但是相比 Egg，Sails 支持 Blueprint REST API、WaterLine 这样可扩展的 ORM、前端集成、WebSocket 等，但这些功能都是由 Sails 提供的。而插件，再使用 sails-egg 框架整合这些插件就可以替代 Sails 了。

# 特性
    提供基于 Egg 定制上层框架的能力
    高度可扩展的插件机制
    内置多进程管理
    基于 Koa 开发，性能优异
    框架稳定，测试覆盖率高
    渐进式开发
    
# 快速初始化
    npm init egg --type=simple
    npm i    
    npm run dev
    open localhost:7001
    
# 目录结构
````
egg-project
├── package.json
├── app.js (可选)
├── agent.js (可选)
├── app
|   ├── router.js
│   ├── controller
│   |   └── home.js
│   ├── service (可选)
│   |   └── user.js
│   ├── middleware (可选)
│   |   └── response_time.js
│   ├── schedule (可选)
│   |   └── my_task.js
│   ├── public (可选)
│   |   └── reset.css
│   ├── view (可选)
│   |   └── home.tpl
│   └── extend (可选)
│       ├── helper.js (可选)
│       ├── request.js (可选)
│       ├── response.js (可选)
│       ├── context.js (可选)
│       ├── application.js (可选)
│       └── agent.js (可选)
├── config
|   ├── plugin.js
|   ├── config.default.js
│   ├── config.prod.js
|   ├── config.test.js (可选)
|   ├── config.local.js (可选)
|   └── config.unittest.js (可选)
└── test
    ├── middleware
    |   └── response_time.test.js
    └── controller
        └── home.test.js
````
app/router.js 用于配置 URL 路由规则，具体参见 Router。

app/controller/** 用于解析用户的输入，处理后返回相应的结果，具体参见 Controller。

app/service/** 用于编写业务逻辑层，可选，建议使用，具体参见 Service。

app/middleware/** 用于编写中间件，可选，具体参见 Middleware。

app/public/** 用于放置静态资源，可选，具体参见内置插件 egg-static。

app/extend/** 用于框架的扩展，可选，具体参见框架扩展。

config/config.{env}.js 用于编写配置文件，具体参见配置。

config/plugin.js 用于配置需要加载的插件，具体参见插件。

test/** 用于单元测试，具体参见单元测试。

app.js 和 agent.js 用于自定义启动时的初始化工作，可选，具体参见启动自定义。关于agent.js的作用参见Agent机制。

>>由内置插件约定的目录：

app/public/** 用于放置静态资源，可选，具体参见内置插件 egg-static。

app/schedule/** 用于定时任务，可选，具体参见定时任务。

app/view/** 用于放置模板文件，可选，由模板插件约定，具体参见模板渲染。

app/model/** 用于放置领域模型，可选，由领域类相关插件约定，如 egg-sequelize。

# 框架内置基础对象

    框架继承Koa4 个对象（Application, Context, Request, Response) 以及框架扩展的一些对象（Controller, Service, Helper, Config）

* Application

>> Application 是全局应用对象，在一个应用中，只会实例化一个，它继承自 Koa.Application，在它上面我们可以挂载一些全局的方法和对象。我们可以轻松的在插件或者应用中扩展 Application 对象。

* 事件

在框架运行时，会在 Application 实例上触发一些事件，应用开发者或者插件开发者可以监听这些事件做一些操作。

server: 该事件一个 worker 进程只会触发一次，在 HTTP 服务完成启动后，会将 HTTP server 通过这个事件暴露出来给开发者。

error: 运行时有任何的异常被 onerror 插件捕获后，都会触发 error 事件，将错误对象和关联的上下文（如果有）暴露给开发者，可以进行自定义的日志记录上报等处理。

request 和 response: 应用收到请求和响应请求时，分别会触发 request 和 response 事件，并将当前请求上下文暴露出来，开发者可以监听这两个事件来进行日志记录。

````
// app.js

module.exports = app => {
  app.once('server', server => {
    // websocket
  });
  app.on('error', (err, ctx) => {
    // report error
  });
  app.on('request', ctx => {
    // log receive request
  });
  app.on('response', ctx => {
    // ctx.starttime is set by framework
    const used = Date.now() - ctx.starttime;
    // log total cost
  });
};
````

* 获取方式

>>Application 对象几乎可以在编写应用时的任何一个地方获取到，下面介绍几个经常用到的获取方式：

几乎所有被框架Loader加载的文件（Controller，Service，Schedule 等），都可以 export 一个函数，这个函数会被 Loader 调用，并使用 app 作为参数：

* Context

Context 是一个请求级别的对象，继承自 Koa.Context。在每一次收到用户请求时，框架会实例化一个 Context 对象，这个对象封装了这次用户请求的信息，并提供了许多便捷的方法来获取请求参数或者设置响应信息。框架会将所有的 Service 挂载到 Context 实例上，一些插件也会将一些其他的方法和对象挂载到它上面（egg-sequelize 会将所有的 model 挂载在 Context 上）。

````
// app.js
module.exports = app => {
  app.beforeStart(async () => {
    const ctx = app.createAnonymousContext();
    // preload before app start
    await ctx.service.posts.load();
  });
}
````

* Request & Response

    Request 是一个请求级别的对象，继承自 Koa.Request。封装了 Node.js 原生的 HTTP Request 对象，提供了一系列辅助方法获取 HTTP 请求常用参数。

    Response 是一个请求级别的对象，继承自 Koa.Response。封装了 Node.js 原生的 HTTP Response 对象，提供了一系列辅助方法设置 HTTP 响应。

获取方式

````
// app/controller/user.js
class UserController extends Controller {
  async fetch() {
    const { app, ctx } = this;
    const id = ctx.request.query.id;
    ctx.response.body = app.cache.get(id);
  }
}
````

# 路由（Router）

    Router 主要用来描述请求 URL 和具体承担执行动作的 Controller 的对应关系， 框架约定了 app/router.js 文件用于统一所有路由规则。

如何定义 Router
    
````
app/router.js 里面定义 URL 路由规则
// app/router.js
module.exports = app => {
  const { router, controller } = app;
  router.get('/user/:id', controller.user.info);
};
````

# 控制器（Controller）

* 什么是 Controller

前面章节写到，我们通过 Router 将用户的请求基于 method 和 URL 分发到了对应的 Controller 上，那 Controller 负责做什么？

简单的说 Controller 负责解析用户的输入，处理后返回相应的结果

所有的 Controller 文件都必须放在 app/controller 目录下，可以支持多级目录，访问的时候可以通过目录名级联访问。
````
// app/controller/post.js
const Controller = require('egg').Controller;
class PostController extends Controller {
  async create() {
    const { ctx, service } = this;
    const createRule = {
      title: { type: 'string' },
      content: { type: 'string' },
    };
    // 校验参数
    ctx.validate(createRule);
    // 组装参数
    const author = ctx.session.userId;
    const req = Object.assign(ctx.request.body, { author });
    // 调用 Service 进行业务处理
    const res = await service.post.create(req);
    // 设置响应内容和响应状态码
    ctx.body = { id: res.id };
    ctx.status = 201;
  }
}
module.exports = PostController;
````

# 服务（Service）

* 简单来说，Service 就是在复杂业务场景下用于做业务逻辑封装的一个抽象层，提供这个抽象有以下几个好处：

定义 Service
````
// app/service/user.js
const Service = require('egg').Service;

class UserService extends Service {
  async find(uid) {
    const user = await this.ctx.db.query('select * from user where uid = ?', uid);
    return user;
  }
}

module.exports = UserService;
````
属性
每一次用户请求，框架都会实例化对应的 Service 实例，由于它继承于 egg.Service，故拥有下列属性方便我们进行开发：
this.ctx: 当前请求的上下文 Context 对象的实例，通过它我们可以拿到框架封装好的处理当前请求的各种便捷属性和方法。
this.app: 当前应用 Application 对象的实例，通过它我们可以拿到框架提供的全局对象和方法。
this.service：应用定义的 Service，通过它我们可以访问到其他业务层，等价于 this.ctx.service 。
this.config：应用运行时的配置项。

# 模板渲染
    在本例中，我们使用 Nunjucks 来渲染，先安装对应的插件 egg-view-nunjucks ：
    npm i egg-view-nunjucks --save
    开启插件：
    // config/plugin.js
    exports.nunjucks = {
        enable: true,
        package: 'egg-view-nunjucks'
    };

    // config/config.default.js
    exports.view = {
        defaultViewEngine: 'nunjucks',
        mapping: {
            '.tpl': 'nunjucks',
        },
    };

* 渲染页面

框架在 Context 上提供了 3 个接口，返回值均为 Promise:

render(name, locals) 渲染模板文件, 并赋值给 ctx.body

renderView(name, locals) 渲染模板文件, 仅返回不赋值

renderString(tpl, locals) 渲染模板字符串, 仅返回不赋值

````
class HomeController extends Controller {
  async index() {
    const data = { name: 'egg' };

    // render a template, path relate to `app/view`
    await ctx.render('home/index.tpl', data);

    // or manually set render result to ctx.body
    ctx.body = await ctx.renderView('path/to/file.tpl', data);

    // or render string directly
    ctx.body = await ctx.renderString('hi, {{ name }}', data, {
      viewEngine: 'nunjucks',
    });
  }
}
````

# 配置

# 中间件

    koa对网络请求采用了中间件的形式处理,中间件可以介入请求和相应的处理,是一个轻量级的模块,每个中间负责完成某个特定的功能。
    中间件的通过next函数联系,执行next()后会将控制权交给下一个中间件,如果没有有中间件没有执行next后将会沿路折返,
    将控制权交换给前一个中间件。
    
![Image text](https://images2017.cnblogs.com/blog/1255164/201711/1255164-20171130184647792-267006694.png)

* 写法

>>app目录下新建middleware文件夹

````
app/middleware/middlewareOne.js

module.exports = (options, app) => {
    return async function middlewareOne(ctx, next) {
        console.log('hello middleware start one' + options.data);
        await next();
        console.log('hello middleware end one' + options.data);
    }
}
````

````
app/middleware/middlewareTwo.js

module.exports = (options, app) => {
    return async function middlewareTwo(ctx, next) {
        console.log('hello middleware start two ' + options.data);
        await next();
        console.log('hello middleware end two ' + options.data);
    }
}
````

>>配置与赋值

````
//config/config.default.js

module.exports = appInfo => {

  const config = {};
  config.keys = appInfo.name + '_1557571399151_5747';

  //每次请求都会跑 用于cookie校验，防爬虫等
  config.middleware = ["middlewareOne","middlewareTwo"];
  
  //为middlewareOne中的options赋值
  config.middlewareOne = {
        data: '11111111Two111!'
  };
  //为middlewareTwo中的options赋值
  config.middlewareTwo = {
       data: '2222222！'
  }
  return {
    ...config,
  };
};
````

>>使用

````
app/router.js

module.exports = app => {
  const { router, controller } = app;
  router.get('/', controller.home.index);
  //访问test接口时调用
  const middlewareOne = app.middleware.middlewareOne({ data: "33333" });
  router.get('/test', middlewareOne, controller.home.test);
};
````

>>打印结果

hello middleware start one11111111111！

hello middleware start two 2222222！

hello middleware start one33333

test_1557571399151_5747

hello middleware end one33333

hello middleware end two 2222222！

hello middleware end one11111111111！


# MySQL

在 Web 应用方面 MySQL 是最常见，最好的关系型数据库之一。

* egg-mysql

        框架提供了 egg-mysql 插件来访问 MySQL 数据库。
    
        这个插件既可以访问普通的 MySQL 数据库，也可以访问基于 MySQL 协议的在线数据库服务。

* 安装与配置

        安装对应的插件 egg-mysql ：

        $ npm i --save egg-mysql

        开启插件：
    ````
    // config/plugin.js
        exports.mysql = {
            enable: true,
            package: 'egg-mysql',
        };
    ````

* 单数据源

    如果我们的应用只需要访问一个 MySQL 数据库实例，可以如下配置：
````
// config/config.${env}.js
exports.mysql = {
  // 单数据库信息配置
  client: {
    // host
    host: 'mysql.com',
    // 端口号
    port: '3306',
    // 用户名
    user: 'test_user',
    // 密码
    password: 'test_password',
    // 数据库名
    database: 'test',
  },
  // 是否加载到 app 上，默认开启
  app: true,
  // 是否加载到 agent 上，默认关闭
  agent: false,
};
````

* 使用方式：

        await app.mysql.query(sql, values); // 单实例可以直接通过 app.mysql 访问
        
* 多数据源
        
        如果我们的应用需要访问多个 MySQL 数据源，可以按照如下配置：
````
exports.mysql = {
  clients: {
    // clientId, 获取client实例，需要通过 app.mysql.get('clientId') 获取
    db1: {
      // host
      host: 'mysql.com',
      // 端口号
      port: '3306',
      // 用户名
      user: 'test_user',
      // 密码
      password: 'test_password',
      // 数据库名
      database: 'test',
    },
    db2: {
      // host
      host: 'mysql2.com',
      // 端口号
      port: '3307',
      // 用户名
      user: 'test_user',
      // 密码
      password: 'test_password',
      // 数据库名
      database: 'test',
    },
    // ...
  },
  // 所有数据库配置的默认值
  default: {

  },

  // 是否加载到 app 上，默认开启
  app: true,
  // 是否加载到 agent 上，默认关闭
  agent: false,
};
````

使用方式：

````
const client1 = app.mysql.get('db1');
await client1.query(sql, values);

const client2 = app.mysql.get('db2');
await client2.query(sql, values);
````

* 动态创建

我们可以不需要将配置提前申明在配置文件中，而是在应用运行时动态的从配置中心获取实际的参数，再来初始化一个实例。

````
// {app_root}/app.js
module.exports = app => {
  app.beforeStart(async () => {
    // 从配置中心获取 MySQL 的配置
    // { host: 'mysql.com', port: '3306', user: 'test_user', password: 'test_password', database: 'test' }
    const mysqlConfig = await app.configCenter.fetch('mysql');
    app.database = app.mysql.createInstance(mysqlConfig);
  });
};
````

* Service 层

    由于对 MySQL 数据库的访问操作属于 Web 层中的数据处理层，因此我们强烈建议将这部分代码放在 Service 层中维护。

````
// app/service/user.js
class UserService extends Service {
  async find(uid) {
    // 假如 我们拿到用户 id 从数据库获取用户详细信息
    const user = await this.app.mysql.get('users', { id: 11 });
    return { user };
  }
}
````

之后可以通过 Controller 获取 Service 层拿到的数据。

````
// app/controller/user.js
class UserController extends Controller {
  async info() {
    const ctx = this.ctx;
    const userId = ctx.params.id;
    const user = await ctx.service.user.find(userId);
    ctx.body = user;
  }
}
````

* 如何编写 CRUD 语句

    下面的语句若没有特殊注明，默认都书写在 app/service 下。

    Create
    可以直接使用 insert 方法插入一条记录。
````
    // 插入
    const result = await this.app.mysql.insert('posts', { title: 'Hello World' }); // 在 post 表中，插入 title 为 Hello World 的记录

    => INSERT INTO `posts`(`title`) VALUES('Hello World');

    console.log(result);
    
    =>
    {
    fieldCount: 0,
    affectedRows: 1,
    insertId: 3710,
    serverStatus: 2,
    warningCount: 2,
    message: '',
    protocol41: true,
    changedRows: 0
    }

    // 判断插入成功
    const insertSuccess = result.affectedRows === 1;
````

直接执行 sql 语句

插件本身也支持拼接与直接执行 sql 语句。使用 query 可以执行合法的 sql 语句。

注意！！我们极其不建议开发者拼接 sql 语句，这样很容易引起 sql 注入！！

如果必须要自己拼接 sql 语句，请使用 mysql.escape 方法。

参考 preventing-sql-injection-in-node-js

````
const postId = 1;

const results = await this.app.mysql.query('update posts set hits = (hits + ?) where id = ?', [1, postId]);

=> update posts set hits = (hits + 1) where id = 1;
````


# Socket.IO

Socket.IO 是一个基于 Node.js 的实时应用程序框架，在即时通讯、通知与消息推送，实时分析等场景中有较为广泛的应用。

WebSocket 的产生源于 Web 开发中日益增长的实时通信需求，对比基于 http 的轮询方式，它大大节省了网络带宽，同时也降低了服务器的性能消耗； socket.io 支持 websocket、polling 两种数据传输方式以兼容浏览器不支持 WebSocket 场景下的通信需求。

* 框架提供了 egg-socket.io 插件，增加了以下开发规约：

        namespace: 通过配置的方式定义 namespace（命名空间）
        middleware: 对每一次 socket 连接的建立/断开、每一次消息/数据传递进行预处理
        controller: 响应 socket.io 的 event 事件
        router: 统一了 socket.io 的 event 与 框架路由的处理配置方式
        
* 安装 egg-socket.io

>> 安装
    
    $ npm i egg-socket.io --save
    
>> 开启插件：

````
// {app_root}/config/plugin.js
exports.io = {
  enable: true,
  package: 'egg-socket.io',
};
````

>> 配置

````
// {app_root}/config/config.${env}.js
exports.io = {
  init: { }, // passed to engine.io
  namespace: {
    '/': {
      connectionMiddleware: [],
      packetMiddleware: [],
    },
    '/example': {
      connectionMiddleware: [],
      packetMiddleware: [],
    },
  },
};
````

命名空间为 / 与 /example, 不是 example

uws
Egg Socket 内部默认使用 ws 引擎，uws 因为某些原因被废止。

如坚持需要使用，请按照以下配置即可：

// {app_root}/config/config.${env}.js
exports.io = {
  init: { wsEngine: 'uws' }, // default: ws
};
redis
egg-socket.io 内置了 socket.io-redis，在 cluster 模式下，使用 redis 可以较为简单的实现 clients/rooms 等信息共享

// {app_root}/config/config.${env}.js
exports.io = {
  redis: {
    host: { redis server host },
    port: { redis server port },
    auth_pass: { redis server password },
    db: 0,
  },
};
开启 redis 后，程序在启动时会尝试连接到 redis 服务器 此处 redis 仅用于存储连接实例信息，参见 #server.adapter

注意： 如果项目中同时使用了 egg-redis， 请单独配置，不可共用。

部署
框架是以 Cluster 方式启动的，而 socket.io 协议实现需要 sticky 特性支持，否则在多进程模式下无法正常工作。

由于 socket.io 的设计，在多进程中服务器必须在 sticky 模式下工作，故需要给 startCluster 传递 sticky 参数。

修改 package.json 中 npm scripts 脚本：

{
  "scripts": {
    "dev": "egg-bin dev --sticky",
    "start": "egg-scripts start --sticky"
  }
}
Nginx 配置

location / {
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection "upgrade";
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_set_header Host $host;
  proxy_pass   http://127.0.0.1:7001;

  # http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_bind
  # proxy_bind       $remote_addr transparent;
}
使用 egg-socket.io
开启 egg-socket.io 的项目目录结构如下：

chat
├── app
│   ├── extend
│   │   └── helper.js
│   ├── io
│   │   ├── controller
│   │   │   └── default.js
│   │   └── middleware
│   │       ├── connection.js
│   │       └── packet.js
│   └── router.js
├── config
└── package.json
注意：对应的文件都在 app/io 目录下

Middleware
中间件有如下两种场景：

Connection
Packet
其配置于各个命名空间下，根据上述两种场景分别发生作用。

注意：

如果我们启用了框架中间件，则会发现项目中有以下目录：

app/middleware：框架中间件
app/io/middleware：插件中间件
区别：

框架中间件基于 http 模型设计，处理 http 请求。
插件中间件基于 socket 模型设计，处理 socket.io 请求。
虽然框架通过插件尽量统一了它们的风格，但务必注意，它们的使用场景是不一样的。详情参见 issue：#1416

Connection
在每一个客户端连接或者退出时发生作用，故而我们通常在这一步进行授权认证，对认证失败的客户端做出相应的处理

// {app_root}/app/io/middleware/connection.js
module.exports = app => {
  return async (ctx, next) => {
    ctx.socket.emit('res', 'connected!');
    await next();
    // execute when disconnect.
    console.log('disconnection!');
  };
};
踢出用户示例：

const tick = (id, msg) => {
  logger.debug('#tick', id, msg);
  socket.emit(id, msg);
  app.io.of('/').adapter.remoteDisconnect(id, true, err => {
    logger.error(err);
  });
};
同时，针对当前的连接也可以简单处理：

// {app_root}/app/io/middleware/connection.js
module.exports = app => {
  return async (ctx, next) => {
    if (true) {
      ctx.socket.disconnet();
      return;
    }
    await next();
    console.log('disconnection!');
  };
};
Packet
作用于每一个数据包（每一条消息）；在生产环境中，通常用于对消息做预处理，又或者是对加密消息的解密等操作

// {app_root}/app/io/middleware/packet.js
module.exports = app => {
  return async (ctx, next) => {
    ctx.socket.emit('res', 'packet received!');
    console.log('packet:', this.packet);
    await next();
  };
};
Controller
Controller 对客户端发送的 event 进行处理；由于其继承于 egg.Contoller, 拥有如下成员对象:

ctx
app
service
config
logger
详情参考 Controller 文档

// {app_root}/app/io/controller/default.js
'use strict';

const Controller = require('egg').Controller;

class DefaultController extends Controller {
  async ping() {
    const { ctx, app } = this;
    const message = ctx.args[0];
    await ctx.socket.emit('res', `Hi! I've got your message: ${message}`);
  }
}

module.exports = DefaultController;

// or async functions

exports.ping = async function() {
  const message = this.args[0];
  await this.socket.emit('res', `Hi! I've got your message: ${message}`);
};
Router
路由负责将 socket 连接的不同 events 分发到对应的 controller，框架统一了其使用方式

// {app_root}/app/router.js

module.exports = app => {
  const { router, controller, io } = app;

  // default
  router.get('/', controller.home.index);

  // socket.io
  io.of('/').route('server', io.controller.home.server);
};
注意：

nsp 有如下的系统事件:

disconnecting doing the disconnect
disconnect connection has disconnected.
error Error occurred
Namespace/Room
Namespace (nsp)
namespace 通常意味分配到不同的接入点或者路径，如果客户端没有指定 nsp，则默认分配到 "/" 这个默认的命名空间。

在 socket.io 中我们通过 of 来划分命名空间；鉴于 nsp 通常是预定义且相对固定的存在，框架将其进行了封装，采用配置的方式来划分不同的命名空间。

// socket.io
var nsp = io.of('/my-namespace');
nsp.on('connection', function(socket){
  console.log('someone connected');
});
nsp.emit('hi', 'everyone!');

// egg
exports.io = {
  namespace: {
    '/': {
      connectionMiddleware: [],
      packetMiddleware: [],
    },
  },
};
Room
room 存在于 nsp 中，通过 join/leave 方法来加入或者离开; 框架中使用方法相同；

const room = 'default_room';

module.exports = app => {
  return async (ctx, next) => {
    ctx.socket.join(room);
    ctx.app.io.of('/').to(room).emit('online', { msg: 'welcome', id: ctx.socket.id });
    await next();
    console.log('disconnection!');
  };
};
注意： 每一个 socket 连接都会拥有一个随机且不可预测的唯一 id Socket#id，并且会自动加入到以这个 id 命名的 room 中

实例
这里我们使用 egg-socket.io 来做一个支持 p2p 聊天的小例子

client
UI 相关的内容不重复写了，通过 window.socket 调用即可

// browser
const log = console.log;

window.onload = function() {
  // init
  const socket = io('/', {

    // 实际使用中可以在这里传递参数
    query: {
      room: 'demo',
      userId: `client_${Math.random()}`,
    },

    transports: ['websocket']
  });

  socket.on('connect', () => {
    const id = socket.id;

    log('#connect,', id, socket);

    // 监听自身 id 以实现 p2p 通讯
    socket.on(id, msg => {
      log('#receive,', msg);
    });
  });

  // 接收在线用户信息
  socket.on('online', msg => {
    log('#online,', msg);
  });

  // 系统事件
  socket.on('disconnect', msg => {
    log('#disconnect', msg);
  });

  socket.on('disconnecting', () => {
    log('#disconnecting');
  });

  socket.on('error', () => {
    log('#error');
  });

  window.socket = socket;
};
微信小程序
微信小程序提供的 API 为 WebSocket ，而 socket.io 是 Websocket 的上层封装，故我们无法直接用小程序的 API 连接，可以使用类似 weapp.socket.io 的库来适配。

示例代码如下：

// 小程序端示例代码
const io = require('./yout_path/weapp.socket.io.js')

const socket = io('http://localhost:8000')

socket.on('connect', function () {
  console.log('connected')
});

socket.on('news', d => {
  console.log('received news: ', d)
})

socket.emit('news', {
  title: 'this is a news'
})
server
以下是 demo 的部分代码并解释了各个方法的作用

config
// {app_root}/config/config.${env}.js
exports.io = {
  namespace: {
    '/': {
      connectionMiddleware: [ 'auth' ],
      packetMiddleware: [ ], // 针对消息的处理暂时不实现
    },
  },

  // cluster 模式下，通过 redis 实现数据共享
  redis: {
    host: '127.0.0.1',
    port: 6379,
  },
};

// 可选
exports.redis = {
  client: {
    port: 6379,
    host: '127.0.0.1',
    password: '',
    db: 0,
  },
};
helper
框架扩展用于封装数据格式

// {app_root}/app/extend/helper.js

module.exports = {
  parseMsg(action, payload = {}, metadata = {}) {
    const meta = Object.assign({}, {
      timestamp: Date.now(),
    }, metadata);

    return {
      meta,
      data: {
        action,
        payload,
      },
    };
  },
};
Format：

{
  data: {
    action: 'exchange',  // 'deny' || 'exchange' || 'broadcast'
    payload: {},
  },
  meta:{
    timestamp: 1512116201597,
    client: 'nNx88r1c5WuHf9XuAAAB',
    target: 'nNx88r1c5WuHf9XuAAAB'
  },
}
middleware
egg-socket.io 中间件负责 socket 连接的处理

// {app_root}/app/io/middleware/auth.js

const PREFIX = 'room';

module.exports = () => {
  return async (ctx, next) => {
    const { app, socket, logger, helper } = ctx;
    const id = socket.id;
    const nsp = app.io.of('/');
    const query = socket.handshake.query;

    // 用户信息
    const { room, userId } = query;
    const rooms = [ room ];

    logger.debug('#user_info', id, room, userId);

    const tick = (id, msg) => {
      logger.debug('#tick', id, msg);

      // 踢出用户前发送消息
      socket.emit(id, helper.parseMsg('deny', msg));

      // 调用 adapter 方法踢出用户，客户端触发 disconnect 事件
      nsp.adapter.remoteDisconnect(id, true, err => {
        logger.error(err);
      });
    };

    // 检查房间是否存在，不存在则踢出用户
    // 备注：此处 app.redis 与插件无关，可用其他存储代替
    const hasRoom = await app.redis.get(`${PREFIX}:${room}`);

    logger.debug('#has_exist', hasRoom);

    if (!hasRoom) {
      tick(id, {
        type: 'deleted',
        message: 'deleted, room has been deleted.',
      });
      return;
    }

    // 用户加入
    logger.debug('#join', room);
    socket.join(room);

    // 在线列表
    nsp.adapter.clients(rooms, (err, clients) => {
      logger.debug('#online_join', clients);

      // 更新在线用户列表
      nsp.to(room).emit('online', {
        clients,
        action: 'join',
        target: 'participator',
        message: `User(${id}) joined.`,
      });
    });

    await next();

    // 用户离开
    logger.debug('#leave', room);

    // 在线列表
    nsp.adapter.clients(rooms, (err, clients) => {
      logger.debug('#online_leave', clients);

      // 获取 client 信息
      // const clientsDetail = {};
      // clients.forEach(client => {
      //   const _client = app.io.sockets.sockets[client];
      //   const _query = _client.handshake.query;
      //   clientsDetail[client] = _query;
      // });

      // 更新在线用户列表
      nsp.to(room).emit('online', {
        clients,
        action: 'leave',
        target: 'participator',
        message: `User(${id}) leaved.`,
      });
    });

  };
};
controller
P2P 通信，通过 exchange 进行数据交换

// {app_root}/app/io/controller/nsp.js
const Controller = require('egg').Controller;

class NspController extends Controller {
  async exchange() {
    const { ctx, app } = this;
    const nsp = app.io.of('/');
    const message = ctx.args[0] || {};
    const socket = ctx.socket;
    const client = socket.id;

    try {
      const { target, payload } = message;
      if (!target) return;
      const msg = ctx.helper.parseMsg('exchange', payload, { client, target });
      nsp.emit(target, msg);
    } catch (error) {
      app.logger.error(error);
    }
  }
}

module.exports = NspController;
router
// {app_root}/app/router.js
module.exports = app => {
  const { router, controller, io } = app;
  router.get('/', controller.home.index);

  // socket.io
  io.of('/').route('exchange', io.controller.nsp.exchange);
};
开两个 tab 页面，并调出控制台：

socket.emit('exchange', {
  target: 'Dkn3UXSu8_jHvKBmAAHW',
  payload: {
    msg : 'test',
  },
});

