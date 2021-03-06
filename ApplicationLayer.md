## 应用层

---

### 代号

**客户端** - *Client*. 

**服务端** - *Server*. 

**用户唯一ID** - *UID*. 

**用户的RSA公钥** - *UserPublic*

**用户的RSA私钥** - *UserPrivate*

**用户会话ID** - *AccessToken*

**记录ID** - *RecordID*

---

### 设计思路

- “环环相扣”，所有的数据保密都靠用户的密码，只有用户妥善保管该系统的密码才能保证数据安全。密码加密*UID*，*UID*加密*UserPrivate*，*UserPrivate*加密*AccessToken*，而每个记录都被随机生成的*RecordKey*加密，而*RecordKey*又被*UID*加密。*Server*完全无法查看用户的任何数据，也不会直接保存用户密码，而通信过程又被传输层安全通道进行加密，只要用户不直接暴露自己的密码就能保证数据安全。
- 如果用户忘记了密码，则无法恢复加密的数据。
- 除去*AccessToken*的下发和解密过程使用RSA-OAEP 2048bit算法外，其他加密过程均使用AES-256-CBC算法。
- 使用AES算法的部分均会使用到"盐"和初始向量，这部分数据需要与密文一起发送，如登录的时候服务端返回一些被加密的数据，也会同时返回盐和初始向量部分的数据。在客户端向服务端提交数据时亦是如此，如果涉及到AES算法在提交密文的同时均需要同时提交盐和初始向量。具体情况以接口文档为准。

---

### 流程

#### 注册

1. *Client*向*Server*发送用户名  
2. *Server*检查是否已经存在该用户,若存在则直接返回错误信息
3. 若不存在该用户,*Client*生成一个*UID*和*RecoveryKey*,分别使用用户的密码和*RecoveryKey*加密*UID*,生成一RSA密钥对,使用明文*UID*加密*UserPrivate*
6. 将用户名、*RecoveryKey*、加密两个的*UID*、加密后的*UserPrivate*和明文*UserPublic*发送至*Server*
5. 发送一份包含RecverKey的邮件给用户并提醒其谨慎保管
6. 注册成功

#### 登录

1. *Client*向*Server*发送用户名
2. *Server*检查是否存在该用户,若不存在则返回错误信息
3. 若存在该用户,则向*Client*发送加密的*UID*、加密的*UserPrivate*和使用*UserPublic*加密的*AccessToken*
4. *Client*尝试使用用户输入的密码解密*UID*
5. 若能解密成功,则用户输入的密码正确,使用解密后的*UID*解密*UserPrivate*
6. 使用解密的*UserPrivate*解密*AccessToken*
7. *Client*保存明文*AccessToken*与明文*UID*
8. *Client*向*Server*发送明文*AccessToken*
9. *Server*保存明文*AccessToken*与用户名的临时对应关系
10. 登录成功

#### 找回账号(忘记密码)

1. *Server*向用户发送一封含有验证码的验证邮件
2. *Client*向*Server*发送验证码
3. *Server*检查验证码是否一致,若不一致返回错误信息
4. 若验证码一致,则向*Client*发送使用*RecoveryKey*加密的*UID*
5. 使用用户输入的*RecoveryKey*解密UID,若解密失败则提示错误
6. 若解密成功,则用用户设置的新密码加密UID,并生成新的*RecoveryKey*加密*UID*
7. 将两个*UID*和RecoveryKey发送至*Server*
8. *Server*发送包含新的RecoveryKey的邮件到用户邮箱
9. 找回账号

#### 增加记录

1. *Client*将用户输入的数据和标题、分类以及随机生成的*RecordID*使用*Base64*编码
2. *Client*随机生成一个密钥*RecordKey*,使用*RecordKey*加密用户数据
3. *Client*使用明文*UID*加密*RecordKey*
4. *Client*向*Server*发送加密的*RecordKey*和加密的用户数据以及编码后的标题和分类,以及*AccessToken*
5. *Server*验证*AccessToken*并取得用户名
6. *Server*将用户名、标题、分类和*RecordID*的对应关系永久保存至数据库
7. *Server*将加密的*RecordKey*以及加密的用户数据永久保存至数据库
8. 增加成功

#### 获取所有数据

1. *Client*将用户的邮箱与*AccessToken*一并发送给*Server*
2. *Server*验证*AccessToken*与邮箱后返回所有匹配的记录数据

#### 修改特定记录

1. *Client*将用户数据使用*Base64*编码
2. *Client*随机生成一个*RecordKey*
3. *Client*使用*RecordKey*加密用户数据
4. *Client*使用明文*UID*加密*RecordKey*
5. *Client*将加密后的*RecordKey*、*RecordID*、加密后的用户数据以及*AccessToken*发送给*Server*
6. *Server*检验*AccessToken*并取得用户名
7. *Server*检验是否存在指定的*RecordID*，若指定*RecordID*不存在则返回错误信息
8. 若指定*RecordID*存在，则修改数据库中指定用户名和*RecordID*对应的标题、分类信息
9. *Server*修改指定*RecordID*的*RecordKey*和加密后的用户数据
10. 修改完成

#### 删除指定数据

1. *Client*将*AccessToken*和*RecordID*发送至*Server*
2. *Server*检验*AccessToken*并取得用户名
3. *Server*检查是否存在对应的*RecordID*，若不存在则返回错误数据
4. 若存在对应数据，则删除对应的用户名、标题、分类、*RecordID*数据
5. *Server*删除对应的*RecordID*、*RecordKey*和加密后的数据
6. 删除成功

#### 修改密码

1. *Client*发送*AccessToken*至*Server*
2. *Server*验证*AccessToken*后发送加密后的*UID*至*Client*
3. *Client*先接收用户输入的密码，尝试解密*UID*
4. 若*UID*解密成功则使用新密码加密*UID*并和*AccessToken*一起发送至*Server*
5. *Server*验证*AccessToken*并取得用户名
6. *Server*修改对应*UID*数据
7. 修改成功

#### 退出系统（包括直接关闭浏览器）

1. *Client*发送*AccessToken*至*Server*
2. *Server*验证*AccessToken*后删除缓存中的对应关系
3. *Client*删除本地保存的临时数据
4. 退出成功

---

<center><small>Written by @masteryyh</small></center>

<center><small>2020.11</small></center>

