# LDAP 镜像

> 镜像地址：https://github.com/osixia/docker-openldap/blob/master/example/docker-compose.yml

查看 admin:  
```
docker exec openldap ldapsearch -x -H ldap://localhost -b dc=example,dc=org -D "cn=admin,dc=example,dc=org" -w admin
```

登录：

```
DN: cn=admin,dc=example,dc=org
密码： admin
```