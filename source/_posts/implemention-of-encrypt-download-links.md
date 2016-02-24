title: Python web 加密下载链接的实现
date: 2015-12-03 18:22:28
tags:
  - Python
  - Web
---
## 需求背景
文件上传下载绝对是Web攻防中一个重要的突破口。网络安全日益受重视的今天，web开发中除了要对文件上传进行严格的权限控制，文件下载亦是如此。不可随意暴露服务器端信息，以及用户信息。
其他技术栈的网站也可以作参考。

## 实例
设有银行网站 WEB_SERVER , 另有银行每月交易流水日志文件服务器 LOG_SERVER , 用户在 BANK_WEB 的 “个人中心” 里可以 “下载流水日志”。
用户点击 “下载流水日志” 后，选择想要流水日志的年月，WEB_SERVER 返回的是一个加密的下载链接，此链接可以由浏览器当时执行，也可放在下载工具中下载。也有如下安全性要求。

* 用户无法构造文件的URL
* 不直接暴露文件的绝对路径
* 开始下载之前不暴露文件名
* 可以进行权限控制，无权限者无法下载
* 下载链接有过期时间
* 还可以实现其他你可以想到的功能
* 文件下载服务器只需提供文件，无须管理用户信息
<!--more-->

## 具体实现
1. 设计URL
  如下形式的URL作为下载链接给用户：
    ```
    http://file.logserver.com/$ENC_MSG
    ```
  其中，`$ENC_MSG`是一条加密消息。由于需要真实的文件信息、用户权限信息、过期信息等都需要从加密消息中解密出来，所以加密算法必须时**可逆且可靠的**。我们采用**AES**加密算法。

2. 生成作为AES加密key的字符串
  主要利用用户留在`WEB_SERVER`服务端的私密信息（无法公开获得或构造）生成， 由于AES解密时需要相同的key，故此key应该可以再次计算出来。

   ```python
   import hashlib
   import hmac
   key = hmac.new(
       account+secret,
       '|'.join([user_id, reg_time, account_id]),
       hashlib.sha1
   ).hexdigest()[:32]    # AES的key最长接受32字节(256 bit)
   ```

3. 生成加密消息 $ENC_MSG
   AESCipher类基于pycrypto库的AES类实现, 可以参考此实现在AES加密解密过程中添加额外的处理。比如我们增加了一层base64算法。

   ```python
   import base64
   from Crypto import Random
   from Crypto.Cipher import AES

   class AESCipher(object):
       def __init__(self, key):
           self.bs = 32
           self.key = key

       def encrypet(self, raw):
           raw = self._pad(raw.encode('UTF-8'))
           iv = Random.new().read(AES.block_size)
           cipher = AES.new(self.key, AES.MODE_CBC, iv)
           return base64.b64encode(iv + cipher.encrypt(raw))

       def decrypt(self, enc):
           enc = base64.b64decode(enc)
           iv = enc[:AES.block_size]
           cipher = AES.new(self.key, AES.MODE_CBC, iv)
           return self._unpad(cipher.decrypt(enc[AES.block_size:])).decode('UTF-8')

       def _pad(self, s):
           return s + (self.bs - len(s) % self.bs) * chr(self.bs - len(s) % self.bs)

       def _unpad(self, s):
           return s[:-ord(s[len(s)-1:])]

   enc_msg = AESCipher(key).encrypet(
       '|'.join([user_id, account_id, month, log_type, expire]))
   ```

4. 将以上URL提供给用户供用户下载，用户向文件服务器 LOG_SERVER 发起请求
5. 文件服务器先从URL中提取出加密消息，转发给 WEB_SERVER 并等待其反会结果
6. WEB_SERVER得到加密消息，解密，获得用户信息，进行各项参数的合法性校验
   WEB_SERVER 按照上面的加密方式，构造出相同的key，解密 $ENC_MSG , 得到 user_id, account_id, month, log_type, expire 几个参数。
   user_id: 用户身份标识，可以校验用户是否具有某种条件，如日志下载权限
   account_id: 账户标识，可以检查该账户是否具有某种条件
   month: 要下载日志的月份，如201511
   log_type: 日志类型，或者是文件基于某种特征的分类
   expire：过期时间的unix时间戳，和当前时间对比，看是否该下载链接已经过期
7. 如果校验通过，WEB_SERVER 则构造合法的日志文件路径，将此路径写入 HTTP 头的 `X-Accel-Redirec` 字段返回给文件服务器。例如：
    ```
    X-Accel-Redirect: /logs/62111644023456868108/custom-201511.log.gz
    ```
8. 文件服务器收到该响应后，输出真实文件给用户

## 交互时序图
![加密下载链接交互时序图](http://7xn3rj.com1.z0.glb.clouddn.com/static/images/encryption-download-url.png)

## 扩展思考
* 如何生成一次性加密下载链接
* 如何更有效地隐藏文件服务器的真实地址
* 用户直接构造最终的真实文件URL来访问文件地址怎么办
