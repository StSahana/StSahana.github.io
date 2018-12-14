# Spring-data-mongoDB执行mongoDB存储过程

spring-data-mongoDB 从1.7开始支持mongoDB的存储过程，因此需要导入spring-data-mongoDB 1.7版本以上的包。

## 调用远程服务器脚本

+ 服务器端脚本如下，因为本文的重点在调用上，使用的脚本很简单

  ```javascript
  //db.system.js.find({_id:"test"})
  {
      "_id" : "test",
      "value" : function() {
            var doc=db.getCollection("user").find({});
            return doc;
  		}
  }
  ```


+ 用于接收数据的java Bean

  ```java
  @Document(collection="user")
  	class DemoBean{
  		@Field("user_name")
  		private String userName;
  		private String password;
  		public String getUserName() {
  			return userName;
  		}
  		public void setUserName(String userName) {
  			this.userName = userName;
  		}
  		public String getPassword() {
  			return password;
  		}
  		public void setPassword(String password) {
  			this.password = password;
  		}
  		
  	}
  ```

+ java调用代码

  ```java
  ScriptOperations scriptOps = mongoTemplate.scriptOps();
  List<UserDemo> call = (List<UserDemo>) scriptOps.call("test");//执行服务器端脚本
  //call(String script,Object...args)参数放在脚本名称之后
  System.out.println(call);
  ```

##　本地编写脚本并放到mongoDB服务器执行

+ 直接执行

  ```java
  ScriptOperations scriptOps = mongoTemplate.scriptOps();
  ExecutableMongoScript script = new ExecutableMongoScript("function(x) { return x; }");
  scriptOps.execute(script, "directly execute script");  
  ```

+ 执行并保存到数据库

  ```java
  ScriptOperations scriptOps = mongoTemplate.scriptOps();
  ExecutableMongoScript script = new ExecutableMongoScript("function(x) { return x; }");
  scriptOps.register(new NamedMongoScript("test", script)); //指定脚本名称
  scriptOps.call("test", "execute script via name");//调用远程脚本
  ```

  ​



