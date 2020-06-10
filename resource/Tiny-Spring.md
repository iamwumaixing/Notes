### Tiny-Spring

#### Step 1

- 创建一个容器来定义Bean，存储对象

```java
public class BeanDefinition {

    private Object bean;

    public BeanDefinition(Object bean) {
        this.bean = bean;
    }

    public Object getBean() {
        return bean;
    }
}
```

- 创建Bean实例工厂，讲Bean注册到Map中

```java
public class BeanFactory {
    private Map<String, BeanDefinition> beanMap = new HashMap<>();

    public Object getBean(String name) {
        return beanMap.get(name).getBean();
    }

    public void registerBeanDefinition(String name, BeanDefinition beanDefinition) {
        beanMap.put(name, beanDefinition);
    }
}
```

#### Step 2

- 改进Bean的初始化方式，Step1中是手动去new对象并放入容器中，在这一步中将通过反射来完成这一步骤
- 改写 BeanDefinition，新增 BeanClass 和 BeanClassName，用于通过反射的方式来创建对象

```java
public class BeanDefinition {
    @Getter
    @Setter
    private Object bean;

    @Getter
    @Setter
    private Class beanClass;

    @Getter
    private String beanClassName;

    public void setBeanClassName(String beanClassName) {
        this.beanClassName = beanClassName;
        try {
            this.beanClass = Class.forName(beanClassName);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```

- 抽象工厂的模式来获取 bean，定义一个 BeanFactory接口

```java
public interface BeanFactory {

    Object getBean(String name);

    void registerBeanDefinition(String name, BeanDefinition beanDefinition);

}
```

- 定义一个抽象工厂类 AbstractBeanFactory，定义并实现公共的方法

```java
public abstract class AbstractBeanFactory implements BeanFactory {

    private Map<String, BeanDefinition> beanMap = new ConcurrentHashMap<>();

    @Override
    public Object getBean(String name) {
        return beanMap.get(name).getBean();
    }

    @Override
    public void registerBeanDefinition(String name, BeanDefinition beanDefinition) {
        Object bean = doCreateBean(beanDefinition);
        beanDefinition.setBean(bean);
        beanMap.put(name, beanDefinition);
    }

    /**
     * 初始化 Bean
     *
     * @param beanDefinition
     * @return
     */
    protected abstract Object doCreateBean(BeanDefinition beanDefinition);
}
```

- 自动注入的实现类，通过Class对象来获取实例

```java
public class AutowireCapableBeanFactory extends AbstractBeanFactory {
    @Override
    protected Object doCreateBean(BeanDefinition beanDefinition) {
        try {
            return beanDefinition.getBeanClass().newInstance();
        } catch (InstantiationException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }
        return null;
    }
}
```

#### Step3

- 为 Bean 注入属性，同样利用反射获取字段的方式为 Bean 注入属性

```java
public class AutowireCapableBeanFactory extends AbstractBeanFactory {

    @Override
    protected Object doCreateBean(BeanDefinition beanDefinition) throws Exception {
        Object bean = createBeanInstance(beanDefinition);
        applyPropertyValues(bean, beanDefinition);
        return bean;
    }

    protected Object createBeanInstance(BeanDefinition beanDefinition) throws Exception {
        return beanDefinition.getBeanClass().newInstance();
    }

    protected void applyPropertyValues(Object bean, BeanDefinition beanDefinition) throws Exception {
        for (PropertyValue propertyValue : beanDefinition.getPropertyValues().getPropertyValues()) {
            Field declaredField = bean.getClass().getDeclaredField(propertyValue.getName());
            declaredField.setAccessible(true);
            declaredField.set(bean, propertyValue.getValue());
        }
    }
    
}
```

