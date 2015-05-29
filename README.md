# Auto Dagger2

Auto Dagger2 is an annotation processor built on top of Dagger2 annotation processor.  
It basically generates the component for you.

The goal is to reduce the boilerplate code when you have simple Dependency Injection strategy, with lot of "empty components". It can usually the case in Android development.  

You can also mix manually written components with the ones generated by Auto Dagger2. Auto Dagger2 produces very simple and human-readable code.


## Getting started

```java
@AutoComponent
@DaggerScope(ExampleApplication.class) // DaggerScope is a custom @Scope annotation
public class ExampleApplication extends Application { 
}
```

It will generate the `ExampleApplicationComponent`

```java
@Component
@DaggerScope(ExampleApplication.class)
public interface ExampleApplicationComponent { 
}
```

As you can see, the DaggerScope annotation is applied to the generated component as well.


## API

### @AutoComponent

Annotate a class with @AutoComponent to generated an associated Component.
On the component, you can add dependencies, modules and superinterfaces.

```java
@AutoComponent(
    dependencies = ExampleApplication.class,
    modules = MainActivity.Module.class,
    superinterfaces = {ExampleApplication.class, GlobalComponent.class})
@DaggerScope(MainActivity.class)
public class MainActivity extends Activity {
}
```

It will generate the following `MainActivityComponent`

```java
@Component(
    dependencies = ExampleApplicationComponent.class,
    modules = MainActivity.Module.class
)
@DaggerScope(MainActivity.class)
public interface MainActivityComponent extends ExampleApplicationComponent, GlobalComponent {
}
```



### @AutoInjector

With `@AutoInjector`, you can add injector methods inside a generated component.  
An injector method looks like: `void inject(MyObject object)`.

```java
@AutoInjector(MainActivity.class)
public class ObjectA {
}
```

It will update the `MainActivityComponent` by adding the following method:

```java
@Component(
    dependencies = ExampleApplicationComponent.class,
    modules = MainActivity.Module.class
)
@DaggerScope(MainActivity.class)
public interface MainActivityComponent extends ExampleApplicationComponent, GlobalComponent {
  void inject(ObjectA objectA);
}
```

If you apply the `@AutoInjector` on the same class that has the `@AutoComponent` annotation, you can skip the value member:

```java
@AutoComponent(
    dependencies = ExampleApplication.class,
    modules = MainActivity.Module.class,
    superinterfaces = {ExampleApplication.class, GlobalComponent.class})
@AutoInjector
@DaggerScope(MainActivity.class)
public class MainActivity extends Activity {
}
```


### @AutoExpose

In the same way, you can also add expose methods to generated components.  
An expose method looks like: `MyObject myObject()`.

```java
@AutoExpose(MainActivity.class)
@DaggerScope(MainActivity.class)
public class SomeObject {

    @Inject
    public SomeObject() {
    }
}
```

The generated component will have added the following method:

```java
@Component(
    dependencies = ExampleApplicationComponent.class,
    modules = MainActivity.Module.class
)
@DaggerScope(MainActivity.class)
public interface MainActivityComponent extends ExampleApplicationComponent, GlobalComponent {
  SomeObject someObject();
}
```

If you apply the `@AutoInjector` on the same class that has the `@AutoComponent` annotation, you can skip the value member:

```java
@AutoComponent(
    dependencies = ExampleApplication.class,
    modules = MainActivity.Module.class,
    superinterfaces = {ExampleApplication.class, GlobalComponent.class})
@AutoExpose
@DaggerScope(MainActivity.class)
public class MainActivity extends Activity {
}
```

You can also apply `@AutoExpose` on a provides method:

```java
@dagger.Module
public static class Module {
    @Provides
    @DaggerScope(MainActivity.class)
    @AutoExpose(MainActivity.class)
    public SomeOtherObject providesSomeOtherObject() {
        return new SomeOtherObject();
    }
}
```


## Dagger scope

Whenever you use `@AutoComponent`, you also need to annotate the class with a dagger scope annotation (an annotation that is itself annotated with `@Scope`).
Auto Dagger2 will detect this annotation, and will apply it on the generated component.

If you don't provide scope annotation, the generated component will be unscoped.


## Installation

Gradle apt plugin recommended, like for Dagger 2.

```groovy
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:1.1.3'
        classpath 'com.github.dcendents:android-maven-plugin:1.2'
    }
}

apply plugin: 'com.android.application'
apply plugin: 'com.neenbedankt.android-apt'

repositories {
    jcenter()
    maven { url "https://oss.sonatype.org/content/repositories/snapshots/" }
}

dependencies {
    apt 'com.github.lukaspili:autodagger2-compiler:1.0'
    compile 'com.github.lukaspili:autodagger2:1.0'
}
```


## Status

Stable API.  

Auto Dagger2 was extracted from Auto Mortar to work as a standalone library.  
You can find more about Auto Mortar here:
[https://github.com/lukaspili/Auto-Mortar](https://github.com/lukaspili/Auto-Mortar)


## Author

- Lukasz Piliszczuk ([@lukaspili](https://twitter.com/lukaspili))


## License

Auto Dagger2 is released under the MIT license. See the LICENSE file for details.