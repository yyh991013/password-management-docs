## 数据库结构设计

---

### 架构设计

- 数据表采用InnoDB引擎。
- 数据库实例在必要的情况下可以部署多个节点作数据库集群分担计算压力。

---

### 表设计

#### 用户表UserInfo

| 字段名称       | 字段类型     | 字段约束              | 注释            |
| :------------- | ------------ | --------------------- | --------------- |
| UserName       | VARCHAR(20)  | PRIMARY KEY, NOT NULL | 用户名          |
| UserID         | VARCHAR(128) | NOT NULL, UNIQUE      | 用户ID(UID)密文 |
| UserPublicKey  | TEXT         | NOT NULL, UNIQUE      | 用户RSA公钥     |
| UserPrivateKey | TEXT         | NOT NULL, UNIQUE      | 用户RSA私钥密文 |

#### 数据记录表DataRecord

| 字段名称       | 字段类型    | 字段约束                                | 注释                  |
| -------------- | ----------- | --------------------------------------- | --------------------- |
| RecordID       | VARCHAR(32) | NOT NULL, PRIMARY KEY                   | 数据ID                |
| UserName       | VARCHAR(20) | NOT NULL, REFERENCES UserInfo(UserName) | 外键，用户名          |
| RecordTitle    | VARCHAR(48) | NOT NULL                                | 数据记录标题          |
| RecordSort     | VARCHAR(32) | NOT NULL                                | 数据记录分类          |
| RecordKey      | VARCHAR(48) | NOT NULL, UNIQUE                        | 加密数据的AES密钥密文 |
| RecordFileName | VARCHAR(32) | NOT NULL, UNIQUE                        | 数据文件的文件名      |

---

<center><small>Written by @masteryyh</small></center>

<center><small>2020.11</small></center>