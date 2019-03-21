```
WARNING 2017-10-12 08:43:12,140 NetUtil.py:116 - Server at https://master1.hadoop.com:8440 is not reachable, sleeping for 10 seconds...
INFO 2017-10-12 08:43:22,141 NetUtil.py:62 - Connecting to https://master1.hadoop.com:8440/ca
ERROR 2017-10-12 08:43:22,151 NetUtil.py:88 - [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed (_ssl.c:579)
ERROR 2017-10-12 08:43:22,151 NetUtil.py:89 - SSLError: Failed to connect. Please check openssl library versions.
Refer to: https://bugzilla.redhat.com/show_bug.cgi?id=1022468 for more details.
```

Ambari-server Ambari-agent 间的通信默认走 HTTPS 协议, `/etc/python/cert-verification.cfg' 文件中设置 HTTPS 为默认值, 在当前环境下因为某些未知原因默认开启, 需要修改为 `disable` 来禁用。

```cfg
# Possible values are:
# 'enable' to ensure HTTPS certificate verification is enabled by default
# 'disable' to ensure HTTPS certificate verification is disabled by default
# 'platform_default' to delegate the decision to the redistributor providing this particular Python version

# For more info refer to https://www.python.org/dev/peps/pep-0493/
[https]
verify=disable # 原来为 platform_default
```
- In Addition

如果该方式不生效, 查看 Ambari Server 配置文件 `/etc/ambari-server/conf/ambari.properties` 中的 `security.server.disabled.ciphers` 项, 可能引起该问题的原因是 Java 与 Python https 加密问题引起, 该配置内容应如下:

```
ecurity.server.disabled.ciphers=TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384|TLS_RSA_WITH_AES_256_CBC_SHA256|TLS_ECDH_ECDSA_WITH_AES_256_CBC_SHA384|TLS_ECDH_RSA_WITH_AES_256_CBC_SHA384|TLS_DHE_RSA_WITH_AES_256_CBC_SHA256|TLS_DHE_DSS_WITH_AES_256_CBC_SHA256|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA|TLS_RSA_WITH_AES_256_CBC_SHA|TLS_ECDH_ECDSA_WITH_AES_256_CBC_SHA|TLS_ECDH_RSA_WITH_AES_256_CBC_SHA|TLS_DHE_RSA_WITH_AES_256_CBC_SHA|TLS_DHE_DSS_WITH_AES_256_CBC_SHA|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256|TLS_RSA_WITH_AES_128_CBC_SHA256|TLS_ECDH_ECDSA_WITH_AES_128_CBC_SHA256|TLS_ECDH_RSA_WITH_AES_128_CBC_SHA256|TLS_DHE_RSA_WITH_AES_128_CBC_SHA256|TLS_DHE_DSS_WITH_AES_128_CBC_SHA256|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA|TLS_RSA_WITH_AES_128_CBC_SHA|TLS_ECDH_ECDSA_WITH_AES_128_CBC_SHA|TLS_ECDH_RSA_WITH_AES_128_CBC_SHA|TLS_DHE_RSA_WITH_AES_128_CBC_SHA|TLS_DHE_DSS_WITH_AES_128_CBC_SHA|TLS_ECDHE_ECDSA_WITH_3DES_EDE_CBC_SHA|TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA|TLS_ECDH_ECDSA_WITH_3DES_EDE_CBC_SHA|TLS_ECDH_RSA_WITH_3DES_EDE_CBC_SHA|SSL_DHE_RSA_WITH_3DES_EDE_CBC_SHA|SSL_DHE_DSS_WITH_3DES_EDE_CBC_SHA|TLS_EMPTY_RENEGOTIATION_INFO_SCSV|TLS_DH_anon_WITH_AES_256_CBC_SHA256|TLS_ECDH_anon_WITH_AES_256_CBC_SHA|TLS_DH_anon_WITH_AES_256_CBC_SHA|TLS_DH_anon_WITH_AES_128_CBC_SHA256|TLS_ECDH_anon_WITH_AES_128_CBC_SHA|TLS_DH_anon_WITH_AES_128_CBC_SHA|TLS_ECDH_anon_WITH_3DES_EDE_CBC_SHA|SSL_DH_anon_WITH_3DES_EDE_CBC_SHA|SSL_RSA_WITH_DES_CBC_SHA|SSL_DHE_RSA_WITH_DES_CBC_SHA|SSL_DHE_DSS_WITH_DES_CBC_SHA|SSL_DH_anon_WITH_DES_CBC_SHA|SSL_RSA_EXPORT_WITH_DES40_CBC_SHA|SSL_DHE_RSA_EXPORT_WITH_DES40_CBC_SHA|SSL_DHE_DSS_EXPORT_WITH_DES40_CBC_SHA|SSL_DH_anon_EXPORT_WITH_DES40_CBC_SHA|TLS_RSA_WITH_NULL_SHA256|TLS_ECDHE_ECDSA_WITH_NULL_SHA|TLS_ECDHE_RSA_WITH_NULL_SHA|SSL_RSA_WITH_NULL_SHA|TLS_ECDH_ECDSA_WITH_NULL_SHA|TLS_ECDH_RSA_WITH_NULL_SHA|TLS_ECDH_anon_WITH_NULL_SHA|SSL_RSA_WITH_NULL_MD5|TLS_KRB5_WITH_3DES_EDE_CBC_SHA|TLS_KRB5_WITH_3DES_EDE_CBC_MD5|TLS_KRB5_WITH_DES_CBC_SHA|TLS_KRB5_WITH_DES_CBC_MD5|TLS_KRB5_EXPORT_WITH_DES_CBC_40_SHA|TLS_KRB5_EXPORT_WITH_DES_CBC_40_MD5
```

关于该问题, 可以参考: https://community.hortonworks.com/questions/228118/when-the-ambari-agent-restarts-it-loses-its-heartb.html