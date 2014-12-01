��װ���̱Ƚ��鷳�������������[�ٷ��ĵ� Installing CoreOS to Disk](https://coreos.com/docs/running-coreos/bare-metal/installing-to-disk/) ������iso����������ֻ�ܵõ�һ����ȫ�޷���¼��ϵͳ��ÿ������֮�󶼻��ʼ������ΪISO����������RAM�ϡ���Ҫ���䰲װ��Ӳ���С������Ҵ��������virtualbox������ϰ�װcoreos��

����1 �� �����ǽ����
       ��װ������Ҫ����coreos�������ڰٶ������ṩ�˰�װ��������Ҫ�������ļ��� [http://pan.baidu.com/s/1sjNSJFJ](http://pan.baidu.com/s/1sjNSJFJ)���������Ҫ���ľ����������ĸ��ļ���Ȼ�����һ������Լ򵥵�wget����λ�á����磬�Ҿ�����virtualbox���棬������һ��ubuntu����������Ϊ���ţ�����ipΪ192.168.1.130��Ȼ��װһ��apache2,���ҽ������/var/www/html���棬
```
root@ubuntu:/var/www/html# ls 
444.5.0 coreos-install coreos_production_image.bin.bz2 id_rsa.pub static.network 
cloud-config.yaml coreos-install2 coreos_production_image.bin.bz2.sig index.html
```
, Ȼ�������������Ļ����ϣ�ִ�� `wget http://192.168.1.130/cloud-config.yaml`�� �������û�����⣬��ô����һ��������ˡ�

����2 :  ����cloud-config.yaml
    ��װ����������Ҫ�ľ����޸�cloud-config.yaml�����ڲ���һ�Ѿ�Ϊ����ṩ��һ������ģ�壬�����Ҫ�޸ĵ���`ssh-authorized-keys`��Ӧ��ֵ����������¼coreos�������ssh�Ĺ�Կ������ͨ�����²���򵥵����ɣ�
```
root@ubuntu:~# ssh-keygen
enter
enter
...
```
�м���Ҫѡ��Ĳ��裬ȫ��Ĭ�ϻس���ɡ�Ȼ���ڵ�ǰ�û���~/.ssh/����������`id_rsa id_rsa.pub`2���ļ�����id_rsa.pub����ļ��ܴ�����`ssh-authorized-keys`��Ӧ��ֵ�ϡ�
�����Ҫ��֤ `static.network` ��Ӧ��name��network��cloud-config.yaml��һ�¡������ҵ����ã�
```
#cloud-config

hostname: coreos1

coreos:
  etcd:
    addr: $private_ipv4:4001
    peer-addr: $private_ipv4:7001
  units:
    - name: etcd.service
      command: start
    - name: fleet.service
      command: start
    - name: static.network
      content: |
        [Match]
        Name=enp0s8

        [Network]
        Address=192.168.1.120/24
        Gateway=192.168.1.254
        DNS=8.8.8.8
        DNS=8.8.4.4
users: 
    - name: core 
        ssh-authorized-keys: 
    - ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDrBCWmYcPhOtsTYtgM7pF4cv5apCP/LHW7ZMZUZO5AZHaDG61fXGmgFc5Sy8t9yRV40/QLGE1BcwRiGVxx1ChPRzFB9/qSzxyzfErt0WGys44ly/d1KvKFNEhZif0hMtKcfGwntI8pILeaRX8pDK6Vct2u3oabPgvFZJZUUCcjv4Sf8ROjV9E8BVjtQNv7iNwgsDEP+Sgdhq/bsR+Nhcp6VX49rbT 
S+jEuAu+6EwvfU0ICv6S0txZn3x1W9b4XG9YfusRXNocNmtPjFsOCpL2hFwJk8mhorQFBLymOttzNcsW6WxuPyLScAbbrQBmgf/ej8GWw61fwKWba77acBNFt core@localho 
st 

    - groups: 
        - sudo 
        - docker
```

����3 �� ��װ
��virtualbox�½�һ������������硷���ӷ�ʽ �� �Ž�������Ȼ��洢���洢����Controller�� IDE�������Ƭ��ѡ����̡�ѡ����ղ����ص�coreos_production_iso_image.iso�� ���������
�����ղ���ӵ����������һ��core�˺����ڵ��û���������������ֱ���л���root
```
sudo -i 
``` 
1�� wget http://192.168.1.130/cloud-config.yaml,  ��Ϊ�Ѿ���ǰ�����ú��ˣ� �����ʲô������Ҫ�޸�(��Ⱥ�����£���Ҫ�޸�Ϊ��ͬ���������ã��޸�ip����ͬ)
2,  wget http://192.168.1.130/static.network,  ����
```
mv static.network  /etc/systemd/network/
systemctl restart systemd-networkd 
``` 
3,   wget http://192.168.1.130/coreos-install ,�޸�����207�е�BASE_URLΪ��Apache2���ڵĻ���ip�����ַ�ʽҲ������ĺô���  ��Ҳ����ֱ���޸�coreos�Դ���coreos-install��BASE_URL��
```
cp /usr/bin/coreos-install coreos-install 
chmod +x coreos-install
vim �޸�BASE_URL ���򵥵㣬û��sed�ˣ�
```  

4�� ��װ�� 
```
./coreos-install -d /dev/sda -C stable -c ./cloud-config.yaml
```
��������⣬������ʾ �ɹ���װ��Ӣ�İ���������

5�� ��¼
��װ���֮�� ��Ҫ��֤���Ƿ���ȷ��װ�������ϡ� ����ֹͣcoreos�������Ȼ��� ���á��洢��controll IDC �� ɾ��ǰ�沽��ѡ���iso�����ʱ�������ֱ�Ӵ�Ӳ�������ˡ�
Ȼ�������������
����֮�����û�����⣬�ͻ���ʾ��
```
...
coreos1 login:
this is coreos1
```
Ȼ���л�������������ssh core@192.168.1.120
```
core@ubuntu:~$ ssh core@192.168.1.120 
Last login: Mon Dec 1 00:37:45 2014 from 192.168.1.130 
CoreOS (stable) 
core@coreos1 ~ $
```
��װ�ɹ�

����4 �� �ٴ��޸�cloud-config.yaml

�����Ե�ǰcoreos�����ò����⣬��ô����Լ����޸�������ȻҲ��������ѡ��ֱ�Ӵ��°�װ��
1�� ֹͣcoreos������coreos iso��Ƭ(����3)��������������
2�� �޸�
```
[core@localhost ~]$ sudo -i 
[root@localhost~]# mount -o subvol=root /dev/sda9 /mnt 
[root@localhost ~]# vim /mnt/var/lib/coreos-install/user_data
```
3, ֹͣ��������ε����̣��������ܿ�����������Ƿ���Ч��

��һ�½ڽ�����coreos��Ⱥ��װ��

