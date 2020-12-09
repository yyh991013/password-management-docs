## API接口文档

1. 登录 (第一步)

   - 路径: /user/login/phase-1

   - 方式: POST

   - 真实参数: *username*       用户名

   - 返回:         *uid*                  被用户的密码加密的UID

     ​                 *user_private*   被用户UID加密的用户私钥

     ​                 *access_token*  被用户公钥加密的会话token

   - 异常返回: *code*                错误代码

   ​                        *reason*             错误原因

2. 登录 (第二步)

   - 路径: /user/login/phase-2
   - 方式: POST
   - 真实参数: *access_token*   明文会话token
   - 返回:         *code*                 错误代码

   ​                         *reason*             错误原因

3. 注册 (第一步)

   - 路径: /user/register/phase-1

   - 方式: POST

   - 真实参数: *username*         新用户名

   - 返回:         *code*                  错误代码

     ​                  *reason*              错误原因

     ​                  *uid*                    新生成的UID

4. 注册 (第二步)

   - 路径: /user/register/phase-2

   - 方式: POST

   - 真实参数: *uid*                      使用用户密码加密的UID

     ​                 *username*           用户名

   - 返回:        无