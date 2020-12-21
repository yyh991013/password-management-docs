## API接口文档

1. 登录 (第一步)

   - 路径: /user/login/phase-1

   - 方式: POST

   - 真实参数: *username*       用户名

   - 返回:         *uid*                  被用户的密码加密的UID

     ​                 *salt*                  用户密码的"盐"

     ​                 *iv*                     UID被加密时产生的初始向量
     
     ​                 *user_private*   被用户UID加密的用户私钥
     
     ​                 *private_salt*     UID的"盐"
     
     ​                 *private_iv*        私钥被加密时产生的初始向量
     
     ​                 *access_token*  被用户公钥加密的会话token

2. 登录 (第二步)

   - 路径: /user/login/phase-2
   - 方式: POST
   - 真实参数: *access_token*   明文会话token
   - 返回: 无

3. 注册 (第一步)

   - 路径: /user/register/phase-1

   - 方式: POST

   - 真实参数: *username*         新用户名

   - 返回: 无

4. 注册 (第二步)

   - 路径: /user/register/phase-2

   - 方式: POST

   - 真实参数: *uid*                      使用用户密码加密的UID

     ​                 *salt*                      用户密码的"盐"

     ​                 *iv*                         使用用户密码加密UID时生成的初始向量

     ​                 *username*           用户名

     ​                 *recovery*              使用恢复密钥加密的UID

     ​                 *rec_salt*                恢复密钥的"盐"

     ​                 *rec_iv*                   使用恢复密钥加密UID时生成的初始向量

     ​                 *private_key*          使用UID加密的私钥

     ​				 *uid_salt*                UID的"盐"

     ​                 *private_iv*             使用UID加密私钥时生成的初始向量

     ​                 *public_key*            明文公钥

   - 返回:        无

5. 获取所有记录
   - 路径: /${user-email}/all
   - 方式: GET
   - 头: *token*                              会话token
   - 返回: records                      所有记录
   
6. 更新记录

   - 路径: /${user-email}/update-${record-id}

   - 方式: POST

   - 头: *token*                               会话token

   - 真实参数: *title*                       标题 (密文，被*key*加密)

     ​                 *sort*                       分类 (密文，被*key*加密)

     ​                 *content*                 内容 (密文，被*key*加密)

     ​                 *key*                        加密数据的密钥 (密文，被*uid*加密)

     ​                 *salt*                        "盐" (密文，被*uid*加密)

     ​                 *iv*                           加密初始向量 (密文，被*uid*加密)

   - 返回: 无

     

7. 增加记录

   - 路径: /${user-email}/add

   - 方式: POST

   - 头: *token*                                会话token

   - 真实参数: *title*                       标题 (密文，被*key*加密)

     ​                 *sort*                       分类 (密文，被*key*加密)

     ​                 *content*                 内容 (密文，被*key*加密)

     ​                 *key*                        密钥 (密文，被*uid*加密)

     ​                 *salt*                        "盐" (密文，被*uid*加密)

     ​                 *iv*                           加密初始向量 (密文，被*uid*加密)

   - 返回: 无

8. 删除记录

   - 路径: /${user-email}/remove-${record-id}
   - 方式: GET
   - 头: *token*                                 会话token
   - 返回: 无