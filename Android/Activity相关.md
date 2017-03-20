## 目录

- [Activity的四种加载模式](#activity的四种加载模式)







### Activity的四种加载模式

 我们在开发项目的过程中，会涉及到该应用中多个 Activity 组件之间的跳转，或者夹带其它应用的可复用的 Activity。
 例如我们可能希望跳转到原来某个 Activity 实例，而不是产生大量重复的 Activity。这样就需要我们为 Activity 配置特定的加载模式，而不是使用默认的加载模式。

Activity 有四种加载模式：

- standard 模式

  这是默认模式，每次激活 Activity 时都会创建 Activity 实例，并放入任务栈中。

- singleTop 模式

  如果在任务的栈顶正好存在该 Activity 的实例，就重用该实例 (会调用实例的 onNewIntent() )，
  否则就会创建新的实例并放入栈顶，即使栈中已经存在该 Activity 的实例，只要不在栈顶，都会创建新的实例。

- singleTask 模式

  如果在栈中已经有该 Activity 的实例，就重用该实例 (会调用实例的 onNewIntent() )。重用时，会让该实例回到栈顶，因此在它上面的实例将会被移出栈。如果栈中不存在该实例，将会创建新的实例放入栈中。

- singleInstance 模式

  在一个新栈中创建该 Activity 的实例，并让多个应用共享该栈中的该 Activity 实例。一旦该模式的 Activity 实例已经存在于某个栈中，任何应用再激活该 Activity 时都会重用该栈中的实例 ( 会调用实例的 onNewIntent() )。其效果相当于多个应用共享一个应用，不管谁激活该 Activity 都会进入同一个应用中。

> 设置启动模式的位置在 AndroidManifest.xml 文件中 Activity 元素的 Android:launchMode 属性。