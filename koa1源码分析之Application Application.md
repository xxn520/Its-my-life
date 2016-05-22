##Application

<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="http://music.163.com/outchain/player?type=2&id=32507038&auto=1&height=66"></iframe>

原来写的文章，迁移过来，还是koa1的，koa2也出了，待更新。

###依赖

-    debug：debug源码分析；
-    compose_es7：与co.wrap相似，不过能够接受 async/await 函数；
-    onFinished：捕捉finish或error事件，并根据第二个参数执行回调；
-    compose：compose_es7的简化版，只接受generator；
-    isJSON：顾名思义，判断传入参数是否为JSON；
-    statuses：将http码分为三类，redirect、empty以及retry，然后根据传入的http来判断属于哪一类；
-    accepts：根据req的内容判断（content negotiation）字段，如accept,Accept-Encoding,Accept-Language等字段对这些字段进行格式化，方便后续处理；
-    only：只返回在传入参数里所包含的字段；
-    co：co源码解析

###构造器源码

<!--javascript-->
    /**
	 * Initialize a new `Application`.
	 *
	 * @api public
	 */
	function Application() {
	  if (!(this instanceof Application)) return new Application;
	  this.env = process.env.NODE_ENV || 'development';
	  this.subdomainOffset = 2;
	  this.middleware = [];
	  this.proxy = false;
	  this.context = Object.create(context);
	  this.request = Object.create(request);
	  this.response = Object.create(response);
	}

上面koa的Application的构造函数，可以看到在创建Application时有初始化如下属性：

1.    env表示是开发环境还是生产环境，通过process.env.NODE_ENV读取环境变量中的参数设置，如果没有设置默认为开发环境
2.    subdomainOffset，表示子域名的偏移，默认为2
3.    proxy如果为true，则解析 "Host" 的 header 域，并支持X-Forwarded-Host，默认为false。
4.    context上下文
5.    request请求对象
6.  response响应对象

#### 有哪些方法

#### app.listen

<!--javascript-->
	/**
	 * Shorthand for:
	 *
	 *    http.createServer(app.callback()).listen(...)
	 *
	 * @param {Mixed} ...
	 * @return {Server}
	 * @api public
	 */
	app.listen = function(){
	  debug('listen');
	  var server = http.createServer(this.callback());
	  return server.listen.apply(server, arguments);
	};

　　这个方法调用了node原生的http模块创建了一个server，要关注的是传入的这个callback。



#### app.callback
<!--javascript-->

	/**	
	 * Return a request handler callback
	 * for node's native http server.
	 *
	 * @return {Function}
	 * @api public
	 */
	app.callback = function(){
	  var fn = this.experimental
	    ? compose_es7(this.middleware)
	    : co.wrap(compose(this.middleware));
	  var self = this;
	  if (!this.listeners('error').length) this.on('error', this.onerror);
	  return function(req, res){
	    res.statusCode = 404;
	    var ctx = self.createContext(req, res);
	    onFinished(res, ctx.onerror);
	    fn.call(ctx).then(function () {
	      respond.call(ctx);
	    }).catch(ctx.onerror);
	  }
	};

　　返回一个适合 http.createServer() 方法的回调函数用来处理请求。 您也可以使用这个回调函数将您的app挂载在 Connect/Express 应用上。一点点看下来，this.middleware是中间件的一个数组，用co.wrap或者compose_es7进行包装，co是tj大神的一个用来包装执行generator函数的库，而compose_es7应该是es7可能会引入的相似功能的特性，不同的是后者支持es7的async/await。后面一句处理错误，最后是返回一个请求处理函数。在函数中会调用createContext创建上下文，用onFinished模块捕捉finish或error事件，并根据第二个参数执行回调。然后调用co包装返回的promise对象进行中间件的调用，调用就由co来进行。调用成功后执行调用respond函数。

#### 最后就来看看respond函数

<!--javascript-->

	/**	
	 * Response helper.
	 */
	function respond() {
	  // allow bypassing koa
	  if (false === this.respond) return;
	  var res = this.res;
	  if (res.headersSent || !this.writable) return;
	  var body = this.body;
	  var code = this.status;
	  // ignore body
	  if (statuses.empty[code]) {
	    // strip headers
	    this.body = null;
	    return res.end();
	  }
	  if ('HEAD' == this.method) {
	    if (isJSON(body)) this.length = Buffer.byteLength(JSON.stringify(body));
	    return res.end();
	  }
	  // status body
	  if (null == body) {
	    this.type = 'text';
	    body = this.message || String(code);
	    this.length = Buffer.byteLength(body);
	    return res.end(body);
	  }
	  // responses
	  if (Buffer.isBuffer(body)) return res.end(body);
	  if ('string' == typeof body) return res.end(body);
	  if (body instanceof Stream) return body.pipe(res);
	  // body: json
	  body = JSON.stringify(body);
	  this.length = Buffer.byteLength(body);
	  res.end(body);
	}

　　在1.0.0版本中respond函数第一行就是yield *next，也就是一开始不执行，到所有中间件执行完后对res进行处理。但co进入4.*以后，用promise代替了thunk函数的实现，返回一个promise，因此当前1.1.2版本中的respond函数的调用写在了co返回的promise的第一个回调中。

#### 其它

-    app.use向中间件数组中添加中间件
-    app.createContext创建请求上下文
-    app.inspect/toJSON返回配置的参数subdomainOffset、proxy和env。