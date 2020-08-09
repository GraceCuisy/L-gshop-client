## 今日任务
    1). 登陆 & 退出登陆 & 自动登陆
    2). 订单/支付路由跳转流程
    3). 导航守卫


## 登陆 & 退出登陆 & 自动登陆

### 登陆
    收集用户输入的mobile和password数据, 并进行前台表单校验
    提交登陆的请求
    如果失败, 提示
    如果成功, 
        将得到的用户信息(包含token)保存到vuex和local中
        跳转到首页 ==>后面需要完善

### 退出登陆
    请求后台清除后台记录的用户信息
    清除内存和local保存的用户信息

### 自动登陆
    方式一： 登陆时将用户的所有信息(用户名/token)保存到local 
        ==> 不需要额外发请求就可以(读取local中的数据)实现自动登陆
    方式二： 登陆时只将token保存到local
        ===> 需要根据local中保存的token发请求获取对应的用户信息

### 组件什么时候会自动更新?
    简单表达: 当模板中使用的响应式数据发生了任何变化
    哪些响应式数据:
        data
        props
        vuex: state与getters
        computed

        this.xxx = 123    {{xxx}}
        this.xxx = 345   不会自动更新的

### 区别：userTempId与token
    1). userTempId: 浏览器端生成， 用于标识一个未登陆的客户端， 每次请求(尤其是购物车)都携带userTempId
    2). token: 请求登陆成功时服务器返回给浏览器保存的, 用于标识这个登陆用户, 每次请求如果有token, 需要自动携带
    3). 在购物车中的应用
        1). 在未登陆前: 购物车数据的提交和获取依赖于userTempId
        2). 在登陆后: 购物车数据的提交和获取依赖于token
        3). 第一次同时携带token和userTempId获取购物时, 后台会将userTempId下的购物项转移到当前用户下下, 其实后面只需要token就可以得到所有的购物项
        如果退出登陆, 通过userTempId, 不能再得到前面与它对应的数据

## 导航守卫
### 导航守卫的理解
    vue-router提供的用来监视和控制路由跳转的一套功能技术

### 导航守卫的分类
    全局守卫: 针对任意路由跳转
        a. 全局前置守卫  router.beforeEach((to, from, next) => { })
        b. 全局后置守卫
	路由守卫: 只针对当前组件的路由跳转
		前置守卫  router.afterEach((to, from) => { })
    组件守卫: 只针对当前组件的路由跳转
        a. 进入: beforeRouteEnter (to, from, next) { },  // 注意回调函数中的this为undefined
        b. 更新
        c. 离开
### 注意:
    理解to/from/next
    前置守卫是在到达目标路由前调用
    组件的前置守卫中的this为undefined, 如果想用组件对象可以: next((component) => {})

### 导航守卫在项目中的应用
    a.只有登陆了, 才能查看交易/支付/个人中心界面
        使用全局前置守卫: router.beforeEach()
    b.只有没有登陆, 才能查看登陆界面
        使用路由独享前置守卫: beforeEnter()
        使用组件前置守卫: beforeRouteEnter()
    c.只有携带的skuId和skuNum以及sessionStorage中有skuInfo数据, 才能查看添加购物车成功的界面
    d.只能从购物车界面, 才能跳转到交易界面
    e.只能从交易界面, 才能跳转到支付界面
    f.只有从支付界面, 才能跳转到支付成功的界面

### 登陆成功后, 自动跳转到前面你想访问但因为没登陆而没有成功跳转的路由?
    在全局前置守卫强制跳转到登陆时携带目标路径的query参数: redirect=目标路径
    在登陆成功后, 跳转到redirect参数所指向的路径

## 订单/支付路由跳转流程
    购物车 => 订单交易 => 支付 => 支付成功 => 我的订单列表
    二级路由的搭建 
### 什么时候需要拆分子路由了?
    如果一个路由组件界面上有其局部界面的切换显示

        
