---
layout: post
title: 简单iOS的APP开发记录_第四周
date:   2017-10-1
categories: "Developing_iOS"
tags: [iOS, APP]
author: yxnne
comment: true
---
>这是在我快速的开发完公司iOS 原生App后，将开发每一次的记录整理起来的。我不是一个专业的iOS开发人员，所以很多东西需要记录，包括琐碎的各方面的知识点，上线的流程，甚至语法。

### 8 / 8 / 2017
(近期人事上有变动，外加上web平台上另有任务，so，近一周iOS方面进度缓慢)
主要干了这么几件事：
1.引入Alamofire作为网络框架；
2.引入了一个toast弹窗，用法和安卓的Toast基本一致；
3.服务器配置界面完成:a.接口调用 b.界面交互和 c.配置信息持久化；
4.搞了一个InterfaceUtils，用它来管理接口，每一个方法返回接口的URL，调用方法的时候传参数进去，方法帮你拼接。
代码如下
```
let urlAvailable = InterfaceUtils.makeURL_testServerConnection(testIp: ipAdress, testPort: portNo)
            //使用AlamoFire发送请求：
            Alamofire.request(urlAvailable).responseJSON(completionHandler: {response in
                //debugPrint(response)
                if let json = response.result.value {
                    //print("\(json)")
                    //测试下toast
                    let jsonDic = json as! [String :Any ]
                    let res = jsonDic["result"] as! String
                    if "ok" == res {
                        //ok
                        ToastUtil.makeCommonToast("测试与服务器通信成功,当前配置可用!").show()
                    }else {
                        ToastUtil.makeCommonToast("测试与服务器通信失败!").show()
                    }
                    
                }else {
                    print("通信失败")
                    ToastUtil.makeCommonToast("测试与服务器通信失败!").show()
                }
            })

```
_**主要参考的文章**_

[Alamofire官方说明](https://github.com/Alamofire/Alamofire)

[Alamofire教程](http://www.jianshu.com/p/30599f64a09c)

[Toaster官方说明（类似android的一个Toast）](https://github.com/devxoul/Toaster)

[iOS沙箱机制](http://www.cnblogs.com/QianChia/p/5777480.html)

[UserDefaults持久化](http://www.jianshu.com/p/645cd8a438c8)

[Achiever归档持久化](http://www.jianshu.com/p/808d64b381a8)

[KeyChain讲解](http://blog.csdn.net/hello_hwc/article/details/45503215)


### 8 / 12 / 2017
登录页面对接完成。
增加了一个全局的AppGlobal对象，单例的，里面托管了当前用户和服务器配置。
```

import UIKit

class AppGlobal {
    
    //封装APP 的服务器信息
    var appServerIPString = ""
    var appServerPortString = ""
    static let instance = AppGlobal()
    
    //封装当前的登录的用户，用户登录成功后，给他赋值
    var currentLoginUser:User?
    
    private init() {
        
        //updateGlobalIpAndPortFromPersistentLayer()
        print("init--> \(appBaseDomin)")
    }
    
    var appBaseDomin :String{
        get{
            return "http://\(appServerIPString):\(appServerPortString)/"
        }
    
    }
    /**设置全局的IP地址和端口号*/
    func setGlobalIpAndPort(ip ipAdress:String ,port portNo :String ){
        
        appServerIPString = ipAdress
        appServerPortString = portNo
        writeIpAndPortToPersistentLayer(ip:appServerIPString ,port  :appServerPortString )
        
        
    }
    /**appDelegation中调用，（当启动程序的时候，instance是nil，所以先构造实例）
 appDelegation： AppGlobal.instance.updateGlobalIpAndPortFromPersistentLayer()
从持久层更新下appServer*/
    func updateGlobalIpAndPortFromPersistentLayer(){
    
        if let serverIP = PersistenceUtil.readUserDefaultsKeyValueString(forKey: AppConst.KEY_CONFIG_SERVER_IP){
            appServerIPString = serverIP
        }
        if let serverPort = PersistenceUtil.readUserDefaultsKeyValueString(forKey: AppConst.KEY_CONFIG_SERVER_PORT){
            appServerPortString = serverPort
        }
        print("updateGlobalIpAndPortFromPersistentLayer is called , the appBaseDomin is :\(appBaseDomin)")
    }
    
    /**向持久层中写入*/
    private func writeIpAndPortToPersistentLayer(ip ipAdress:String ,port portNo :String ){
        PersistenceUtil.writeUserDefaultsKeyValueString(forKey: AppConst.KEY_CONFIG_SERVER_IP, valueStr: ipAdress)
        PersistenceUtil.writeUserDefaultsKeyValueString(forKey: AppConst.KEY_CONFIG_SERVER_PORT, valueStr: portNo)
        
    }

}

```

_**主要参考的文章**_

[Swift中单例四种方式](http://www.jianshu.com/p/230cc900948c)

### 8 / 16 / 2017
*首页的两个接口对接完成，但是第三个接口，涉及多天的角色数据，以前的android的接口虽说可用，但是使用很麻烦，不好用。对该接口的需求明确后，交给aguii帮忙实现了。*
解析JSON数据的时候，对于IOS来说是个麻烦事情，借助框架会简单些，主要看的框架是两个：
* ObjectMapper：这个有点类似GSON或者FastJson的功能就是将Json映射成Entity
* SwiftyJson：这个大概就是将JSON转换成关联数组，直接用下标访问就好。
```
        Alamofire.request(url).responseJSON(completionHandler: {response in
            //debugPrint(response)
            if let json = response.result.value {

                let jsonJ = JSON(json)
                print(jsonJ)
                //得到viewController
                let vc = viewController as? MainPageViewController
                //1.计算总体依从率           ---------------------------------------------
                let rightTimes = jsonJ["page"]["result"][0]["rightCount"].intValue
                let wrongTimes = jsonJ["page"]["result"][0]["wrongCount"].intValue
                let rateOverall = Double(rightTimes) / Double(rightTimes + wrongTimes)
                //向viewController设置值
                vc?.depaertRateOverAll = rateOverall * 100
                
                //2.计算四大洗手时机 :接触患者前，无菌操作前，接触环境后，接触患者后--------------
                let rateBeforeCloseNick         = jsonJ["page"]["result"][0]["rateBeforeCloseNick"].doubleValue         //接触患者前
                let rateBeforeAsepticOperation  = jsonJ["page"]["result"][0]["rateBeforeAsepticOperation"].doubleValue  //无菌操作前
                let rateAfterCloseNickEnvri     = jsonJ["page"]["result"][0]["rateAfterCloseNickEnvri"].doubleValue     //接触环境后
                let rateAfterCloseNick          = jsonJ["page"]["result"][0]["rateAfterCloseNick"].doubleValue          //接触患者后
                //设置时机
                let occasionArrays = [rateBeforeCloseNick,rateBeforeAsepticOperation,rateAfterCloseNickEnvri,rateAfterCloseNick]
                vc?.occasionRateArrays = occasionArrays
                
            }else {
               
            }
        })
```

_**主要参考的文章**_


[Swift 的JSON基本知识](http://www.jianshu.com/p/9ba9f716dde1)

[使用SwiftyJson解析](http://blog.csdn.net/wuyanyanstrong/article/details/52638903)

[使用ObjectMapper](http://www.jianshu.com/p/20764fb30df2)

[SwiftyJson官方](https://github.com/SwiftyJSON/SwiftyJSON)

[ObjectMapper官方](https://github.com/Hearst-DD/ObjectMapper)


__学而不思则罔，思而不学则殆__