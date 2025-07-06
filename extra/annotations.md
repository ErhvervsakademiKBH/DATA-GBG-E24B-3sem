# Annotations

Annotations are a form of metadata that provide information to the compiler or runtime environment. They don’t change the program’s behavior directly but are used to give instructions or information.

### How to create an annotation?

```java
import java.lang.annotation.*;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface MyComponent {
}
```

**Explanation:**

* `@Retention`: Determines if the annotation is available at **runtime**, **compile-time**, etc.
* `@Target`: Specifies what the annotation can be applied to (methods, fields, classes, etc).

Since the `MyComponent` annotation specified on `ElementType.TYPE`, it means that it is a class level annotation - and can be used as so:

```java
@MyComponent
public class MyService {    
    public void myMethod() {
        System.out.println("Inside method");
    }
}
```

At the moment, the annotation does nothing - we want to add some functionality. So we create a class that scans for the annotation. For us to be able to scan for annotations, we will use reflection.

TODO: WRITE ABOUT REFLECTION

```java
public class AnnotationScanner {
    public static void scan(String basePackage) throws Exception {
        // Scan the basepackage and nested packages for the annotation
        // if it contains the annotation -> print to the console
        String path = basePackage.replace('.', '/');
        URL resource = Thread.currentThread().getContextClassLoader().getResource(path);

        if (resource == null) {
            System.out.println("Could not find package: " + basePackage);
            return;
        }

        File directory = new File(resource.toURI());
        scanDirectory(directory, basePackage);
    }

    private static void scanDirectory(File directory, String packageName) throws Exception {
        for (File file : Objects.requireNonNull(directory.listFiles())) {
            if (file.isDirectory()) {
                scanDirectory(file, packageName + "." + file.getName());
            } else if (file.getName().endsWith(".class")) {
                String className = packageName + '.' + file.getName().replace(".class", "");
                processClass(className);
            }
        }
    }

    private static void processClass(String className) {
        try {
            Class<?> clazz = Class.forName(className);
            if (clazz.isAnnotationPresent(MyComponent.class)) {
                System.out.println("Found @MyComponent on: " + className);
            }
        } catch (Throwable e) {
            System.err.println("Could not load class: " + className);
        }
    }
}
```

We can now in our entry point method, use the annotationscanner to scan all classes for a specific annotation.

```java
public class App {
    public static void main(String[] args) {
        AnnotationScanner.scan("kea.osnb");
    }
}
```
