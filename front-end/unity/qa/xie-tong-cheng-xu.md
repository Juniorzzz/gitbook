# 协同程序

&#x20;引用 [https://zhuanlan.zhihu.com/p/72882324](https://zhuanlan.zhihu.com/p/72882324)

协同程序——在主程序运行的同时开启另一段逻辑处理，来协同当前程序的执行，开启协同程序就是开启一个线程。 协同程序（coroutine）与多线程情况下的线程比较类似：有自己的堆栈，自己的局部变量，有自己的指令指针（IP，instructionpointer），但与其它协同程序共享全局变量等很多信息。 \[与线程的区别] 线程和协同程序的主要不同在于：在多处理器情况下，从概念上来讲多线程程序同时运行多个线程； 而协同程序是通过协作来完成，在任一指定时刻只有一个协同程序在运行，并且这个正在运行的协同程序只在必要时才会被挂起。 【开启协同程序】 使用MonoBehavior.StartCoroutine可以开启一个协同程序，所以该方法需要在继承Monobehaviour的类中调用 Unity3D中，使用StartCoroutine（StringmethodName）和startCoroutine（IEnumeratorroutine）都可以开启一个线程， a.使用字符串作为参数可以开启线程并在线程结束之前终止线程，开启线程最多只能传递一个参数 b.使用IEnumeraotor开启的的线程不能随时终止（除非使用StopAllCoroutines（）方法） 没有参数个数的限制 【终止协同程序】 a.Unity使用StopCoroutine（StringmethodName）来终止一个协同程序，使用StopAllCoroutine来终止所有协同程序，但是这两个方法都只能终止该MonoBehaviour中的协同程序 b.将协同程序所在的gameObject的acitve属性设置为false，当再次设置active为true的时候，协同程序不会再开启，而设置enable和false则不会生效，因为协同程序开启后是以一个线程在运行的，它与MonoBehavior是互不干扰的模式在运行，此后除非代码调用，他们共同作用于同一个对象，只有当对象不可见的时候才能够同时终止这两个线程。
