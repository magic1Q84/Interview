### 知道threadlocal吗，是什么？

threadlocal而是一个线程内部的存储类，可以在指定线程内存储数据，数据存储以后，只有指定线程可以得到存储数据，官方解释如下。

    This class provides thread-local variables.  These variables differ from
    their normal counterparts in that each thread that accesses one (via its
    {@code get} or {@code set} method) has its own, independently initialized
    copy of the variable.  {@code ThreadLocal} instances are typically private
    static fields in classes that wish to associate state with a thread (e.g.,
    a user ID or Transaction ID).
