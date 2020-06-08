# if...else优化
## 表驱动
通过某种映射关系，将逻辑表达式用表格的方式表示；再使用表格查找的方式，找到某个输入所对应的处理函数，使用这个处理函数进行运算。
##### 优化前
```
if (param.equals(value1)) {
    doAction1(someParams);
} else if (param.equals(value2)) {
    doAction2(someParams);
} else if (param.equals(value3)) {
    doAction3(someParams);
}
```
##### 优化后
```
Map<?, Function<?> action> actionMappings = new HashMap<>(); // 这里泛型 ? 是为方便演示，实际可替换为你需要的类型
// When init
actionMappings.put(value1, (someParams) -> { doAction1(someParams)});
actionMappings.put(value2, (someParams) -> { doAction2(someParams)});
actionMappings.put(value3, (someParams) -> { doAction3(someParams)});

// 省略 null 判断
actionMappings.get(param).apply(someParams);
```
## 责任链模式
条件表达式灵活多变，没有统一的形式。
##### 优化前
```
public void handle(request) {
    if (handlerA.canHandle(request)) {
        handlerA.handleRequest(request);
    } else if (handlerB.canHandle(request)) {
        handlerB.handleRequest(request);
    } else if (handlerC.canHandle(request)) {
        handlerC.handleRequest(request);
    }
}
```
##### 优化后
```
public void handle(request) {
  handlerA.handleRequest(request);
}

public abstract class Handler {
  protected Handler next;
  public abstract void handleRequest(Request request);
  public void setNext(Handler next) { this.next = next; }
}

public class HandlerA extends Handler {
  public void handleRequest(Request request) {
    if (canHandle(request)) doHandle(request);
    else if (next != null) next.handleRequest(request);
  }
}
```
## 注解驱动
- 适用场景  
适合条件分支很多多，对程序扩展性和易用性均有较高要求的场景。通常是某个系统中经常遇到新需求的核心功能。

## 事件驱动
- 适用场景  
事件驱动可以看做是表驱动的一种，但从实践角度讲，事件驱动和前面提到的表驱动有多处不同。具体来说：
1. 表驱动通常是一对一的关系；事件驱动通常是一对多；
2. 表驱动中，触发和执行通常是强依赖；事件驱动中，触发和执行是弱依赖  
事件驱动可用于如订单支付完成触发库存、物流、积分等功能。  

- 实现与示例
单机的实践驱动可以使用 Guava、Spring 等框架实现  
分布式的则一般通过各种消息队列方式实现

## 有限状态机
- 适用场景  
在很多场景中，如协议栈、订单处理等功能中，状态机有这其天然的优势。因为这些场景中天然存在着状态和状态的流转。  
- 实现与示例
  - Apache Mina State Machine
  - Spring State Machine

## Optional
- 使用场景
有较多用于非空判断的 if...else。  
##### 优化前
```
String str = "Hello World!";
if (str != null) {
    System.out.println(str);
} else {
    System.out.println("Null");
}
```
##### 优化后
```
Optional<String> strOptional = Optional.of("Hello World!");
strOptional.ifPresentOrElse(System.out::println, () -> System.out.println("Null"));
```

## Assert 模式
- 使用场景
通常用于各种参数校验  
- 框架类库
  - Apache Commons Lang 中的 Validate 类
  - Spring 的 Assert 类

## 多态
- 使用场景
一般来说，当一个类中的多个方法都有类似于示例中的 if...else 判断，且条件相同，那就可以考虑使用多态的方式消除 if...else

# if...else嵌套过深
##### 表现
```
if (condition1) {
    action1();
    if (condition2) {
        action2();
        if (condition3) {
            action3();
            if (condition4) {
                action4();
            }
        }
    }
}
```
## 抽取方法
##### 重构前
```
public void add(Object element) {
  if (!readOnly) {
    int newSize = size + 1;
    if (newSize > elements.length) {
      Object[] newElements = new Object[elements.length + 10];
      for (int i = 0; i < size; i++) {
        newElements[i] = elements[i];
      }
      
      elements = newElements
    }
    elements[size++] = element;
  }
}
```
##### 重构后
```
public void add(Object element) {
  if (readOnly) {
    return;
  }
  
  if (overCapacity()) {
    grow();
  }
  
  addElement(element);
}
```

## 卫语句
##### 重构前
```
double getPayAmount() {
    double result;
    if (_isDead) result = deadAmount();
    else {
        if (_isSeparated) result = separatedAmount();
        else {
            if (_isRetired) result = retiredAmount();
            else result = normalPayAmount();
        };
    }
    return result;
}
```
##### 重构后
```
double getPayAmount() {
    if (_isDead) return deadAmount();
    if (_isSeparated) return separatedAmount();
    if (_isRetired) return retiredAmount();
    return normalPayAmount();
}
```

# if...else 表达式过于复杂
##### 示例
```
if ((condition1 && condition2 ) || ((condition2 || condition3) && condition4)){
   ...
 }
```
##### 方案
抽取方法、移动方法等手段解决


