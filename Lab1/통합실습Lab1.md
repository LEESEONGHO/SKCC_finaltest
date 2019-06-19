## Local to Server 접속
```
ssh -i ./SKCC.pem  centos@15.164.124.231
ssh -i ./SKCC.pem  centos@15.164.152.205
ssh -i ./SKCC.pem  centos@15.164.68.198
ssh -i ./SKCC.pem  centos@15.164.7.245
ssh -i ./SKCC.pem  centos@15.164.86.252

이후 서버 5대 재발급 받아 진행함



```

## 각 서버별 centos 계정 패스워드 변경
```
sudo passwd centos
```

## 각 서버별 sshd_config 파일 수정
```
sudo vi /etc/ssh/sshd_config
>> PasswordAuthentication yes

sudo systemctl restart sshd.service
sudo systemctl status sshd.service
```

## 각 서버별 hosts 파일 수정
```
sudo vi /etc/hosts 

172.31.40.125   util01.cdhcluster.com   mn1
172.31.47.108   master01.cdhcluster.com util01
172.31.39.119   data01.cdhcluster.com   dn1
172.31.34.132   data02.cdhcluster.com   dn2
172.31.46.185   data03.cdhcluster.com   dn3


-- 신규 발급받은 IP
172.31.13.45    master01.cdhcluster.com mn1
172.31.12.220   data01.cdhcluster.com   dn1
172.31.15.46    data02.cdhcluster.com   dn2
172.31.1.226    data03.cdhcluster.com   dn3
172.31.12.50    util01.cdhcluster.com   util01

```

## key gen 설정 (util01)
```
-- keygen 설정
cd .ssh

ssh-keygen -t rsa

ssh-copy-id -i ~/.ssh/id_rsa.pub mn1
ssh-copy-id -i ~/.ssh/id_rsa.pub dn1
ssh-copy-id -i ~/.ssh/id_rsa.pub dn2
ssh-copy-id -i ~/.ssh/id_rsa.pub dn3

ssh mn1 명령실행후 로그인 없이 로그인 여부 확인 
```

## hostname 변경 (각 서버)
```
hostname -f
sudo hostnamectl set-hostname master01.cdhcluster.com
sudo hostnamectl set-hostname util01.cdhcluster.com  
sudo hostnamectl set-hostname data01.cdhcluster.com  
sudo hostnamectl set-hostname data02.cdhcluster.com  
sudo hostnamectl set-hostname data03.cdhcluster.com  
hostname -f

init 6
```

## install CM (util01)
```
util01 서버에 cm 설치 
[centos@util01 ~]$ hostname
util01.cdhcluster.com

A. Configure repository

# sudo wget https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/cloudera-manager.repo \
-P /etc/yum.repos.d/

# sudo vi /etc/yum.repos.d/cloudera-manager.repo
>> baseurl=https://archive.cloudera.com/cm5/redhat/6/x86_64/cm/5.15.2/

# sudo rpm --import \
https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/RPM-GPG-KEY-cloudera
```

## JDK 설치 (util01)
```
sudo yum install oracle-j2sdk1.7
```

## CM 서버 설치 (util01)
```
sudo yum install cloudera-manager-daemons  cloudera-manager-server
```

## maria DB 설치(util01)
```
# sudo yum install -y mariadb-server
# sudo systemctl enable mariadb
# sudo systemctl start mariadb
# sudo /usr/bin/mysql_secure_installation
>> root 비번 : toor 세팅
```

## JDBC connector 설치 (각 서버)
```
# sudo wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.47.tar.gz

# tar zxvf mysql-connector-java-5.1.47.tar.gz
# sudo mkdir -p /usr/share/java/
# cd mysql-connector-java-5.1.47
# sudo cp mysql-connector-java-5.1.47-bin.jar /usr/share/java/mysql-connector-java.jar

# cd /usr/share/java/
# sudo yum install mysql-connector-java
>> Y
```

## maria DB, USER 생성
```
# mysql -u root -p

CREATE DATABASE scm DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL ON scm.* TO 'scm-user'@'%' IDENTIFIED BY 'somepassword';

CREATE DATABASE amon DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL ON amon.* TO 'amon-user'@'%' IDENTIFIED BY 'somepassword';

CREATE DATABASE rmon DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL ON rmon.* TO 'rmon-user'@'%' IDENTIFIED BY 'somepassword';

CREATE DATABASE hue DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL ON hue.* TO 'hue-user'@'%' IDENTIFIED BY 'somepassword';

CREATE DATABASE metastore DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL ON metastore.* TO 'metastore-user'@'%' IDENTIFIED BY 'somepassword';

CREATE DATABASE sentry DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL ON sentry.* TO 'sentry-user'@'%' IDENTIFIED BY 'somepassword';

CREATE DATABASE oozie DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL ON oozie.* TO 'oozie-user'@'%' IDENTIFIED BY 'somepassword';

FLUSH PRIVILEGES;

SHOW DATABASES;
EXIT;

```

## CM Database setup
```
# sudo /usr/share/cmf/schema/scm_prepare_database.sh mysql scm scm-user somepassword
# sudo systemctl start cloudera-scm-server
```

