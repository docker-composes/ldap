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

搜索所有对象：
```
docker exec openldap ldapsearch -x -H ldap://localhost -b dc=example,dc=org -D "cn=admin,dc=example,dc=org" -w admin "objectClass=*"
```

Golang 搜索:
```
package ldap

import (
	"encoding/json"
	"fmt"
	"github.com/go-ldap/ldap/v3"
)

func Connect() {
	l, err := ldap.Dial("tcp", "127.0.0.1:389")
	if err != nil {
		fmt.Println("连接失败", err)
	}
	err = l.Bind("cn=admin,dc=example,dc=org", "admin")
	if err != nil {
		fmt.Println("管理员认证失败", err)
	}
	fmt.Println("连接成功")
	searchRequest := ldap.NewSearchRequest("dc=example,dc=org",
		ldap.ScopeWholeSubtree, ldap.NeverDerefAliases, 0, 0, false,
		"(&(objectClass=*))",
		[]string{"cn", "sn", "mail", "userPassword"}, nil)
	search, err := l.Search(searchRequest)
	if err != nil {
		fmt.Println("搜索错误", err)
		return
	}
	fmt.Println(search)
	if search != nil {
		for _, entry := range search.Entries {
			d, _ := json.Marshal(entry.Attributes)
			fmt.Println(string(d))
			entry.PrettyPrint(0)
			//fmt.Println(entry.DN,
			//	entry.GetAttributeValue("cn"), entry.GetAttributeValue("Password"), entry.GetAttributeValue("sn"))
		}
	}
	
}
```