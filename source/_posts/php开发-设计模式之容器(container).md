---
title: php开发-设计模式之容器(container)
abbrlink: aa1b125a
date: 2024-08-15 19:58:22
tags:
categories:
  - PHP开发
---

# 控制反转(IOC)

 含义：原来由人为（程序员）修改代码 控制流程，反转为由程序【框架】去控制
 控制反转(Inversion of Control) 是一种设计思想


没反转的情况(正转)
```php
 class Application {
      
      public function commit($supplierName)
      {
         // 去判断需要用哪个上游供应商，然后实例化
         if($supplierName == "pushang"){
            $supplier = new PuShangSupplier(); 
            $supplier->commit();
         }
         
      }
     
    }
```

反转后的情况（反转）
```php
    abstract class Supplier {
      
       abstract function commit();
    }
    
    class PuShangSupplier extend Supplier
    {
        public function commit()
        {
            // 逻辑
        }
    }
    
    class Application {
      
      public function commit(Supplier $supplier)
      {
        $supplier->commit();
      }
     
    }
    $app = new Application();
    $app->commit(new PuShangSupplier());
```

# 依赖注入(DI) 

依赖注入 是一种设计模式
依赖注入是控制反转的一种实现方式/方法
也就是依赖注入 实现了  控制反转

依赖注入方式 1、构造方法注入；2、set属性注入__set；3、静态工厂方法注入；

一旦换了平台，就要修改构造函数 即使你传参数 构造判断使用哪个类，新增了平台，你还有去修改A类构造方法
```php
class A{
    public $ob;
    public function __construct(C $c){
        $this->ob = new Aly();
      //  $this->ob = new Txy();
    }
    
    public function send(){
        $this->ob->send();
    }
}
// 实现阿里云
class Aly{}
// 实现腾讯云
class Txy{}

$a = new A();
$a->send();
```





通过构造函数注入 依赖
```php
class A{
    public $ob;
    public function __construct(C $c){
        $this->ob = $c;
    }
    
    public function send(){
        $this->ob->send();
    }
}
class Aly{
// 实现阿里云发送短信类
}
$notice = new A(new Aly());
$notice->send();
```

# IoC容器

将依赖的对象注入到容器中
当需要某个对象的时候，就直接获取，不必关注其实例化需要些什么，容器自动实例化了
运行时容器会自动解析依赖（存在子依赖也可以自动解析）

 ```php
<?php
class Container
{
    public $i=1;
    private $s = array();
    public function __set($k, $c)
    {
        $this->s[$k] = $c;
    }
    public function __get($k)
    {
        return $this->build($this->s[$k]);
    }
    /**
     * 自动绑定（Autowiring）自动解析（Automatic Resolution）
     *
     * @param string $className
     * @return object
     * @throws Exception
     */
    public function build($className)
    {
        // 如果是匿名函数（Anonymous functions），也叫闭包函数（closures）
        if ($className instanceof Closure) {
//            var_dump($className);exit;
            // 执行闭包函数，并将结果
            return $className($this);
        }
        /*通过反射获取类的内部结构，实例化类*/
        $reflector = new ReflectionClass($className);
        // 检查类是否可实例化, 排除抽象类abstract和对象接口interface
        if (!$reflector->isInstantiable()) {
            throw new Exception("Can't instantiate this.");
        }
        /** @var ReflectionMethod $constructor 获取类的构造函数 */
        $constructor = $reflector->getConstructor();
        // 若无构造函数，直接实例化并返回
        if (is_null($constructor)) {
            return new $className;
        }
        // 取构造函数参数,通过 ReflectionParameter 数组返回参数列表
        $parameters = $constructor->getParameters();

        // 递归解析构造函数的参数
        $dependencies = $this->getDependencies($parameters);
        // 创建一个类的新实例，给出的参数将传递到类的构造函数。
        return $reflector->newInstanceArgs($dependencies);
    }
    /**
     * @param array $parameters
     * @return array
     * @throws Exception
     */
    public function getDependencies($parameters)
    {
        $dependencies = [];
        /** @var ReflectionParameter $parameter */
        foreach ($parameters as $parameter) {
            /** @var ReflectionClass $dependency */
            $dependency = $parameter->getClass();
            if (is_null($dependency)) {
                // 是变量,有默认值则设置默认值
                $dependencies[] = $this->resolveNonClass($parameter);
            } else {
                // 是一个类，递归解析
                $dependencies[] = $this->build($dependency->name);
            }
        }
        return $dependencies;
    }
    /**
     * @param ReflectionParameter $parameter
     * @return mixed
     * @throws Exception
     */
    public function resolveNonClass($parameter)
    {
        // 有默认值则返回默认值
        if ($parameter->isDefaultValueAvailable()) {
            return $parameter->getDefaultValue();
        }
        throw new Exception('I have no idea what to do here.');
    }
}
require_once "./testclass.php"; //开始测试，先测试已知依赖关系的情况
$c = new Container();
$c->department = 'Department';// 赋值类名 或 匿名函数
$c->company = function ($c) {
    return new Company($c->department);
};
// 从容器中取得company
$company = $c->company;
$company->doSomething(); //输出: Group:hello|Department:hello|Company:hello|


// 测试未知依赖关系，直接使用的方法
$di = new Container();
$di->company = 'Company';// 赋值类名 或 匿名函数
$company = $di->company;
$company->doSomething();//输出: Group:hello|Department:hello|Company:hello|

```

```php
<?php //依赖关系：Company->Department->Group
class Group
{
    public function doSomething()
    {
        echo __CLASS__.":".'hello', '|';
    }
}
class Department
{
    private $group;
    public function __construct(Group $group)
    {
        $this->group = $group;
    }
    public function doSomething()
    {
        $this->group->doSomething();
        echo __CLASS__.":".'hello', '|';
    }
}
class Company
{
    private $department;
    public function __construct(Department $department)
    {
        $this->department = $department;
    }
    public function doSomething()
    {
        $this->department->doSomething();
        echo __CLASS__.":".'hello', '|';
    }
}
```





