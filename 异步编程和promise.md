# 异步编程和promise

注意文件之间的以来嵌套关系,最初被需要的文件写在最外面.

通俗理解为异步就是一个任务分成两段，先执行第一段，然后转而执行其他任务，等做好了准备，再回过头执行第二段。排在异步任务后面的代码，不用等待异步任务结束会马上运行，也就是说，**异步任务不具有”堵塞“效应**。

"异步模式"非常重要。在浏览器端，耗时很长的操作都应该异步执行，避免浏览器失去响应，最好的例子就是Ajax操作。在服务器端，"异步模式"甚至是唯一的模式，因为执行环境是单线程的，如果允许同步执行所有http请求，服务器性能会急剧下降，很快就会失去响应。

## 微任务队列

promise有许多微任务队列.比如之前是等餐十分钟,排着队等十分钟,现在是等餐十分钟,你不必站着排队等十分钟,可以在边上坐着等餐好了叫你.  也就是说你只需要等着被这个promise呼叫就行.

```javascript
//padding 准备阶段
//resolved 成功状态
//rejected 拒绝状态
new Promise((resolve, reject) => {
    resolve('操作成功');
    reject('拒绝状态');
}).then(
    value => {
        console.log("成功业务处理");
    },
    reason => {
        console.log("拒绝的业务处理");
    })
```

## 宏任务(队列)与微任务(队列)/同步(主线程)任务执行顺序

面试有可能会问到这个顺序问题....

就算是加到了微任务队列,也需要等到同步任务之后才==轮询==微任务队列,宏任务在最后.

==整个promise里面是主线程任务==.整个就是无数嵌套关系.

**整体的执行顺序是:  ==同步任务-->微任务-->宏任务==**.

```javascript
setTimeout(() => {
       console.log("setTimeout");
   },0);  //这是宏任务
new Promise((resolve) => {  //promise下面整个都是同步任务
    resolve();  //通知.then到微任务队列中
    console.log("同步一号");  //同步任务一号输出
}).then(value => console.log("成功业务处理")); //.then微任务
//此时这里的.then不会同步执行,他需要等到接收到resolve通知后才会加入到微任务队列
console.log("同步任务吼吼吼二号");  //这两个输出是同步执行
```

如果再来嵌套一下会是什么样呢?

**此时把resolve放到宏任务中,把宏任务放到同步任务中,这时的输出又是怎么样?这==是个极好的捋清楚关系的例子==**.

依然遵循同步任务到微任务到宏任务的执行顺序--------输出顺序是----同步一号--同步任务吼吼吼二号--setTimeout--成功业务处理

```javascript
new Promise((resolve) => {  //promise下面整个都是同步任务
    setTimeout(() => {
       console.log("setTimeout");
       resolve();  //通知.then到微任务队列中
   },0);  //这是宏任务
    console.log("同步一号");  //同步任务一号输出
}).then(value => console.log("成功业务处理")); //.then微任务
//此时这里的.then不会同步执行,他需要等到接收到resolve通知后才会加入到微任务队列
console.log("同步任务吼吼吼二号");  //这两个输出是同步执行
```

## promise单一状态与状态中转

**==promise的状态是单向的不可逆的不可撤销的==**,当状态形成的时候再添加更改是没有效果的,已经上路了只能把这个轮询走完.

```javascript
let p1 = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve('成功');
    },2000);  
});

new Promise((resolve,reject) => {
      resolve("fulfilled");  //状态不可逆
//    resolve(p1);  //返回的是一个promise
    
//    setTimeout(() => {
//       resolve("fulfilled");
//    },2000);  /产生微任务,如果这个状态产生时间较长,微任务出现的时间也会比较晚
}).then(
    msg => {
        console.log(msg);  //返回的p1的状态,也就是当时promise的状态.
    },
    error => {
        console.log("error" + error)  //输出:error拒绝
    }
);
console.log("qweer");
```

## promise的then方法(注意顺序)

```javascript
new Promise((resolve,reject) => {  
    resolve(); 
}).then(
    	value => {
            console.log("成功业务处理");
        },  //如果你传的是reject,不需要resolve,整个value可以写个null,
        reason => {
            console.log(reason);  //拒绝因素
        }
	);
	.then(null,a => {
        console.log(`处理了失败的状态,$a`);
    })

```

**每个then返回的也是一个promise**.

p1和p2就都是同步任务,但是此时p1的状态是已完成,p2的状态是未完成.因为p**2是在p1.then中才加入到微任务队列, then返回的也是一个promise(==这个promise只有在第二轮轮询时才会触发==)**.  注意顺序!很重要

```javascript
let p1 = new Promise((resolve, reject) => {
    resolve("fulfilled");
});
let p2 = p1.then(
    value => console.log(value),
    reason => console.log(reason)
);
console.log(p1);
console.log(p2);  //如果p1和p2在这里,p1和p2就都是同步任务,但是此时p1的状态是已完成,p2的状态是未完成.因为p2是在p1.then中才加入到微任务队列, then返回的也是一个promise(这个promise只有在第二轮轮询时才会触发)

setTimeout( () => {
    console.log(p1);
    console.log(p2);  //如果p1和p2在这里,namep1和p2就都是宏任务,此时它们的状态是相同的,都是已完成.因为按照顺序,宏任务是最后一个,此时p1和p2都已准备完毕.
})
```

### then返回值的处理技巧

有时候我们需要的东西是要一步一步给下走的,then在这时候就起到了应有的作用.

**then里面只要return了,那么后面的then就能接收value,==不管return了啥都能接收到.==但是==return了Promise之后接受的对象就不一样了==,不是之前的value了,是一个新的value**.

==**后面的then都是对前面返回的Promise的处理**==.

then如果不手动返回一个新的promise的话,**默认都当成成功状态**进入下一个then.

```javascript
let p1 = new Promise((resolve, reject) => {
        reject('失败');
}).then(
	value => {
        return "houdun"
    },
    reason => console.log("error" + reason)
).then(value => {
    console.log(value); 
    //这里就可以接到第一个then返回的value值
});


let p1 = new Promise((resolve, reject) => {
    setTimeout(() => {
        reject('失败');
    },2000); 
}).then(null,r => {
    console.log(r);
})  //如果只写了reject/resolve,那么必须要加then,给一个结果,不然会报错的嘞.


let p1 = new Promise((resolve, reject) => {
    resolve("success");
}).then(value => {
    return new Promise((resolve ,reject) => { 
    	resolve("解决了");  //返回的是一个Promise
    });
}).then(value => {
    console.log(value)  //这里接收到第一个value的值:解决了 因为return的是一个Promise,所以接收到的value改变了,是现在这个return的Promise的值
});
```

## 其他类型的Promise的封装

就是有then方法的对象会被自动转换为promise.

**class方法**

```javascript
let p1 = new Promise((resolve, reject) => {
    resolve("success");
}).then(value => {
    class hd{  //封装的class方法
        then(resolve, reject) { 
        //这里返回的这个then拥有和Promise有相同的作用
        	setTimeout(() => {
        		resolve('这是对象');
    		},2000); 
    	}
    }
    return new hd();
}).then(value => {
    console.log(value)
```

**静态方法**

```javascript
.then(value => {
    return class {
        static then(resolve, reject) {   //封装的静态方法
            resolve("这是一个静态方法")
        }
    },
    reason => {}
}.then(value => {
    console.log(value)
	},
    reason => {
       console.log(reason);  
    })
```

## 使用Promise封装Ajax异步请求

之前的使用都是让then立即执行,但是实际却不会这样做.解决的方法就是封装为函数.

避免回调函数,一层一层操作,结构更清晰更易操作.

```javascript
function request() {
    return new Promise((resolve, reject) => {
        resolve('成功');
    });
}
request().then(value => console.log(value));  
//Promise封装成函数
```

```javascript
//使用Promise封装Ajax异步请求
function ajax(url) {
    return new Promise((resolve, reject) => {
        let xhr = new XMLHttpRequest();
        xhr.open("GET",url);
        xhr.send();
        xhr.onload = function () {
            if (this.status == 200){
                resolve(JSON.parse(this.response));
            }else {
                reject("加载失败");
            }
        }
        xhr.onerror = function () {
                reject(this);  //发生的其他错误
        }  //onerror事件会在文档或图像加载过程中发生错误时被触发。
     });
}

//这是后台请求
let url = `http://localhost:8888/php`;
ajax(`${url}/user.php?name=nnnn`).then(value => {
    console.log(value);  //先获取用户,再使用then来接收到请求返回来的值
},
reason => {
    console.log(reason);
});

let url = `http://localhost:8888/php`;
ajax(`${url}/user.php?name=nnnn`).then(user => {
    return ajax(`${url}/houdunren.php?id=${user.id}`);
}).then(lessons => {
    console.log(lessons);
});

//结构优化之后
let url = `http://localhost:8888/php`;
ajax(`${url}/user.php?name=nnnn`)
    .then(user => ajax(`${url}/houdunren.php?id=${user.id}`))
    .then(lessons => console.log(lessons))
```

## Promise多种错误检测以及catch的使用

一般推荐把catch放到最后面,这时候可以把前面的Promise统一错误处理.

**如果catch前面没有错误回调函数处理,那么catch之前的所有错误都会交由catch处理**.如果你想**对某一个Promise单独处理错误,那么就在这个Promise后放`reason`回调进行单独处理**,否则错误会全部堆积到之后的catch上.

```javascript
new Promise((resolve, reject) => {
        reject(new Error("promise fail"));  
    //自定义的错误会提交
    	throw new Error("fail");  //直接抛的错误也会提交给reason回调函数
    	hd +1 ; //一个系统的语法错误也会被捕捉接收到
    	reject("rejected") //这个错误也会被提交给reason回调
    }).then(
		value => {return new Promise((resolve, reject) => { 
    					reject("p2")
				}), //如果这个value里又有一个Promise,它的错误就放到下一个then里
		reason => {console.log(reason.message)}
		}).then(value => {
            console.log(value);
        }).catch(error => {
            console.log(error);  
 //接收到reject("p2")这个错误;如果第一个then里没有对错误接收的reason,这里也能接收到最上面的错误.
        })
```

### 自定义错误

如果在Ajax中定义了两个错误类,那么这两个错误类同时进行,可以在catch中if分情况设置.

==**必须同步使用,异步请求不可以**==.

```javascript
//请求
    let url = `ttp://localhost:8888/php`;
    ajax(`${url}/user.php?name=后盾人`)
        .then(value =>{
        console.log(value)
        })
    .catch(error => {
        if (error instanceof ParamError){
            console.log(error.message);
        }
        if (error instanceof HttpError){
            alert(error.message);
        }
    })
//在Ajax中定义了两个错误类,两个错误类同时进行,可以在这个catch中if


//ajax
class ParamError extends Error{
    constructor(msg){
        super(msg);
        this.name = "ParamError";
    }  //继承内部错误类,拿到消息
}
class HttpError extends Error{
    constructor(msg){
        super(msg);
        this.name = "HttpError";
    }  //继承内部错误类,拿到消息
}
function ajax(url) {
    return new Promise((resolve, reject) => {
        if(!/^http/.test(url)){
            throw new ParamError("请求地址格式错误") 
            //这里还是同步的
        } //如果不是http开头就报错ParamError类"请求地址格式错误"
        let xhr = new XMLHttpRequest();
        xhr.open("GET",url);
        xhr.send();
        xhr.onload = function () {  //这里是异步网络请求,所以 else if下面要用reject同步,不能直接用throw new
            if (this.status == 200){
                resolve(JSON.parse(this.response));
            }else if (this.status == 404){
                reject(new HttpError("用户不存在")) 
                //像这种必须同步使用,异步请求不可以
            } else {
                reject("加载失败");
            }
        }
        xhr.onerror = function () {
            reject(this);  //发生的其他错误
        }  //onerror事件会在文档或图像加载过程中发生错误时被触发。
    });
}
```

## 使用finally实现异步加载动画

finally无论怎样都会加载

```javascript
 ajax(`http://localhost:8888/php/user.php?name=后盾人`)
    .then(value => console.log(value))
    .finally(() => loading.style.display = "none")
    // const promise = new Promise((resolve, reject) => {
    //     resolve("hdcms");
    // }).then(msg => {
    //     console.log("resolve");
    // }).finally(() => {
    //     console.log("永远会执行,无论成功失败")
    // })

 return new Promise((resolve, reject) => {
        loading.style.display = 'block'; 
 })
```

## Promise动态加载图片

```javascript
//加载图片
function loadImg(src) {
    return new Promise((resolve, reject) => {
        const image = new Image();
        image.src = src;
        image.onload = () => {
            resolve(image);
        };
        image.onerror = reject();
        document.body.appendChild(image);
    })
}
loadImg('') //图片地址
.then(image => {
        image.style.border = "solid 6px red";
    })
```

## 封装timeout定时器

在promise里封装好,直接调用.

```javascript
//封装timeOut定时器
    function timeout(delay = 1000) {
  return new Promise(resolve => setTimeout(resolve,delay))
    }
    timeout(2000)
    .then(() => {
        console.log("houdunren.com");
        return timeout(2000);  //在promise里封装好,直接调用
    })
```

## 构建扁平化的setInterval

如果setInterval嵌套多层则会使得代码繁重复杂,Promise可以解决这个问题.

在封装好的setInterval中写入效果,不需要使用嵌套关系了,使其变得扁平化.

```javascript
    //封装setInterval
    function interval(delay = 1000,callback) {
        //回调函数决定终止
        return new Promise(resolve => {
            let id = setInterval(() => {
                callback(id, resolve);
            }, delay);
        });
    }

        interval(100,(id,resolve) => {
            const div = document.querySelector("div");
            let left = parseInt(window.getComputedStyle(div).left)
            div.style.left = left + 10 + "px";  //向右移动
            if (left >= 200) {
                clearInterval(id);  //清除id
                resolve(div)  //更改Promise状态
            }
        }).then(div => {  //接到div了嗷
                interval(100, (id,resolve) => {
                    let width = parseInt(window.getComputedStyle(div).width)
                    div.style.width = width - 10 + "px";  //宽度变窄
                    if (width<= 200) {
                        clearInterval(id);  //清除id
                        resolve(div);  //更改Promise状态
                    }
                });
            })
        .then(div => {
            div.style.backgroundColor = "red"; //更改为红色
        })
```

## script脚本和Promise加载引擎

详见:test01_异步编程和Promise_script脚本的Promise加载引擎&yilai.js&beiyilai.js

封装依赖函数对被依赖的调用

```javascript
//依赖的函数
function houdunren() {
    beiyilai();
    console.log("houdunren.js");
//在这个函数里面调用beiyilai里的函数,把它封装成Promise,在'script脚本的Promise加载引擎.html'中
}
```

```javascript
//被依赖的函数
function beiyilai() {
    console.log("beiyilai.js");
}
```

```javascript
//封装script
function loadScript(src) {  //定义函数
    return new Promise((resolve, reject) => {  //返回一个Promise
        const  script = document.createElement('script');  //定义一个脚本节点
        script.src = src; //指定src属性
        script.onload = () => resolve(script)  //指定加载成功后的函数
        script.onerror = reject; //指定加载失败后的函数
        document.body.appendChild(script);  //放到body中去
    })
}

//使用Promise加载script
loadScript("beiyilai.js")  //加载被依赖的包:beiyilai.js
    .then(script => {
    return loadScript("yilai.js");  //返回依赖的包
})
    .then((script => {
    houdunren();  //执行依赖的包:yilai里的函数
}))
```

## Promise.resolve-缓存后台数据

​		有时候写单页面时,可能会在不同页面请求相同数据,**当==请求相同数据==**时,我们希望**不要反复请求后台,而去走本地缓存**,减少走后台的次数,减少服务器压力也使服务器响应变快.

走了缓存就意味着**没有**发送异步请求.

`Promise.resolve("bbbbb")`是成功状态

```javascript
<script src="ajax.js"></script>
<script>
   // function hd() {}  //他是个对象
   // hd.site = "houdunren.com";  //给一个属性
   // console.dir(hd);
    function query(name) {  
        const cache = query.cache || (query.cache = new Map());
        if (cache.has(name)){  
            //检测缓存里有没有数据,有就直接返回
            console.log("走缓存了");
            return Promise.resolve(cache.get(name))
        }
        return ajax(`http://localhost:8888/php?name=${name}`)
            //写上要请求的用户
            .then(user => {
            cache.set(name,user); //把值放到里面
            console.log("没走缓存");
            return user;
        })
    }

    //请求
    setTimeout(() => {
        query('后盾人').then(user => {
            console.log(user);
        })
    },1000)
</script>
```

## Promise.reject

**==直接返回或抛出异常==**来改变本次Promise的状态.

```javascript
let he = {
    then(resolve,reject){
        resolve("hh");
    }
};
Promise.resolve(he).then(value => {
    console.log(value);
});

//改变单次失败状态的方法
new Promise((resolve, reject) => {
    resolve("houdun")
}).then(value => {
    if (value != "成功"){
        throw new Error("fail");  //抛出异常
        return Promise.reject("参数错误");  //直接返回也是可以的
    } //直接返回或抛出异常来改变本次Promise的状态
})
    
.catch(error => {
    console.log(error + "fail");
})  //对总体状态有影响
// Promise.reject("fail").then(value => {
//     console.log(value);
// }).catch(error => {
//     console.log(error);
// })
```

## promise.all-批量获取-已解决状态

**==promise.all返回的全部状态都是已解决状态==**.

批量获取原理:

```javascript
    const one = new Promise((resolve, reject) => {
        setTimeout(() => {
            //resolve("第一个异步");
            reject("fail");
        },1000);
    }).catch(error => {
        console.log(error);  //这里已经对reject有所处理,所以整体返回的是一个解决状态
    });  //catch是对reject处理,它处理之后本身也会返回一个解决状态,所以在all里面就是成功状态
    const two = new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve("第二个异步");
        },1000);
    });
    Promise.all([one,two])
        .then(result => {
            console.log(result);
        })
        .catch(error => {
            console.log(error);
        })
```

举例-批量获取用户资料:

可以把用户信息和地址什么的一起const,然后获取资料;也可以**把地址这堆东西封装成函数**,然后输入用户信息获取资料.还是后者好一点.

```javascript
    //例子
    //批量获取用户资料
   const promise = ['后盾人','向军'].map(name => {
       return ajax(`http://localhost:8888/php?name=${name}`)
   });
    Promise.all(promise).then(users => {
        console.log(users);  //批量获取到信息了
    });

    //也可以封装成函数再拿用户资料
    function getUsers(names){
        return names.map(name => {
            return ajax(`http://localhost:8888/php?name=${name}`)
        })
    }
    getUsers(['后盾人','向军']).then(users => {
        console.log(users);  //批量获取到信息了
    });
```

## Promise.allSettled接口-批量获取-任意状态

这个接口和all接口不一样的就是:这个接口可以不是都是已解决状态,可以是拒绝状态.

```javascript
   //原理
    const p1 = new Promise((resolve, reject) => {
        resolve("resolved");
    });
    const p2 = new Promise((resolve, reject) => {
        reject("rejected");
    });
    Promise.allSettled([p1,p2]).then(results => {
        console.log(results);
    })  //这个接口可以不是都是已解决状态,可以是拒绝状态
```

```javascript
    //例子
    const promise = ['后盾人','li'].map(name => {
        return ajax(`http://localhost:8888/php?name=${name}`)
    });
    Promise.allSettled(promise).then(value => {
        console.log(value);   //即使用户信息"li"不存在, 也不影响,照样返回数据
        let users = value.filter(user => {  //过滤
            return user.status == "fulfilled";  //返回这个状态的,没有就不要了
        });
        console.log(users);  //返回成功接入的用户的值  这个数据就可以渲染到页面什么的啦
    })
```

## Promise.race接口-获取-以快为准

这个接口就是,**哪个返回的快,先改变状态,就用哪个的数据**.

原理

```javascript
 //原理
    const one = new Promise((resolve, reject) => {
        setTimeout(() => {
            //resolve("第一个异步");
            reject("fail");
        },2000);
    }).catch(error => {
        console.log(error);  //这里已经对reject有所处理,所以整体返回的是一个解决状态
    });  
    const two = new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve("第二个异步");
        },1000);
    });
    Promise.race([one,two])  //这个接口就是,哪个返回的快,先改变状态,就用哪个的数据
        .then(results => {
            console.log(results);
        })
    .catch(msg => {
        console.log(msg);
    })
```

举例

```javascript
    //举例
    let promise = [
        ajax(`http://localhost:8888/php?name=后盾`),
        new Promise((resolve, reject) => {
            setTimeout(() => {
                reject("请求超时")
            },2000)
        })
    ];  //目的是后台给信息超过两秒就发送请求失败的结果
    Promise.race(promise)
    .then(value => {
        console.log(value);
    })
    .catch(error => {
        console.log(error);
    })
```

封装使用

```javascript
//封装
    function query(url,delay=2000) {
        let promise = [
            ajax(url),
            new Promise((resolve, reject) => {
                setTimeout(() => {
                    reject("请求超时");
                },2000);
            })
        ];
        return Promise.race(promise)
    }
    //使用
    query(`http://localhost:8888/php?name=后盾`,1000).then(value => {
        console.log(user);
    })
    .catch(error => {
        console.log(error)
    })
```

## Promise队列

### 队列原理

下一个Promise要等到这个Promise完了之后再走.

返回新的Promise,执行完之后才能执行下一个promise.

队列中的每一个成员都有自己的Promise,==**下一个成员依赖上一个成员的状态的改变**==.

```javascript
let promise = Promise.resolve("hou");
promise.then(v => {
    return new Promise(resolve => {
        setTimeout(() => {
            console.log(1);
            resolve();  //如果不写这个resolve来完成这个Promise,下一个then就永远无法执行
        },1000)
    })
}).then(v => {
    console.log("ac");
//本来这两个then是同时处理的,但是上一个then里又加了个新的Promise,所以只能等到上一个then中的Promise完成,才能处理这个then中的数据
})
```

### 使用Map封装实现Promise队列

map封装队列,**==重点在于要给promise赋值==**,如果不赋值的话,处理的其实是统一对let的promise进行处理.**赋值后,就是把下面new的promise返回给这个变量**,然后在下一次循环走这个promise的时候得等到上一个promise的完成.

```javascript
//使用Map封装Promise队列
function query(num) {
    let promise = Promise.resolve();
    num.map(v => {
        promise = promise.then(_ => {  //重点在于要给promise赋值,如果不赋值的话,处理的其实是统一对let的promise进行处理,就不会有setTimeout的效果了,会一起出来
//现在赋值了,就是把下面new的promise返回给这个变量,然后在下一次循环走这个promise的时候得等到上一个promise的完成
            return new Promise( resolve => {
                setTimeout(() => {
                    console.log(v);
                    resolve();
                },1000);
            });
        });
    })

}
query([1,2,3,4,5]);
```

使用map封装的队列实用小例

根据队列,间隔1秒依次输出p1,p2.

```javascript
//小例
function queee(num) {
    let promise = Promise.resolve();
    num.map(v => {
        promise = promise.then(_ => { 
            return v(); //返回的是这个函数呢
        });
    })
}

function p1() {
    return new Promise( resolve => {
        setTimeout(() => {
            console.log("p1");
            resolve();
        },1000);
    });
}
function p2() {
    return new Promise( resolve => {
        setTimeout(() => {
            console.log("p2");
            resolve();
        },1000);
    });
}
queee([p1,p2]);
```

### 使用reduce封装实现Promise队列

==核心原理,在then中返回promise==.

```javascript
//reduce封装Promise
function que(num) {
    num.reduce((promise,n) => {
        return promise.then(_ => {  //核心原理,在then中返回promise
            return new Promise(resolve => {
                setTimeout(() => {
                    console.log(n);
                    resolve();
                },1000);
            })
        })
    })
}
que([1,2,3,4])
```

## 使用队列渲染数据

**使用队列渲染数据,可以简化操作**.比如说你想批量搞个什么事情,就可以采用队列.

每个用户处理时包含两步操作:**==1.获取到数据;2.渲染到页面==**.

```javascript
class User {
    ajax(user){
        let url = `http://localhost:8888/php/user.php`
        return new Promise((resolve, reject) => {
            loading.style.display = 'block';//让样式显示出来
            if(!/^http/.test(url)){
                throw new ParamError("请求地址格式错误")
            } //如果不是http开头就报错ParamError类"请求地址格式错误"
            let xhr = new XMLHttpRequest();
            xhr.open("GET",`${url}?name=${user}`);
            xhr.send();
            xhr.onload = function () {  //这里开始是异步网络请求,所以 else if下面要用reject同步,不能直接用throw new
                if (this.status == 200){
                    resolve(JSON.parse(this.response));
                }else if (this.status == 404){
                    reject(new HttpError("用户不存在"));  //必须同步使用,异步请求不可以
                } else {
                    reject("加载失败");
                }
            }
            xhr.onerror = function () {
                reject(this);  //发生的其他错误
            }  
        });
    }  //获取到数据

    //渲染到页面
    render(users){
        users.reduce((promise,user) => {
            return promise.then(_ => {
                return this.ajax(user);
            }).then(user => {
                return this.view(user)
            })
        },Promise.resolve())
    }
    view(user){
        return new Promise(resolve => {
            console.log(user);
            let h2 = document.createElement('h2')
            h2.innerHTML = user.name
            document.body.appendChild(h2)  //已经添加完毕
            resolve()
        })
    }
}
new User().render(["后盾人","向军"]);
//使用队列渲染数据,可以简化操作.比如说你想批量搞个什么事情,就可以采用队列
```

## asnyc与await语法糖

**语法糖就是让语法变得更加舒服简洁**,好像吃糖一样甜甜的感觉.

==await是`then`的语法糖==,就是then的简写嘛.

`asnyc`类似等同于`new Promise`的感觉,应该是差不多的作用.

asnyc用于声明一个function是异步的.

await只能用于asnyc中.

```javascript
async function hd() {
    let name = await new Promise(resolve => {
        resolve('hou')
    });  //await就是then的语法糖,省去了then后面v=>{}那一堆,直接可以new Promise了.
    console.log(name);
}
hd()
```

### asnyc与await执行异步

```javascript
//例子  延迟函数
//封装
async function sleep(delay=2000) {
    return new Promise(resolve => {
        setTimeout(() => {
            resolve();
        },delay);
    })
}
//调用 显示:每隔2秒出来
async function show() {
    for (const inter of [1,2,3]){
        await sleep(); //这轮不结束进不到下轮里去
        console.log(inter);
    }
}
show();  //调用函数
```

### await制作加载进度条

如果需要加以复用的话

```javascript
<style>
    #loading{
        height: 50px;
        background-color: #1b6d85;
        display: flex;
        justify-content: center;
        font-size: 30px;
        color: #fff;
    }
</style>
<div id="loading">0%</div>
//例子 await制作加载进度条
//可以当封装
function query(name) {
    return ajax(`http://localhost:8888/php/user.php?name=${name}`);
}
async function loadUser(users) {
    for (let i=0;i<users.length;i++){
        let user = await query(users[i]);
        let progress = ((i+1)/users.length)*100
        loading.style.width = progress+ "%";
        loading.innerHTML = Math.round(progress)+ "%";
        //由于数据多的话有小数的情况,加上Math.round就没问题.
        console.log(i);
    }
}
loadUser([1,2,3,4,5,6])  //数据
```

如果是不需要封装,而**是单次执行**的话:

```javascript
function query(name) {
    return ajax(`http://localhost:8888/php/user.php?name=${name}`);
}
(async () => {
    let users = [1,2,3,4,5,6]
    for (let i=0;i<users.length;i++){
        let user = await query(users[i]);
        let progress = ((i+1)/users.length)*100
        loading.style.width = progress+ "%";
        loading.innerHTML = Math.round(progress)+ "%"; 
    }
})();
```

## class与await结合使用/封装

```javascript
class User {
    constructor(name){
        this.name = name;
    }
    then(resolve,reject){
        let user = ajax(`http://localhost:8888/php/user.php?name=${name}`;)
        resolve(user)
    }
}
async function get() {
    let user = await new User("hou")
    console.log(user);
}
get()
//如果一个类中包含then时,会包装成一个promise.
```

封装

```javascript
//封装
class User{
    async get(name){  //如果想要加载完后,就要用到asnyc和await
        let user = await ajax(`http://localhost:8888/php/user.php?name=${name}`);
        user.name+='-hou';
        return user ;
    }  //不能先执行再加载,要先全部加载
}
new User().get('hou').then(user => {
    console.log(user);
})
```

## async和await的多种声明方式

1.`asnyc function get(name){ }`声明.

```javascript
asnyc function get(name){ 
	let user = await ajax(`http://localhost:8888/php/user.php?name=${name}`)
}
get('hou')  //获取
```

2.`let hd={ }`变成对象声明.

调用时以对象形式调用.

```javascript
let hd={ 
	async get(name){
       return await ajax(`http://localhost:8888/php/user.php?name=${name}`);
    }
}
hd.get('hou')  //调用
```

3.`class User{ }`封装成一个类

```javascript
class User{
    async get(name){
       return await ajax(`http://localhost:8888/php/user.php?name=${name}`);
    }
}
new User().get('hou').then(user => {  //调用
    console.log(user);
})
```

## asnyc和await的基本错误处理

之前写错误处理就直接catch,asnyc也有相似的用法特性.

不管是自定的还是抛出的错误都能接到,异步里的错误也可以接到.

```javascript
//之前写错误处理就直接catch,asnyc也有相似的用法特性
new Promise((resolve, reject) => {
    throw new Error("fail");  //不用直接error的错误也能被catch捕捉到,之前讲过
}).catch(error=>{
    console.log(error);
});

//async对错误的处理
async function hd(name) {
    //console.log(a);
    //throw new Error("fail"); //不管错误是自定的还是自己抛的错误,都能接到
    return ajax(`http://localhost:8888/php/user.php?name=${name}`)  //在异步里也可以
}
hd('hou')
    .then(user => {
    console.log(user);
})
    .catch(error => {
    console.log(error + "Error");
})
```

## 标准await错误处理流程

```javascript
async function hd(name) {
    try {    //try catch  在内部进行错误处理  可以放入多个错误
        let user = await ajax(`http://localhost:8888/php/user.php?name=${name}`)
        return user;
    } catch(error) {
        console.log(error.message);  //执行处理错误
        }
    console.log(333);  //执行完上一个错误后才能继续
}


hd('hou').then(lesson => {  //这是ajax外部

}).catch(error => {

})  //建议在内部错误处理,自己处理自己的错误,不要堆在一起,太耦合了
```

## await并行执行技巧

p1,p2没有任何关系,但是我们还要用await,又不想顺序执行,这时就要用到并行开发,其实最简单的方法就是用all接口,把[p1(),p2()函数放在数组里.

```javascript
async function hd() {  //await需要等待执行
    let h1 = await p1()
    let h2 = await p2()  //现在是p1完了之后再执行p2
}
hd();  //调用

async function hd() {
    let res = await Promise.all([p1(),p2()])
    console.log(res)  //这是后就会同时接到p1,p2
}
//p1,p2没有任何关系,但是我们还要用await,又不想顺序执行,这时就用到并行开发,就是用all
```

