## 传输层

---

### 代号

**服务端** - *Server*

**客户端** - *Client*

**客户端唯一ID** - *ClientID*

**客户端RSA公钥** - *ClientPublic*

**客户端RSA私钥** - *ClientPrivate*

**服务端RSA公钥** - *ServerPublic*

**服务端RSA私钥** - *ServerPrivate*

**AES密钥** - *DataKey*

---

### 设计思路

- 每次打开浏览器打开网站时，*Client*与*Server*均会进行一次“握手协议”，用于安全地交换密钥用于后期的加密传输。
- 双方的RSA私钥均被保存在自己手里，用于解密或签名，而经过握手后，双方都会拥有对方的RSA公钥。RSA加密性能很差，所以被用于加密每次临时*DataKey*，而AES加密性能良好，被用于加密明文数据。在传输数据时，先使用*DataKey*加密明文数据，随后使用对方公钥加密*DataKey*后传输加密的*DataKey*和密文。对方收到后使用自己的私钥解密*DataKey*，随后使用*DataKey*解密得到明文。
- 日常的数据传输时，*DataKey*的随机生成规则为**IP+随机部分**，IP地址部分在一定程度上保证了数据不被抓取并被异地传输，随机部分保证了*DataKey*的不可预测性。
- 在第一次握手时，*Client*应该生成一个*ClientID*便于*Server*区分，随机算法应该最大程度保证生成的*ClientID*不重复。
- 非对称加密算法使用RSA-OAEP 2048bit，对称算法使用AES-256-CBC。

---

### 流程

#### 握手

1. *Client*生成一RSA密钥对以及*ClientID*
2. *Client*将*ClientPrivate*安全地保存在本地，将*ClientPublic*与*ClientID*和本机IP地址打包并使用*Base64*编码
3. *Client*将编码后的数据使用预置的*ServerPublic*加密并发送至*Server*
4. *Server*使用*ServerPrivate*解密数据
5. *Server*将*ClientID*与IP地址和*ClientPublic*临时保存对应关系
6. 握手成功

#### 传输

1. *Client*随机生成一个*DataKey*，使用*DataKey*加密明文数据
2. *Client*使用*ServerPublic*加密*DataKey*
3. *Client*将加密的*DataKey*和加密的密文以及*ClientID*发送至*Server*
4. *Server*验证*ClientID*，并使用*ServerPrivate*解密*DataKey*
5. *Server*验证*DataKey*中包含的IP地址是否与缓存中*ClientID*对应的IP地址相同，若不匹配则返回错误信息
6. 若IP地址匹配则更新*ClientID*的对应关系数据的超时时间
7. 传输成功

#### 挥手

1. *Client*向*Server*发送*ClientID*
2. *Server*收到*ClientID*后检验是否存在该*ClientID*，若不存在则返回错误信息
3. 若存在该*ClientID*，则删除缓存内的对应关系
4. *Client*删除本地存储的所有临时数据
5. 挥手成功

---

<center><small>Written by @masteryyh</small></center>

<center><small>2020.11</small></center>