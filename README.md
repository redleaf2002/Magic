# magic
每个module向其他的组件提供的能力，可以通过一个接口去封装，这个接口的实现也是在自己的组件中实现。
在其他的组件中通过这个框架去直接调用这个接口的实现。


## 特点
实现了各个module功能的封装，有利于各个module的解耦

## 使用:
#### 在需要使用的module的build gradle中增加moduleName和两个依赖

    defaultConfig {
          javaCompileOptions {
            annotationProcessorOptions {
                includeCompileClasspath = true
                arguments = [moduleName: project.getName()]
            }
        }
    }
    
    dependencies {
    implementation 'com.leaf:magic-provider:1.0.1'
    implementation 'com.leaf:magic-compiler:1.0.3'
    }
    
   如果是kotlin的module：
   dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation "org.jetbrains.kotlin:kotlin-stdlib-jdk7:${KOTLIN_VERSION}"
    implementation 'com.leaf:magic-provider:1.0.1'
    kapt 'com.leaf:magic-compiler:1.0.3'
}
    

#### 1. 需要注解实现的接口

```java
public interface MyTestProvider {
    String getCount();
}

//kotlin：

interface DemoProvider {
    fun getDemoName(): String
}

```


#### 2. 注解接口的实现

```java
import com.leaf.magic.annotation.Provider;

@Provider(provider = MyTestProvider.class)
public class MyTestProviderImpl implements MyTestProvider {
    private int count = 100;

    @Override
    public String getCount() {
        count += 100;
        return "MyTestProvider count = " + count;
    }
}


import com.leaf.magic.annotation.Provider

@Provider(provider = DemoProvider::class)
class DemoProviderImpl : DemoProvider {
    override fun getDemoName(): String {
        return "module name is demo"
    }
}

```



#### 3. 在需要的地方调用
```java
 MyTestProvider myTestProvider1 = (MyTestProvider) Magic.getInstance().getServiceInstance(MyTestProvider.class);
        if (myTestProvider1 != null) {
            Log.i("Magic", "myTestProvider1: " + myTestProvider1.getCount());
            textView1.setText(myTestProvider1.getCount());
        }

        MyTestProvider myTestProvider2 = (MyTestProvider) Magic.getInstance().getServiceInstance(MyTestProvider.class);
        if (myTestProvider2 != null) {
            Log.i("Magic", "myTestProvider2: " + myTestProvider2.getCount());
            textView2.setText(myTestProvider2.getCount());

        }

        MyTestProvider myTestProvider3 = (MyTestProvider) Magic.getInstance().generateServiceInstance(MyTestProvider.class);
        if (myTestProvider3 != null) {
            Log.i("Magic", "myTestProvider3: " + myTestProvider3.getCount());
            textView3.setText(myTestProvider2.getCount());

        }

        MyTestProvider myTestProvider4 = (MyTestProvider) Magic.getInstance().generateServiceInstance(MyTestProvider.class, 100);
        if (myTestProvider4 != null) {
            Log.i("Magic", "myTestProvider4: " + myTestProvider4.getCount());
            textView4.setText(myTestProvider4.getCount());

        }
       
        DemoProvider demoProvider = (DemoProvider) Magic.getInstance().getServiceInstance(DemoProvider.class);
        if (demoProvider != null) {
            Log.i("Magic", "demo module: " + demoProvider.getDemoName());
            textView5.setText(demoProvider.getDemoName());
        }

```

###### 结果：

```java
I/Magic: myTestProvider1: MyTestProvider count = 200
I/Magic: myTestProvider2: MyTestProvider count = 400
I/Magic: myTestProvider3: MyTestProvider count = 200
I/Magic: myTestProvider4: MyTestProviderImpl2 count = 10000
I/Magic: demo module: module name is demo

```

 







