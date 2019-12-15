# ImportSelector作用
用来导入外部的类，通常开发框架用

# ImportSelector使用


1. 实现接口: ImportSelector
```
public interface ImportSelector {

    /**
     * Select and return the names of which class(es) should be imported based on
     * the {@link AnnotationMetadata} of the importing @{@link Configuration} class.
     */
    String[] selectImports(AnnotationMetadata importingClassMetadata);

}
```
该接口不能返回null，否则报NPE。

返回需要导入的类名数组

2. @Import(MyImportSelector.class)

# 参考

- [深入理解Spring的ImportSelector接口](https://www.cnblogs.com/niechen/p/9262452.html)