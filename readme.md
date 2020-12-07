# unittestreport—V1.1.9版本使用详细文档

- ###### 1.1.8新增功能：发送测试结果到钉钉群
- ###### 1.1.9新增功能：测试结果推送到企业微信群（未测试，欢迎大家提bug）

### 前言

关于unittestreport最初在开发的时候，最初只是计划开发一个unittest生成html测试报告的模块，所以起名叫做unittestreport。在开发的过程中结合使用的小伙伴的一些反馈，所以慢慢的扩展了更多的功能进去。之前在写unittestreport的时候，也陆续写了几遍关于unittestreport相关功能的使用，每次都是一个特定的功能，这边给出一遍系统的使用文档来给大家介绍一下unittestreport的功能。

- #### 关于unittestreport是什么？

    - unittestreport是基于unittest开发的的一个功能扩展库，为unittest提供了一些常用的扩展功能：

        - HTML测试报告生成
        - 测试用例失败重运行
        - 发送测试结果及报告到邮箱
        - unittest数据驱动
        - 测试结果钉钉通知
        - 测试结果推送到企业微信群
       
- #### 安装命令：

    cmd命令行下输入下面的命令进行安装

    ```
    pip install unittestreport
    ```

- #### 备注：

    - ##### 开发者：柠檬班—木森

    - ##### E-mail:musen_nmb@qq.com

    - ##### 大家在使用过程中发现bug,可以联系我，以便优化解决！

### 一、HTML测试报告生成

unittestteport中封装了一个TestRunner类，可以用来代替unittest中的TextTestRunner来执行测试用例，执行完测试用例之后会自动生成测试报告。并且有多种报告风格可选

- #### 模块导入

```python
from unittestteport import TestRunner
```

- #### 使用案例

```python
runner = TestRunner(test_suite)
runner.run()
```

- #### 关于TestRunner初始化参数

    - ######  suites: 测试套件（必传）

    - ######  filename: 指定报告文件名

    - ######  report_dir:指定存放报告路径

    - ######  title:指定测试报告的标题

    - ######  templates: 可以指定1，2，3三个风格的模板

    - ######  tester:测试人员名称

- #### 报告样式展示：

    ![image](http://testingpai.com/upload/file/2020/09/1598866900740-4967c8ad.png )

### 二、测试用例失败重运行

​	关于unittest重运行机制，unittestreport中提供了两种方式

- ##### 方式一： rerun装饰器

    - ###### 使用案例：使用rerun装饰失败需要重运行的用例，该用例失败后会自动重运行

        ```python
        from unittestreport import rerun
        
        class TestClass(unittest.TestCase):
            @rerun(count=4, interval=2)
            def test_case_01(self):
                a = 100
                b = 99
                assert a == b
        ```

    - ###### 用例运行

        ```
        runner = TestRunner(test_suite)
        runner.run()
        ```

        

    - ###### 参数说明：

        - count：用来指定用例失败重运行的次数
        - interval：指定每次重运行的时间间隔

- ##### 方式二：TestRunner.rerun方法

    - ###### 使用案例：所有的用例失败，只要有失败的用例，会自动重运行该用例

    - 用例正常编写即可

    - 运行是使用TestRunner.rerun_run方法运行

        ```python
        runner = TestRunner(suite=suite)
        runner.rerun_run(count=3, interval=2)
        ```

    - ###### 参数说明：

        - count：用来指定用例失败重运行的次数
        - interval：指定每次重运行的时间间隔

### 三、邮件发送测试报告

unittestreport内部实现了发生测试结果到邮箱的方法，执行完测试用例之后调用发送测试报告的方法即可。发邮件的方法介绍：TestRunner类中实现了send_email方法，可以方便用户，快速发送邮件。

- ##### 使用案例

    ```python
    runner = TestRunner(suite)
    runner.run()
    runner.send_email(host="smtp.qq.com",
                      port=465,
                      user="musen_nmb@qq.com",
                      password="algmmzptupjccbab",
                      to_addrs="3247119728@qq.com")
    ```

- ##### 参数介绍

    - ###### host： smtp服务器地址

    - ###### port：端口

    - ######  user：邮箱账号

    - ###### password：smtp服务授权码

    - ###### to_addrs：收件人邮箱地址（一个收件人传字符串，多个收件人传列表）

- ##### 收到的邮件样式

![image](http://testingpai.com/upload/file/2020/09/1598866521882-8470de3c.png )

### 四、数据驱动的使用

​	关于数据驱动这边就不给大家做过多的介绍了，数据驱动的目的是将测试数据和用例逻辑进行分离，提高代码的重用率，以及用例的维护，关于数据驱动本，unittestreport.dataDriver模块中实现了三个使用方法，支持使用列表(可迭代对象)、json文件、yaml文件来生成测试用例，接来分别给大家介绍一下使用方法：

- #### 1、使用介绍

    ```
    from unittestreport.dataDriver import ddt, list_data,json_data,yaml_data
    ```

    - ###### 第一步：使用ddt装饰测试用例类

    - ###### 第二步：根据使用的数据选择对应的方法进行驱动

- #### 2、使用案例

    - ##### 一、用例保存在可迭代对象中（如列表）：使用list_data

        ```python
        from unittestreport import ddt, list_data
        @ddt
        class TestClass(unittest.TestCase):
            cases = [{'title': '用例1', 'data': '用例参数', 'expected': '预期结果'}, 
                     {'title': '用例2', 'data': '用例参数', 'expected': '预期结果'},
                     {'title': '用例3', 'data': '用例参数', 'expected': '预期结果'}]
            @list_data(cases)
            def test_case(self, data):
                pass
        
        ```

    - ##### 二、用例保存在json文件中：使用json_data

        ```python
        from unittestreport import ddt,json_data
        
        @ddt
        class TestClass(unittest.TestCase):
            @json_data("C:/xxxx/xxx/cases.json")
            def test_case(self, data):
                pass
        
        ```

        - json文件中的数据格式

            cases.json文件

            ```json
            [
              {
                "title": "用例1",
                "data": "用例参数",
                "expected": "预期结果"
              },
              {
                "title": "用例2",
                "data": "用例参数",
                "expected": "预期结果"
              },
              {
                "title": "用例3",
                "data": "用例参数",
                "expected": "预期结果"
              }
            ]
            
            ```

    - ##### 三、用例保存在yaml文件中：使用yaml_data

        ```python
        from unittestreport import ddt,yaml_data
        
        @ddt
        class TestClass(unittest.TestCase):
            @yaml_data("C:/xxxx/xxx/cases.yaml")
            def test_case(self, data):
                pass
        
        ```

        - ###### yaml文件中的数据展示

            cases.yaml文件

            ```yaml
            - title: 用例1
              data: 用例参数
              expected: 预期结果
            
            - title: 用例2
              data: 用例参数
              expected: 预期结果
              
            - title: 用例4
              data: 用例参数
              expected: 预期结果
            
            ```

- #### 2、注意点：

    - 关于使用ddt的时候进行数据驱动，指定测试报告中的用例描述：

    - 测试报告中的用例描述默认使用的是用例方法的文档字符串注释，
    - 如果要给每一条用例添加用例描述，需要在用例数据中添加title或者desc字段，字段对应的数据会自动设置为测试报告中用例的描述

    ![image](http://testingpai.com/upload/file/2020/09/1598929137584-877329ac.png)





### 五、发送测试结果到钉钉

​	关于把如果测试结果发送到钉钉群，unittestreport里面进行了封装。执行完用例之后，调用TestRunner对象的dingtalk_notice方法即可。

- #### 参数介绍

    关于dingtalk_notice这个方法的参数如下，大家可以根据使用需求来进行选择。

    - ###### url: 钉钉机器人的Webhook地址

    - ######  key: （非必传：str类型）如果钉钉机器人安全设置了关键字，则需要传入对应的关键字

    - ###### secret:（非必传:str类型）如果钉钉机器人安全设置了签名，则需要传入对应的密钥

    - ###### atMobiles: （非必传，list类型）发送通知钉钉中要@人的手机号列表，如：[137xxx,188xxx]

    - ###### isatall: 是否@所有人，默认为False,设为True则会@所有人

    - ###### except_info:是否发送未通过用例的详细信息，默认为False，设为True则会发送失败用例的详细信息

- #### 案例代码：

```python
import unittest
from unittestreport import TestRunner

# 收集用例到套件
suite = unittest.defaultTestLoader.discover(CASE_DIR)
runner = TestRunner(suite)
# 执行用例
runner.run()

url = "https://oapi.dingtalk.com/robot/send?access_token=XXXXXXX"
# 发送钉钉通知  
runner.dingtalk_notice(url=url, key='钉钉安全设置的关键字',secret='钉钉安全设置签名的秘钥')

# 备注：关于钉钉群机器人的创建大家可以去看钉钉开放平台上的教程，关键字和秘钥，根据创建钉钉机器人时设置的去添加，没有设置就不需要传这个参数。
```


### 六、推送测试结果到企业微信

​	目前也有不少的公司使用企业微信办公，自动化跑完之后，测试结果需要推送到企业微信群，所以把这个功能做了一下集成（其实大家自己去写也没多少代码)。执行完用例之后，调用TestRunner对象的weixin_notice方法即可将测试结果推送到企业微信群。

- #### 参数介绍

    - ##### chatid： 企业微信群id

    - ##### access_token：调用企业微信API接口的凭证

    - ##### corpid：企业ID

    - ##### corpsecret：应用的凭证密钥

- #### 案例代码

```python
import unittest
from tests.test_case import TestClass
from unittestreport import TestRunner
# 加载用例
suite = unittest.defaultTestLoader.loadTestsFromTestCase(TestClass)
runner = TestRunner(suite=suite)
# 运行用例
runner.run()

# 推送测试结果到企业微信
# 方式一：
runner.weixin_notice(chatid="企业微信群id", access_token="调用企业微信API接口的凭证")
# 方式二：
runner.weixin_notice(chatid="企业微信群id",corpid='企业ID', corpsecret='应用的凭证密钥')
```






























