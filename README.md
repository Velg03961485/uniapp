# uniapp
how create app Android / ios  from  uniapp


### 非固定级别滑动选择器-picker


·备注：因为滑动选择器一般是固定的同深度树状接构，这里做了一些处理
保证在一级、二级、三级 不同深度都可选择（uniapp）


依赖组件 picker




### uniPush遇坑总结



##### 1.程序必须开启推送设置


manifest.json配置中


APP SDK配置  找到推送选择 勾选（下方有说明文档可以打开看看）


APP模块权限配置


勾选push 消息推送选项


（这两项至关重要，同时要打包或者重新运行才能生效）



##### 2.dcloud设置


点击右上角的登陆者 认证开发者（当天就能审核通过）


应用列表，找到要添加推送消息的应用，点击名称进去-有一个Uni Push


消息通知栏  填写通知标题  通知内容   点击发送预览


下方会有一个测试预览 填写CID （测试使用，可不用填写）


当预计人数 有人数的时候  才能点击确认 


##### 3.CID


CID 为用户登录APP 产生的手机型号ID 唯一标识，这里用来推送消息的唯一值


CID 必须要在正式的包下 才能获取 （这里测推送测试 也是一直在正式包下面进行的）



```
var info = plus.push.getClientInfo();
console.log( JSON.stringify( info ) );
```


在APP.vue 的onLaunch中获取CID （有些安卓手机 获取的CID为null-极少数的有这种情况）


因为做推送测试的时候要填写CID，建议得到的CID信息 显示在页面上面  方面记录


(如果 不选择测试CID推送的话  只要在推送确认页 有预计人数，表明就是注册成功的 就可以执行推送了)



##### 4.监听推送消息的点击


APP.vue中


```
plus.push.addEventListener('click', (res) => {  
    console.log("payload-->" + JSON.stringify(res))  
    uni.showModal({
        content: JSON.stringify(res),
        showCancel: false
    });
});  
plus.push.addEventListener('receive', (res) => {  
    console.log("payload-->" + JSON.stringify(res))  
    uni.showModal({
        content: JSON.stringify(res),
        showCancel: false
    });
});
```
                    








