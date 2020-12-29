## API接口文档

P.S: 每一个请求所带来的返回值内均有错误代码和错误信息，懒得写了所以没标出来，都放在密文里。

P.P.S: 把需要发iv和salt的地方改一下，这俩东西使用base64编码之后按照先iv后salt的顺序放在密文的后面，用小数点分隔。

1. 登录 (第一步)

   - 路径: /user/login/phase-1?username=${mail}

   - 方式: GET

   - 真实参数: *mail*       邮箱

   - 返回:         *uid*                  被用户的密码加密的UID (包括iv和salt)

     ​                 *user_private*   被用户UID加密的用户私钥 (包括iv和salt)

     ​                 *access_token*  被用户公钥加密的会话token
   
2. 登录 (第二步)

   - 路径: /user/login/phase-2?token=${access_token}
   - 方式: GET
   - 真实参数: *token*   明文会话token
   - 返回: 无

3. 注册 (第一步)

   - 路径: /user/register/phase-1?username=${mail}

   - 方式: GET

   - 真实参数: *mail*         新邮箱

   - 返回: 无

4. 注册 (第二步)

   - 路径: /user/register/phase-2

   - 方式: POST

   - 真实参数: *userid*                 使用用户密码加密的UID (包括iv和salt)

     ​                 *mail*                     用户名

     ​                 *recovery*              使用恢复密钥加密的UID (包括iv和salt)

     ​                 *private_key*          使用UID加密的私钥 (包括iv和salt)

     ​                 *public_key*            明文公钥

   - 返回:        无

5. 获取所有记录
   - 路径: /${mail}/all
   - 方式: GET
   - 头: *token*                              会话token
   - 返回: records                      所有记录
   
6. 更新记录

   - 路径: /${user-email}/update-${record-id}

   - 方式: POST

   - 头: *token*                               会话token

   - 真实参数: *title*                       标题 (密文，被*key*加密，包含iv和salt)

     ​                 *sort*                       分类 (密文，被*key*加密，包含iv和salt)

     ​                 *content*                 内容 (密文，被*key*加密，包含iv和salt)

     ​                 *key*                        加密数据的密钥 (密文，被*uid*加密，包含iv和salt)

   - 返回: 无

     

7. 增加记录

   - 路径: /${user-email}/add

   - 方式: POST

   - 头: *token*                                会话token

   - 真实参数: *title*                       标题 (密文，被*key*加密，包含iv和salt)

     ​                 *sort*                       分类 (密文，被*key*加密，包含iv和salt)

     ​                 *content*                 内容 (密文，被*key*加密，包含iv和salt)

     ​                 *key*                        密钥 (密文，被*uid*加密，包含iv和salt)

   - 返回: 无

8. 删除记录

   - 路径: /${user-email}/remove-${record-id}
   - 方式: GET
   - 头: *token*                                 会话token
   - 返回: 无



传输层数据包格式

- *data*               数据密文 (包含iv和salt)
- *key*                 密钥密文



