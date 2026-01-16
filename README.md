# Pants + top-level Scala definitions

This reproduces a bug with [Pants](https://github.com/pantsbuild/pants) and top-level definitions in Scala 3.

There are two Scala files here:

1. `src/main/scala/example/Foo.scala` with contents
    ```scala
    package example
    val foo = "foo"
    ```
2. `src/main/scala/example/Bar.scala` with contents
    ```scala
    package example
    val bar = "foo"
    ```

Compiling the project with `pants check ::` results in an error:

```scala
-- [E006] Not Found Error: main/scala/example/Bar.scala:2:10 -------------------
2 |val bar = foo
  |          ^^^
  |          Not found: foo
  |
```

However if I modify the files so that `val foo` is not a top-level definition, but is instead defined within an `object`, then everything works:

```diff
diff --git a/src/main/scala/example/Bar.scala b/src/main/scala/example/Bar.scala
index 660a746..3002eaa 100644
--- a/src/main/scala/example/Bar.scala
+++ b/src/main/scala/example/Bar.scala
@@ -1,2 +1,2 @@
 package example
-val bar = foo
+val bar = Foo.foo
diff --git a/src/main/scala/example/Foo.scala b/src/main/scala/example/Foo.scala
index 782ccaf..a808ceb 100644
--- a/src/main/scala/example/Foo.scala
+++ b/src/main/scala/example/Foo.scala
@@ -1,2 +1,4 @@
 package example
-val foo = "foo"
+object Foo {
+  val foo = "foo"
+}
```
