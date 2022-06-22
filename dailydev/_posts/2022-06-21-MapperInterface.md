---
layout: post
title: Mapper Interface
description: >
  @Mapper 적용하기
hide_description: false
category: dailydev
---


- Table of Contents
{:toc .large-only}

@Mapper 어노테이션과 @Repository의 차이점과 사용법을 알려보려고 한다.

## Spring 웹 계층
![Full-width image](/assets/img/blog/Dao.PNG)
DAO Spring 웹 계층 구조
{:.figure}

![Full-width image](/assets/img/blog/Mapper.PNG)
Mapper Interface Spring 웹 계층 구조
{:.figure}


##@Repository

먼저 DAO Interface를 사용하여 DAO클래스를 구현할때를 알아보겠다.

클라이언트 요청 > @Controller - @Service - @Repository - Mapper.xml 의 흐름이다.
dao구현 클래스에 @Autowired를 통해 sqlSession을 주입해주어야 한다. 자동으로 빈에 등록 되어 있지 않기 때문에
SqlSessionFactory와 SqlSessionTemplate을 추가해주어야 하고

DAO 구현 클래스 
```java

@Repository
public class exDaoImpl{
	@Autowired
	SqlSession sqlSession;
	
	public void exinsert(int num){
		sqlSession.insert("example.exinsert", num);
	}
}

```
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="example">
	<insert id="exinsert" parameterType="int">
		insert sql
	</insert>
</mapper>	

```
예시 코드 처럼 sqlSession 메서드를 사용하여 mapper 의 id와 일치하는 sql문을 실행하는 방식이다.


## Mapper Interface

@Mapper 어노테이션을 사용하면 
따로 구현 클래스를 작성하지 않아도 된다.
MyBatis 3.0 부터 지원한다.

클라이언트 요청 > @Controller - @Service - @Mapper - Mapper.xml 의 흐름이다.
```java
@Mapper
public interface exMapper{

	public void exinsert(int num);
}

```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="@mapper의 경로">
	<insert id="insertRouteCell" parameterType="routeCellVO">
		insert into lrms_route_cell 
		(route_id, version_id, dir, lane, 
		extension, poly, is_cross, is_bridge, 
		centerX, centerY, wgsX, wgsY)
		values
		( #{route_id}, #{version_id}, #{dir}, #{lane}, 
		#{extension}, GeomFromText(#{poly}), #{is_cross}, #{is_bridge}, 
		#{centerX}, #{centerY}, #{wgsX}, #{wgsY})		
	</insert>

```
위처럼 @Mapper를 사용하면 작성한 인터페이스의 메소드 이름과 Mapper.xml에 작성한 Sql id는 일치하고
xml의 namespace는 인터페이스의 경로를 작성하기만 하면 사용 할 수 있다.

![Full-width image](/assets/img/blog/MyBatis.PNG)
Mapper Interface는 Mapperxml 파일과 DTO, VO 를 통해 데이터베이스와 서비스 계층에 접근할 수 있다.
{:.figure}
