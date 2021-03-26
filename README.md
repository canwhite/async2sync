# async2sync
将异步操作转化成waterfall同步形式，适配前端有顺序的io操作

## 引入
方法1：
>因为没有发布的npm，是以npm私包存放的，so以私包方式加载  
>npm install git+ssh://git@github.com:canwhite/async2sync.git   
>使用的时候可以自己在git上fork一下，同样格式去调用安装    

方法2:  
>直接下zip包到本地加载
## 使用
```
import AsyncWaterfall from 'async2sync'
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
//订阅
queue.promise('series promise').then(res=>{
    console.log(res);
    console.timeEnd('cost6');
}).catch((err)=>{
    console.log('error',err);
    console.timeEnd('cost4');
});


```