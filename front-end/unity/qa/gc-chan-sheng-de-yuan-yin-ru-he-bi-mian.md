# GC产生的原因，如何避免

当用new创建一个对象时，当可分配的内存不足GC就会去回收未使用的对象，但是GC的操作是非常复杂的，会占用很多CPU时间，对于移动设备来说频繁的垃圾回收会严重影响性能。下面的建议可以避免GC频繁操作。&#x20;

1. 减少用new创建对象的次数，在创建对象时会产生内存碎片，这样会造成碎片内存不法使用&#x20;
2. 使用公用的对象（静态成员，常量），但是不能乱用，因为静态成员和常量的生命周期是整个应用程序。
3. 在拼接大量字符串时StringBuilder。在使用注意，创建StringBuilder对象时要设置StringBuilder的初始大小如： StringBuilder sbHtml = new StringBuilder (size);&#x20;
4. 使用object pool(对象池)
