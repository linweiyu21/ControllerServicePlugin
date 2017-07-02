## Mybatis Generator 的 Controller , Service 类文件生成插件
在 Spring 与 Mybatis 整合的项目中,使用 [MyBatis Generator](https://github.com/mybatis/generator) 生成代码后,还需要手动创建 Controller 与 Service 类,不是很优雅.
通过本插件,可以在使用 MyBatis Generator 生成代码的同时,生成对应的 Controller 与 Service 类.

### 生成的 Controller
```$xslt
package org.seckill.web;

import java.util.List;
import org.seckill.entity.User;
import org.seckill.service.UserService;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

@Controller
@RequestMapping("/users")
public class UserController {
    private final Logger logger = LoggerFactory.getLogger(this.getClass());

    @Autowired
    private UserService userService;

    @RequestMapping(value = "/{userId}", method = RequestMethod.DELETE, produces = {"application/json;charset=UTF-8"})
    public ResponseEntity<Integer> deleteByPrimaryKey(@PathVariable("userId") Long userId) {
        int resultCount = this.userService.deleteByPrimaryKey(userId);
        return new ResponseEntity<Integer>(resultCount,HttpStatus.OK);
    }

    @RequestMapping(value = "/", method = RequestMethod.POST, produces = {"application/json;charset=UTF-8"})
    public ResponseEntity<Integer> insert(User record) {
        int resultCount = this.userService.insert(record);
        return new ResponseEntity<Integer>(resultCount,HttpStatus.OK);
    }

    @RequestMapping(value = "/selective", method = RequestMethod.POST, produces = {"application/json;charset=UTF-8"})
    public ResponseEntity<Integer> insertSelective(User record) {
        int resultCount = this.userService.insertSelective(record);
        return new ResponseEntity<Integer>(resultCount,HttpStatus.OK);
    }

    @RequestMapping(value = "/{userId}", method = RequestMethod.PUT, produces = {"application/json;charset=UTF-8"})
    public ResponseEntity<Integer> updateByPrimaryKey(User record) {
        int resultCount = this.userService.updateByPrimaryKey(record);
        return new ResponseEntity<Integer>(resultCount,HttpStatus.OK);
    }

    @RequestMapping(value = "/selective/{userId}", method = RequestMethod.PUT, produces = {"application/json;charset=UTF-8"})
    public ResponseEntity<Integer> updateByPrimaryKeySelective(User record) {
        int resultCount = this.userService.updateByPrimaryKeySelective(record);
        return new ResponseEntity<Integer>(resultCount,HttpStatus.OK);
    }

    @RequestMapping(value = "/{userId}", method = RequestMethod.GET, produces = {"application/json;charset=UTF-8"})
    public ResponseEntity<User> selectByPrimaryKey(@PathVariable("userId") Long userId) {
        User user = this.userService.selectByPrimaryKey(userId);
        return new ResponseEntity<User>(user,HttpStatus.OK);
    }
}
```

### 生成的 Service
```$xslt
package org.seckill.service;

import java.util.List;
import org.seckill.entity.User;

public interface UserService {
    int deleteByPrimaryKey(Long userId);

    int insert(User record);

    int insertSelective(User record);

    User selectByPrimaryKey(Long userId);

    int updateByPrimaryKeySelective(User record);

    int updateByPrimaryKey(User record);
}
```

### 生成的 ServiceImpl
```$xslt
package org.seckill.service.impl;

import java.util.List;
import org.seckill.dao.UserMapper;
import org.seckill.entity.User;
import org.seckill.service.UserService;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class UserServiceImpl implements UserService {
    private final Logger logger = LoggerFactory.getLogger(this.getClass());

    @Autowired
    private UserMapper userMapper;

    public int deleteByPrimaryKey(Long userId) {
        return this.userMapper.deleteByPrimaryKey(userId);
    }

    public int insert(User record) {
        return this.userMapper.insert(record);
    }

    public int insertSelective(User record) {
        return this.userMapper.insertSelective(record);
    }

    public User selectByPrimaryKey(Long userId) {
        return this.userMapper.selectByPrimaryKey(userId);
    }

    public int updateByPrimaryKeySelective(User record) {
        return this.userMapper.updateByPrimaryKeySelective(record);
    }

    public int updateByPrimaryKey(User record) {
        return this.userMapper.updateByPrimaryKey(record);
    }
}
```

### 安装
下载此项目,在此项目根目录下执行以下 Maven 命令安装此项目到本地仓库
```
mvn install
 ```
 
### 使用
在 mybatis-generator-maven-plugin 插件中添加依赖
```
<!-- mybatis generator plugin -->
<plugin>
    <groupId>org.mybatis.generator</groupId>
    <artifactId>mybatis-generator-maven-plugin</artifactId>
    <version>1.3.5</version>
    <!-- 解决生成时无法找到JDBC驱动的错误 -->
    <dependencies>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.35</version>
        </dependency>
        
        <!-- 添加自定义的Controller,Service生成插件 -->
            <dependency>
                <groupId>org.linweiyu</groupId>
                <artifactId>ControllerServicePlugin</artifactId>
                <version>1.0-SNAPSHOT</version>
            </dependency>
        <!-- 添加自定义的Controller,Service生成插件 -->
        
    </dependencies>
    <configuration>
        <!-- 是否覆盖 -->
        <overwrite>true</overwrite>
        <!-- 生成时是否输出信息 -->
        <verbose>true</verbose>
    </configuration>
</plugin>
```

在 Mybatis 的代码生成配置文件(例如:generatorConfig.xml)中的 context 元素下配置此插件
```
<!-- 生成 Controller 与 Service 类 -->
<plugin type="org.linweiyu.ControllerServicePlugin">
    <!-- 是否生成 Controller 与 Service 类 -->
    <property name="generate.controller.service" value="true"/>
    <property name="targetProject" value="src/main/java"/>
    <property name="service.package" value="org.seckill.service"/>
    <!-- Controller 的包路径 -->
    <property name="web.package" value="org.seckill.web"/>
</plugin>
```

### 结束
本插件为本人学习 Mybatis 插件开发的成果,个人时间水平有限,生成的代码非常简单,仅适用于特定配置下的 Mybatis .如有不足或错误之处,请多多包涵.参考此插件,可以生成任何你想要的 Java 文件! Mybatis Generator 插件开发可以参考本人写的[文章](http://www.jianshu.com/p/b96043291b0d).谢谢.
