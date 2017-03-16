# SMARTVPN #
## ���� ##
SmartVPNΪ������Ϸ��ά�Ŷӷ�����һ��������ά��Ա�����Զ�����װOPENVPN����Ľű�����Ҫ������ҵʹ��OpenVPN����������

##���� ##

 - ���ذ�װVPN Server
 - Զ�̰�װVPN Client

## ������� ##

    lzo-2.03           ��װ:/usr/local           ����:null
    openssl-1.0.2k     ��װ:/usr/local/openssl   ����:null
    openvpn-2.1_rc22   ��װ:/usr/local/openvpn   ����:/etc/openvpn  ��־:/var/log/openvpn-server/client.log
    smartvpn-x.x.x     ��װ:/usr/local/smartvpn  ����:null
    
## �������� ##

**openvpn�������**

    /etc/init.d/openvpn start/stop

**smartvpn��װ��SERVER��װ��CLIENT��װ��**

    /usr/local/smartvpn/smartvpn #����ʽ�ű�

## �������� ##

    linux expect tools    #��Ҫ���ڽ���ʽ������Զ���������֤�飬Զ��SSH��װCLIENT��ʹ�ã�
    
## �Ѳ��Ա��뻷�� ##

    Red Hat Enterprise Linux Server release 6.4 (Santiago)
    
## �ֶ���װ ##
�������������Ҫ�ֶ���װ���밴�����¹淶���а�װ

��װ�����ݣ�

    smartvpn_x.x.x
    �� smartvpn.sh                    #�Զ�����װ�ű�
    ��
    ����smartvpn-package               #�����
           init.d.openvpn            #init.d��openvpn�����ļ�
           lzo-2.03.tar.gz           #lzoԴ���
           openssl-1.0.2k.tar.gz     #opensslԴ���
           openvpn-2.1_rc22.tar.gz   #openvpnԴ���
           openvpn-config.tar.gz     #openvpn�����ļ�
           
�ֶ���װʱ���Զ�����װ�ű����Զ��߼�

### VPN SERVER ###

**��ʼ��װ**      

��������(smartvpn_x.x.x)��/usr/local/smartvpn , �������еĲ�������/usr/local/smartvpn�²���

```shell
mkdir /usr/local/smartvpn
cp * /usr/local/smartvpn
cd /usr/local/smartvpn
```

��װ lzo-2.03.tar.gz
```shell
cd smartvpn-package
tar -zxvf lzo-2.03.tar.gz
cd lzo-2.03
./configure -prefix=/usr/local && make && make install
cd ..
rm -rf lzo-2.03.tar.gz
cd ..
```

2����װ openssl-1.0.2k
```shell
cd smartvpn-package
tar -zxvf openssl-1.0.2k.tar.gz
cd openssl-1.0.2k
./config -prefix=/usr/local/openssl && make && make install
cd ..
rm -rf openssl-1.0.2k
cd ..
```

3����װ openvpn-2.1_rc22.tar.gz
```shell
cd smartvpn-package
tar -zxvf openvpn-2.1_rc22.tar.gz
cd openvpn-2.1_rc22
./configure -prefix=/usr/local/openvpn && make && make install
cd ..
rm -rf openvpn-2.1_rc22
cd ..
```

4������ת��
```shell
sed -i '/net.ipv4.ip_forward/ s/\(.*= \).*/\11/' /etc/sysctl.conf
sysctl -p
```

5���½����������ļ�
```shell
mkdir -r /etc/openvpn
cd smartvpn-package
tar -zxvf openvpn-config.tar.gz
cp -R ./openvpn-config /etc/openvpn
rm -rf openvpn-config
cd ..
cp -r ./smartvpn-package/init.d.openvpn /etc/init.d/openvpn
sed -i "s/client/server/g" /etc/init.d/openvpn
chmod +x /etc/init.d/openvpn
```

6�����������ļ�

ע���滻����shell�еı���
 - $SERVERIP Ϊ�����IP
 - $SERVERIPAREA ΪOPENVPNͨѶ���Σ���ʽ��172.31.0.0 255.255.255.0��
 - $PORT OPENVPN�˿ںţ���ʽ��10050��

```shell
cd /etc/openvpn/easy-rsa/
source ./vars
./clean-all
./build-ca #ȫ���س�
./build-key-server server #����������y��ȫ���س�
./build-dh
cp ./keys/server.* /etc/openvpn/keys
cp ./keys/*.pem /etc/openvpn/keys
cp ./keys/ca* /etc/openvpn/keys
sed -i "s/#LOCALIP#/$SERVERIP/g" /etc/openvpn/server.conf
sed -i "s/#PORT#/$PORT/g" /etc/openvpn/server.conf
sed -i "s/#SERVERIPAREA#/$SERVERIPAREA/g" /etc/openvpn/server.conf
sed -i "s/#SERVERIP#/$SERVERIP/g" /etc/openvpn/client-conf/client.conf
sed -i "s/#PORT#/$PORT/g" /etc/openvpn/client-conf/client.conf
```

7������OPENVPN
```shell
/etc/init.d/openvpn start
```

**�����ļ�**

    /etc/openvpn
    ������ ccd                CCD�ļ���
    ��   ������ CLIENT_110     CLIENT_110�ļ������ҽ�������CLIENTʱ���ڣ�
    ������ client-conf        CLIENT����ļ�
    ��   ������ CLIENT_110     CLIENT_110�ļ��У����ҽ�������CLIENTʱ���ڣ�
    ��   ������ client.conf    CLIENTģ���ļ�
    ������ easy-rsa           rsa�ļ��У�Ĭ��Ϊ2.0
    ������ ipp.txt
    ������ keys               SERVER�˵�KEY�ļ�
    ��   ������ 01.pem
    ��   ������ ca.crt
    ��   ������ ca.key
    ��   ������ dh1024.pem
    ��   ������ server.crt
    ��   ������ server.csr
    ��   ������ server.key
    ������ server.conf        SERVER�����ļ�


**����CLIENT�����ļ�**

ע�⣺CLIENT�����ļ�������SERVER���������ɣ�CLIENT_$CLIENTIDΪ�Զ���

```shell
cd /etc/openvpn/easy-rsa/
source ./vars
./build-key CLIENT_$CLIENTID  #����������y��ȫ���س�
mkdir -p /etc/openvpn/client-conf/CLIENT_$CLIENTID/keys
cp ./keys/CLIENT_$CLIENTID* /etc/openvpn/client-conf/CLIENT_$CLIENTID/keys
cp ./keys/ca.crt /etc/openvpn/client-conf/CLIENT_$CLIENTID/keys
cp /etc/openvpn/client-conf/client.conf /etc/openvpn/client-conf/CLIENT_$CLIENTID/
mv /etc/openvpn/client-conf/CLIENT_$CLIENTID/keys/CLIENT_$CLIENTID.crt /etc/openvpn/client-conf/CLIENT_$CLIENTID/keys/client.crt
mv /etc/openvpn/client-conf/CLIENT_$CLIENTID/keys/CLIENT_$CLIENTID.csr /etc/openvpn/client-conf/CLIENT_$CLIENTID/keys/client.csr
mv /etc/openvpn/client-conf/CLIENT_$CLIENTID/keys/CLIENT_$CLIENTID.key /etc/openvpn/client-conf/CLIENT_$CLIENTID/keys/client.key
```

���� /etc/openvpn/client_conf/CLIENT_$CLIENTID �¼�CLIENT�����ļ�


### VPN CLIENT ###

**��ʼ��װ**
��������(smartvpn_x.x.x)��/usr/local/smartvpn , �������еĲ�������/usr/local/smartvpn�²���

```shell
mkdir /usr/local/smartvpn
cp * /usr/local/smartvpn
cd /usr/local/smartvpn
```

��װ lzo-2.03.tar.gz
```shell
cd smartvpn-package
tar -zxvf lzo-2.03.tar.gz
cd lzo-2.03
./configure -prefix=/usr/local && make && make install
cd ..
rm -rf lzo-2.03.tar.gz
cd ..
```

2����װ openssl-1.0.2k
```shell
cd smartvpn-package
tar -zxvf openssl-1.0.2k.tar.gz
cd openssl-1.0.2k
./config -prefix=/usr/local/openssl && make && make install
cd ..
rm -rf openssl-1.0.2k
cd ..
```

3����װ openvpn-2.1_rc22.tar.gz
```shell
cd smartvpn-package
tar -zxvf openvpn-2.1_rc22.tar.gz
cd openvpn-2.1_rc22
./configure -prefix=/usr/local/openvpn && make && make install
cd ..
rm -rf openvpn-2.1_rc22
cd ..
```

4������ת��
```shell
sed -i '/net.ipv4.ip_forward/ s/\(.*= \).*/\11/' /etc/sysctl.conf
sysctl -p
```

5���½����������ļ�
```shell
mkdir -r /etc/openvpn

# ������������ɵ������ļ���/etc/openvpn/client_conf/CLIENT_$CLIENTID/*��

cd /usr/local/smartvpn
cp -r ./smartvpn-package/init.d.openvpn /etc/init.d/openvpn
chmod +x /etc/init.d/openvpn
```

6������OPENVPN
```shell
/etc/init.d/openvpn start
```