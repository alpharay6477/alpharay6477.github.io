---
title: 部署 Slurm DataBase
summary: Rocky Linux 在部署 Slurm 集群软件后启用 Slurm DataBase 对作业进行记录。
tags:
  - Linux
date: 2025-03-16T21:03:22Z
categories:
  - Work
draft: false
isCJKLanguage: true
showAuthor: true
authors:
  - 葛世杰
---

## 一、 配置 MySQL 数据库

1. 安装 MySQL 数据库

```sh
root# dnf install mysql-server-8.0.36-1.el9_3.x86_64

root# systemctl start mysqld
root# systemctl enable mysqld

root# mysql_secure_installation
```

2. 设置 MySQL 数据库

```sh
root# mysql -u root -p
> CREATE DATABASE slurm_acct_db;
> CREATE USER 'slurm'@'localhost' IDENTIFIED BY 'your_password';
> GRANT ALL PRIVILEGES ON slurm_acct_db.* TO 'slurm'@'localhost';
> FLUSH PRIVILEGES;
> EXIT;
```

3. CREATE USER 'slurm'@'localhost' IDENTIFIED BY 'mypassword';

```sh
vim /etc/my.cnf.d/mysql-server.cnf

innodb_buffer_pool_size = 300G
innodb_lock_wait_timeout = 900

systemctl restart mysqld
```

## 二、 配置 Slurm DBD

```sh
dnf install slurm-slurmdbd-22.05.9-1.el9.x86_64

vim /etc/slurm/slurmdbd.conf

AuthType=auth/munge
DbdAddr=localhost
DbdHost=localhost                        # your_hostname
StorageType=accounting_storage/mysql
StorageHost=localhost
StoragePort=3306
StoragePass=p*******5                    # your_password
StorageUser=slurm
StorageLoc=slurm_acct_db
```

## 三、配置 SlurmCTL

配置 SlurmCTL 控制器以使用 slurm DBD，编辑 Slurm 的主配置文件 `slurm.conf`​，通常位于 `/etc/slurm/`​ 或 `/etc/slurm-llnl/`​：

```bash
sudo vim /etc/slurm/slurm.conf
```

在文件中添加以下行：

```ini
AccountingStorageEnforce=1
AccountingStorageHost=localhost
# AccountingStoragePass=                            # 使用 slurmdbd 无这一项
AccountingStoragePort=6819                            # slurmdbd 的端口
AccountingStorageType=accounting_storage/slurmdbd
# AccountingStorageUser=slurm
AccountingStoreFlags=job_account
```

确保 `Slurmctld`​ 和 `slurmdbd`​ 的配置文件中使用的端口和套接字路径一致。

```sh
scp /etc/slurm/slurm.conf root@node11:/etc/slurm/slurm.conf
scp /etc/slurm/slurm.conf root@node12:/etc/slurm/slurm.conf

systemctl restart slurmd.service
systemctl restart slurmdbd.service
systemctl restart slurmctld.service
```

## 四、初始化 Slurm 数据库

```bash
sacctmgr -i add cluster lab-server           # your_cluster_name
```

将 `your_cluster_name`​ 替换为你的集群名称，重启 Slurm 服务。

重启 Slurm 控制器和节点服务以应用配置更改：

```bash
sudo systemctl restart slurmd
sudo systemctl restart slurmctld
```

验证配置：使用 `sacct`​ 或 `sacctmgr`​ 命令验证数据库是否正常工作：

```bash
sacct 
```

或者列出集群信息：

```bash
sacctmgr show clusters
```

如果一切正常，你应该能够看到 Slurm 作业的会计信息存储在 MySQL 数据库中。

Slurm 添加 Slurm DBD 后出现无法提交作业错误

```sh
g16s *gjf
dos2unix: 正在转换文件 QABN_tdvert.gjf 为Unix格式...
sbatch: error: Batch job submission failed: Invalid account or account/partition combination specified

(base) [jzq@node01 tdvert]$ systemctl status slurmctld.service
● slurmctld.service - Slurm controller daemon
     Loaded: loaded (/usr/lib/systemd/system/slurmctld.service; enabled; preset: disabled)
     Active: active (running) since Wed 2025-02-05 22:54:20 CST; 9min ago
   Main PID: 3637988 (slurmctld)
      Tasks: 17
     Memory: 4.9M
        CPU: 173ms
     CGroup: /system.slice/slurmctld.service
             ├─3637988 /usr/sbin/slurmctld -D -s
             └─3637989 "slurmctld: slurmscriptd"

2月 05 22:58:38 node01 slurmctld[3637988]: slurmctld: _slurm_rpc_submit_batch_job: Invalid account or account/partition combination specified
2月 05 22:58:38 node01 slurmctld[3637988]: slurmctld: error: User 1000 not found
```

解决方法：

```sh
[root@node01 ~]# sacctmgr show account
   Account                Descr                  Org
---------- -------------------- --------------------
      root default root account                 root

[root@node01 ~]# sacctmgr add user name=jzq account=root
 Adding User(s)
  jzq
 Associations =
  U = jzq       A = root       C = lab-server
 Non Default Settings
Would you like to commit changes? (You have 30 seconds to decide)
(N/y): y

[root@node01 ~]# sacctmgr show user
      User   Def Acct     Admin
---------- ---------- ---------
       jzq       root      None
       ljd       root      None
      root       root Administ+
       wjr       root      None
```

## 附：对 Slurm DataBase 的补充

#### 1. 记录已经完成的任务的路径（不使用 Slurm DataBase）

由于我比较需要记录完成作业的时间，名称和 路径，但是 `sacct` 命令不支持记录 **路径**，于是另辟蹊径，在 sbatch 脚本入手：

```bash
#!/bin/bash
#SBATCH -J jobname
#SBATCH -N 1
#SBATCH -n 1
......
if ! grep -q "$(date '+%Y-%m-%d')" ~/.end_jobs; then
    echo "-------------------------------------" >> ~/.end_jobs
    echo "$(date '+%Y-%m-%d')" >> ~/.end_jobs
    echo " TIME        NAME  CPUS WORK_DIR" >> ~/.end_jobs
fi
printf "%-6s %10s %5s %-25s\n" "$(date "+%H:%M")" "jobname" "32" "/path/lc-blyp/cal" >> ~/.end_jobs
```

在 .sbatch 脚本末尾加入如上代码，让后就可以在 用户目录下的 .end_jobs 文件中记录 `TIME NAME CPUS WORK_DIR` 信息，每次登录可以 tail 一下查看信息。

修改生成 .sbatch 脚本的提交程序，只要在 `sbatch $sbatch` 前面加上如下代码，即可实现。

```bash
#!/bin/bash
.....
cat <<MYEND >> $sbatch    # 写入 sbatch 脚本
#!/bin/bash
#SBATCH -J jobname
#SBATCH -N 1
#SBATCH -n 1
............
if ! grep -q "\$(date '+%Y-%m-%d')" ~/.end_jobs; then
echo "-------------------------------------" >> ~/.end_jobs
echo "\$(date '+%Y-%m-%d')" >> ~/.end_jobs
echo " TIME        NAME  CPUS WORK_DIR" >> ~/.end_jobs
fi
printf "%-6s %10s %5s %-25s\n" "\$(date "+%H:%M")" "$JOB_NAME" "$XNPROC" "$workdir" >> ~/.end_jobs
MYEND 
sbatch $sbatch        # 提交脚本计算
```

#### 2. slurm.conf 关于 Account （Slurm DBD）参数解释

1. ​**​`AccountingStorageEnforce`​**​
   
   * **用途**: 控制会计存储的强制性。
   
   * **值**:
     
     * ​`0`​: 不强制要求作业会计信息。
     * ​`1`​: 强制要求作业会计信息。
     * ​`2`​: 强制要求作业会计信息，并且会计信息不完整时拒绝作业。
   
   * **建议**: 如果需要严格的会计，可以设置为 `1`​ 。

2. ​**​`AccountingStorageHost`​**​
   
   * **用途**: 指定会计存储后端的主机名或 IP 地址。
   * **值**: 根据你的后端存储（如 MySQL、PostgreSQL、SlurmDBD）设置相应的主机名或 IP。
   * **建议**: 如果你使用的是 MySQL，可以设置为 `localhost`​ 或你的 MySQL 服务器地址。

3. ​**​`AccountingStoragePass`​**​
   
   * **用途**: 指定访问会计存储后端的密码（如果是 Slurm DBD 则不设置）。
   * **值**: 根据你的后端存储设置相应的密码。
   * **建议**: 设置一个强密码，并确保它符合后端存储的要求。

4. ​**​`AccountingStoragePort`​**​
   
   * **用途**: 指定会计存储后端的端口号 （Slurm DBD 6819 `ss -tlnp | grep 6819`​）。
   * **值**: 根据你的后端存储设置相应的端口号（例如 MySQL 默认是 `3306`​，PostgreSQL 默认是 `5432`​）。
   * **建议**: 如果使用默认端口，可以保留为空或不设置。

5. ​**​`AccountingStorageType`​**​
   
   * **用途**: 指定会计存储的类型。
   
   * **值**:
     
     * ​`accounting_storage/none`​: 禁用会计存储。
     * ​`accounting_storage/mysql`​: 使用 MySQL 作为会计存储。
     * ​`accounting_storage/slurmdbd`​: 使用 SlurmDBD 作为会计存储。
   
   * **建议**: 如果需要会计功能，建议设置为 `accounting_storage/mysql`​ 或更高级的 `accounting_storage/slurmdbd`​。

6. ​**​`AccountingStorageUser`​**​
   
   * **用途**: 指定访问会计存储后端的用户名（如果是 Slurm DBD 则不设置）。
   * **值**: 根据你的后端存储设置相应的用户名。
   * **建议**: 设置一个专门用于会计的用户名，并确保它具有足够的权限。

7. ​**​`AccountingStoreFlags`​**​
   
   * **用途**: 指定会计存储的标记，控制哪些信息会被记录。
   
   * **值**: 可以是以下一个或多个（用逗号分隔）：
     
     * ​`job_comment`​: 存储作业的注释信息。
     * ​`end`​: 存储作业结束信息。
     * ​`durations`​: 存储作业的持续时间。
     * ​`job_env`​: 存储作业的环境变量。
     * ​`associative`​: 存储关联信息。
     * ​`format_std`​: 存储标准格式。
     * ​`job_account`​: 存储完整的会计信息。
   
   * **建议**: 根据你的需求选择需要的标记。例如，`job_account`​ 会存储完整的会计信息。

‍
