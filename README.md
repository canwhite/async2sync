# async2sync
将异步数据请求按publish的顺序串联，中间有数据传递，形成瀑布流，并且可以设置初始参数

## 引入
方法1：
>因为没有发布的npm，是以npm私包存放的，so以私包方式加载  
>npm install git+ssh://git@github.com:canwhite/async2sync.git   
>使用的时候可以自己在git上fork一下，同样格式去调用安装    

方法2:  
>直接下zip包到本地加载

方法3：
>npm包发布async2sync失败，试了一下，已经有人用了这个名字，大概二三十行代码实现了一个回调的库  
>没办法只能改名叫做asynctsync，不要问我为什么中间不用to，不好意思也被占了  
>so：  
>我们可以npm i asynctsync

## 使用
```
import AsyncWaterfall from 'async2sync'
//定义初始传入的参数
let queue = new AsyncWaterfall(['name']);
console.time('cost6');

//发布,因为是瀑布流，resolve的值可以往下传递
queue.publish_promise('1',function(name){
   return new Promise(function(resolve,reject){
       setTimeout(function(){
           console.log(name, 1);
           resolve('1');
 
       },1000)
   });
});
queue.publish_promise('2',function(data){
    return new Promise(function(resolve,reject){
        setTimeout(function(){
            console.log('this is 2 , get data form 1:', data);
            resolve('2')
        },2000)
    });
});
queue.publish_promise('3',function(data){
    return new Promise(function(resolve,reject){
        setTimeout(function(){
            console.log('this is 3 , get data from 2:',data);
            resolve()
        },3000)
    });
});
//订阅,传入初始参数，按顺序执行异步操作
queue.promise('series promise').then(res=>{
    console.log(res);
    console.timeEnd('cost6');
}).catch((err)=>{
    console.log('error',err);
    console.timeEnd('cost6');
});

/*
输出：
series promise 1 Home.vue:26
this is 2 , get data form 1: 1 Home.vue:35
this is 3 , get data from 2: 2 Home.vue:43
2 Home.vue:50
cost6：6007 毫秒 - 倒计时结束


*/


```
