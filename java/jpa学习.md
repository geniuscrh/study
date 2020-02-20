# 配置

## build.gradle配置
``` groovy
dependencies {	
	compile('org.springframework.boot:spring-boot-starter-data-jpa')
	compile ("mysql:mysql-connector-java")
}
```
## application.properties配置
``` properties
#MySQL数据库连接
spring.datasource.url=jdbc:mysql://localhost/wangluo
spring.datasource.username = root
spring.datasource.password = ffffff
spring.datasource.driverClassName = com.mysql.jdbc.Driver
#配置自动建表：updata:没有表新建，有表更新操作,控制台显示建表语句
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true

```



# 命名规则

| 关键字            | 方法命名                       | sql where字句              |
| ----------------- | ------------------------------ | -------------------------- |
| And               | findByNameAndPwd               | where name= ? and pwd =?   |
| Or                | findByNameOrSex                | where name= ? or sex=?     |
| Is,Equals         | findById,findByIdEquals        | where id= ?                |
| Between           | findByIdBetween                | where id between ? and ?   |
| LessThan          | findByIdLessThan               | where id < ?               |
| LessThanEquals    | findByIdLessThanEquals         | where id <= ?              |
| GreaterThan       | findByIdGreaterThan            | where id > ?               |
| GreaterThanEquals | findByIdGreaterThanEquals      | where id > = ?             |
| After             | findByIdAfter                  | where id > ?               |
| Before            | findByIdBefore                 | where id < ?               |
| IsNull            | findByNameIsNull               | where name is null         |
| isNotNull,NotNull | findByNameNotNull              | where name is not null     |
| Like              | findByNameLike                 | where name like ?          |
| NotLike           | findByNameNotLike              | where name not like ?      |
| StartingWith      | findByNameStartingWith         | where name like '?%'       |
| EndingWith        | findByNameEndingWith           | where name like '%?'       |
| Containing        | findByNameContaining           | where name like '%?%'      |
| OrderBy           | findByIdOrderByXDesc           | where id=? order by x desc |
| Not               | findByNameNot                  | where name <> ?            |
| In                | findByIdIn(Collection<?> c)    | where id in (?)            |
| NotIn             | findByIdNotIn(Collection<?> c) | where id not  in (?)       |
| True              | findByAaaTue                   | where aaa = true           |
| False             | findByAaaFalse                 | where aaa = false          |
| IgnoreCase        | findByNameIgnoreCase           | where UPPER(name)=UPPER(?) |



# 注释

## Entity
``` java
@Entity
@Table(name="gis_association_talbe")

//主键
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)

//列名
@Column(name="house_id")

//不生成
@Transient

```
## Repository
``` java
extends   JpaRepository<Entity,Long>
```



# 基本操作

## 查询

### 自定义

```java
@Query("select o from RemoteGISEntity o where o.remoteGISId not in(select max(r.remoteGISId) as id from RemoteGISEntity r group by r.address)")
List<RemoteGISEntity> findRepeatRecord();

```

### 分页

```java
/*
*repository
*/
public Page<KeyEntity> findAll(Pageable pageable);

/*
*Controller
*/
//设置排序方式
Direction sortOrder=Sort.Direction.DESC;
//设置分页参数
Pageable pageable =PageRequest.of(cur_page,page_limit, sortOrder,"borrowTime");
//获取分页数据
List<Entity> list=page.getContent()
//获取所有的数据量
page.getTotalElements();

```



## 删除

``` java
@Transactional
public void deleteByNetworkType(String networkType);
```



## 增加

## 修改

```java
@Transactional
@Modifying
@Query("update RemoteGISEntity r set r.village=?1 where r.address like ?2")
int updateVillageByAddressLike(String village,String address);

```

## 过程

```java
@Entity
@NamedStoredProcedureQueries({
    @NamedStoredProcedureQuery(
      name = "analyzeBoxAssociation",//系统过程名
      procedureName = "analyze_box_association",//数据库过程名
      resultClasses = {
				AnalyzeBoxAssociationProcedure.class},
            parameters = {
                        @StoredProcedureParameter(
                                mode=ParameterMode.IN,
                                name="c_village_name",
                                type=String.class
                        )
             }//传入参数
     )
})

//新增一个主键
@Id
@GeneratedValue
private long id;

```



# 外部关联

## 学习网址

http://www.ityouknow.com/springboot/2017/06/26/springboot-shiro.html

## 例子:多对多

```java
/*
*RoleEntity
*/

package com.geniuscrh.wangluo.user.entity;

import javax.persistence.*;
import java.io.Serializable;
import java.util.List;

@Entity
@Table(name="user_role_table")
public class RoleEntity implements Serializable {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private long roleId;

    @Column(name="role_name")
    private  String roleName;

    @Column(name="role_type")
    private String roleType;

    @Column(name="role_description")
    private String roleDescription;

    @ManyToMany(fetch = FetchType.LAZY)
    @JoinTable(
            name="user_rolePermission_table",
            joinColumns = {@JoinColumn(name="role_id")},
            inverseJoinColumns = {@JoinColumn(name="permission_id")}
    )
    private List<PermissionEntity> permissionList;


 
/*
*PermissionEntity
*/

package com.geniuscrh.wangluo.user.entity;

import javax.persistence.*;
import java.io.Serializable;

@Entity
@Table(name="user_permission_table")
public class PermissionEntity implements Serializable {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private  long permissionId;

    @Column(name="permission_name")
    private String permissionName;


    public long getPermissionId() {
        return permissionId;
    }

    public void setPermissionId(long permissionId) {
        this.permissionId = permissionId;
    }

    public String getPermissionName() {
        return permissionName;
    }

    public void setPermissionName(String permissionName) {
        this.permissionName = permissionName;
    }
}


```



## 例子：多对一

```java
@Entity
@Table(name="key_table")
public class KeyEntity {

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private long keyId;
	
	@ManyToOne(fetch=FetchType.LAZY)
	@JoinColumn(name="user_card",referencedColumnName="user_card")
	private UserEntity user;
	
	@Column(name="borrow_time")
	private String borrowTime;
	
	@Column(name="return_time")
	private String returnTime;
	
	@Column(name="purpose")
	private String purpose;
	
	@Column(name="site_list")
	private String siteList;
	
	@Column(name="return_site_list")
	private String returnSiteList;

	public long getKeyId() {
		return keyId;
	}

	

}
```

