# Spring bean的依赖注入--Spring学习之路2

***

***

Spring是一个轻量级的框架，Spring的特点之一是IOC（控制反转）。

首先，建立Spring-injection.xml，在src文件目录下，并在Project Structure的Modules添加到项目里面

![1](https://raw.githubusercontent.com/paddy10020/PersonalStudy-Spring/master/images/2/1.jpg) 

![2](https://raw.githubusercontent.com/paddy10020/PersonalStudy-Spring/master/images/2/2.jpg) 

然后在mian中建立四个类。在新建类用逗号分隔来表示在哪个包下，非常方便

* com.study.injection.service.InjectionService.class
* com.study.injection.service.InjectionServiceImpl.class
* com.study.injection.dao.InjectionDao.class
* com.study.injection.dao.InjectionDaoImpl.class

![3](https://raw.githubusercontent.com/paddy10020/PersonalStudy-Spring/master/images/2/3.jpg) 

并把其中InjectionService.class和InjectionDao.class改成接口    
InjectionDao:

    package com.study.injection.dao;
	public interface InjectionDao {
    	public void save(String arg);
	}

InjectionService:    

	package com.study.injection.service;
	public interface InjectionService {
    	public void save(String arg);
	}

填写InjectionServiceImpl和InjectionDaoImpl    
InjectionServiceImpl:

	package com.study.injection.service;
	import com.study.injection.dao.InjectionDao;
	public class InjectionServiceImpl implements InjectionService{
    	private InjectionDao injectionDao;
    //设值注入
    	public void setInjectionDao(InjectionDao injectionDao) {
        	this.injectionDao = injectionDao;
    	}

    	//构造器注入
    	public InjectionServiceImpl(InjectionDao injectionDao){
        	this.injectionDao = injectionDao;
    	}

    	@Override
    	public void save(String arg) {
        	System.out.println("InjectionService:" + arg);
        	arg = arg + this.hashCode();
        	injectionDao.save(arg);
    	}
	}

InjectionDaoImpl:    

	package com.study.injection.dao;
	public class InjectionDaoImpl implements InjectionDao{
    	@Override
    	public void save(String arg) {
        	System.out.println("InjectionDao:" + arg);
    	}
	}    


需要用到的测试的类已经建立完和填写完后，在test下建立测试类injection.TestInejction.class
<pre><code>
package injection;

import com.study.injection.service.InjectionService;
import org.junit.*;
import org.junit.runner.RunWith;
import org.junit.runners.BlockJUnit4ClassRunner;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.FileSystemXmlApplicationContext;

@RunWith(BlockJUnit4ClassRunner.class)
public class TestInjection {
    //设值注入
    @Test
    public void testSet(){
        ApplicationContext context = new FileSystemXmlApplicationContext("src/Spring-injection.xml");
        InjectionService service =(InjectionService) context.getBean("injectionService");
        service.save("test1:这是要保存的数据");
    }

    //构造注入
    @Test
    public void testCon(){
        ApplicationContext context = new FileSystemXmlApplicationContext("src/Spring-injection.xml");
        InjectionService service = (InjectionService) context.getBean("injectionService");
        service.save("这是要保存的数据");
    }
}
</pre></code>

说明，使用设值注入的时候把构造注入的代码注释掉，同理使用构造注入把设值注入注释

修改Spring-injection.xml的内容:

<pre><code>
&lt;?xml version="1.0" encoding="UTF-8"?>
&lt;beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    &lt;bean id="injectionService" class="com.study.injection.service.InjectionServiceImpl" >
        &lt;!--设值注入-->
        &lt;!--<property name="injectionDao" ref="injectionDao"></property>-->
        &lt;!--构造注入-->
        &lt;constructor-arg name="injectionDao" ref="injectionDao"></constructor-arg>
    &lt;/bean>
    &lt;bean id="injectionDao" class="com.study.injection.dao.InjectionDaoImpl"/>
&lt;/beans>
</pre></code>

输出单元测试结果为结果为：
![4](https://raw.githubusercontent.com/paddy10020/PersonalStudy-Spring/master/images/2/4.jpg) 
