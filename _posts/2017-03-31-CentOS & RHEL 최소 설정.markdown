---
layout: post
title:  "CentOS & RHEL �ּ� ����"
date:   2017-03-31 18:15:55 +0900
categories: jekyll update
---

- �۾� ����
0. ��Ƽ�� �и� �� �⺻ ���丮 ����(/engn001, /srcw001 �Ǵ� /srch001, /logs001, -DB-/data001)
1. �������� �� ������ ȯ�� Ȯ��
- �ѱ�Ȯ�� : locale (ko_KR.UTF-8)
- KST Ȯ�� (ls /usr/share/zoneinfo/Asia/Seoul --> ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime)
- root �� �ű԰��� �н����� ����
- ����� ��ġ�� httpd, mysql, svn ���� (yum remove)
- Package ��ġ
  yum install ntp gcc gc gcc-c++ make apr-util openssl openssl-devel zlib zlib-devel unzip perl
- sudo ������ �ʿ��� ���� ������ ���:  wheel �׷쿡 ���� �׷�(/etc/group)�� �߰� �� ������ ���� �Ǵ� ���� ���� �߰� -- ���߼����� �� ��
  $ visudo
  ## Allows people in group wheel to run all commands (�ּ�����)
  %wheel  ALL=(ALL)       ALL
- jdk ��ġ: /usr/local/java
  alternative ���� (Jenkins �������� ��⵿�� ���� �⺻ Java ���� ��ġ�� �������־�� �Ѵ�)
  $ alternatives --install /usr/bin/java java /usr/local/java/jdk1.7.0_67/bin/java 100
  $ alternatives --config java (����� Java�� 1�� ���� ���õǾ��־�� �Ѵ�)
- iptables disable : vhost web ������ ���� on

2. hostname �缳�� (hostname, /etc/hosts, /etc/sysconfig/network) : ���� ������ Rule�� ������ ��κ� 'ȸ�� + H/W/D + �ý��� + (RIC) + ����/� + �Ϸù�ȣ + V
- API Service: LGWAPISVCS01
- DB : LGDSVCS01

3. logroatated ���� (���û���)
   /etc/logrotate.conf
   # see "man logrotate" for details
   # rotate log files weekly
   daily
   # keep 14 days worth of backlogs
   rotate 14

3. NTP ���� (/etc/ntp.conf)
server 0.rhel.pool.ntp.org
server 1.rhel.pool.ntp.org
server 2.rhel.pool.ntp.org

���� Ȯ�Ή�����
$ ntpdate 0.rhel.pool.ntp.org
$ service ntpd start
$ chkconfig ntpd on
$ ntpq -p �� Ȯ��

4. FTP ����(no guest) �� ���ʿ� ���� ����
    <Redhat>
    # userdel adm
    # userdel lp
    # userdel ftp
    # userdel sync
    # userdel shutdown
    # userdel halt
    # userdel news
    # userdel uucp
    # userdel operator
    # userdel games
    # userdel gopher
    # userdel mysql <-- ���� !!!
    # userdel apache
    # userdel oprofile
    # userdel nfsnobody

    # groupdel adm
    # groupdel lp
    # groupdel news
    # groupdel uucp
    # groupdel games
    # groupdel dip
    # groupdel pppusers
    # groupdel slipusers

5. Limit 'su' command (/etc/pam.d/su) �ּ�����
#auth           required        pam_wheel.so use_uid

$ chmod 4750 /bin/su

(���û���) root ���� login ��������
/etc/ssh/sshd_config ������ 'PermitRootLogin no' ���� ��,
service sshd restart

6. ������ �⺻ ����
-   .bashrc
###########################################################
# Bash Setting
###########################################################
set -o vi
alias ll='ls -alF'  <-- ���� ������ �����ؼ� alias ll='ls -lF' �� ����
alias vi=vim
(root ���� ����)
DEFAULT="\[\033[m\]"
BG_BLUE="\[\033[44m\]"
BG_RED="\[\033[41m\]"
PS1=$BG_RED"[\u@\h \W]"$DEFAULT" \\$ "
export PS1

7. profile �⺻ ���� (/etc/profile)
###########################################################
# Profile Setting
###########################################################

# Add timestamp to .bash_history
export HISTTIMEFORMAT="%F %T "

# Add session timeout 1800
TMOUT=1800
HISTSIZE=10000

(JDK ��ġ ����)
## JDK
export JAVA_HOME=/usr/local/java/jdk1.7.0_80
export PATH=$PATH:$JAVA_HOME/bin
export CLASSPATH=$JAVA_HOME/jre/lib:$JAVA_HOME/lib/tools.jar

(MariaDB ��ġ ����)
## MariaDB
export MARIADB_HOME=/engn001/maria/mariadb-10.0.21
export PATH=$PATH:$MARIADB_HOME/bin

8. ulimit ȯ�� ���� (/etc/security/limits.conf)
�۾����� �Ʒ��� ���� ���翩�� Ȯ�� ��, ������ �߰�
/etc/pam.d/login
session required pam_limits.so

/etc/pam.d/sshd
session required pam_limits.so

/etc/security/limits.conf
*               soft    nofile          65536
*               hard    nofile          65536
*               soft    stack           20480
*               hard    stack           20480
--- ���⼭ �Ϲ�
*               soft    nproc           65536
*               hard    nproc           65536
--- Apache
# max user processes for apache (pending signals�� ���߸� ����)
*               soft    nproc           158757
*               hard    nproc           158757

9. System Ŀ�� ���� (/etc/sysctl.conf  --> sysctl -p)
--- �Ϲ� �̵���� ����
##### max file count #####
fs.file-max = 65535

--- DB����
##### max file count #####
fs.file-max = 6815744

### Disable IPv6 ###
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1

--- Front End �迭 ���� ����
### MAX Connection ###
net.core.somaxconn = 4096 ~ 65535

--- �Ϲ� �̵���� ����
### MAX Connection ###
net.core.somaxconn = 2048

--- DB����
### MARIA setting ### : ���ص� ��
net.core.rmem_default = 8388608
net.core.wmem_default = 8388608
net.core.rmem_max = 8388608
net.core.wmem_max = 8388608
net.ipv4.tcp_syn_retries = 2
net.ipv4.tcp_retries2 = 5
net.ipv4.tcp_rmem = 8388608 8388608 8388608
net.ipv4.tcp_wmem = 8388608 8388608 8388608
net.ipv4.tcp_rfc1337 = 1
net.ipv4.tcp_max_syn_backlog = 8192
net.ipv4.tcp_keepalive_time = 600
net.ipv4.tcp_fin_timeout = 30
net.ipv4.ip_local_port_range = 1024 65535
net.ipv4.tcp_max_tw_buckets = 2000000

### REDIS Support ###
vm.overcommit_memory = 2
vm.overcommit_ratio = 99
vm.swappiness = 0

10. ��Ÿ
/etc/rc.d
### for REDIS ###
echo never > /sys/kernel/mm/transparent_hugepage/enabled
