

# 内部类

* 可以将一个类定义在另一个类的定义内部，这就是内部类

 

## 创建内部类

```java
package Internal;

/**
 * @author lenovo
 * 连接到外部类
 */
class CiteinternalClass{

    CiteinternalClass(int i){
        System.out.println("CiteinternalClass构造器："+i);
    }

    void say(int i){
        System.out.println("CiteinternalClass成员方法"+i);
    }
}

public class LinkExternal {

    private int i;

    /**组合的引用类*/

    CiteinternalClass internalClass;

    class internalClass{

        internalClass(int i){
            System.out.println("internalClass构造器："+i);
        }
    }

    public LinkExternal(int i) {
        this.i = i;

    }

    public  void link(){
        //引用类创建对象
        internalClass=new CiteinternalClass(i);
        //引用类
        internalClass.say(i);
        //内部类
        new internalClass(i);
    }

    public static void main(String[] args) {
        new LinkExternal(1).link();

    }

}

```

从上面代码我们可以知道：当你生成一个内部类对象的时候，此对象与制造它的外围类会形成一种联系，内部类可以**直接**访问外围类的所有元素

## 内部类与向上转型

```java
package Internal;

interface BookingFactory{
    public void book();
}

interface BookingService{
    public void bookService();
}
public class internal_tranform {

    private  class localBookingFactory implements BookingFactory{
        @Override
        public void book() {
            System.out.println("localBookingFactory");
        }
    }

    protected class RemoteBookingFactory implements BookingService{

        @Override
        public void bookService() {
            System.out.println("RemoteBookingFactory");
        }
    }

    public localBookingFactory getLocalBookingFactory(){
        return new localBookingFactory();
    }

    public RemoteBookingFactory getRemoteBookingFactory(){
        return new RemoteBookingFactory();
    }

    public static void main(String[] args) {
        //我们使用了接口，我们封装了内部类，也实现了多态
        internal_tranform.localBookingFactory e = new internal_tranform().getLocalBookingFactory();
        e.book();
        BookingFactory example=new internal_tranform().getLocalBookingFactory();
        example.book();

    }
}

class test{
    public static void main(String[] args) {
//        这里会出现报错你，因为本身localBookingFactory类就是私有的内部类，只有在外部类下才可以访问到
//        internal_tranform.localBookingFactory e = new internal_tranform().getLocalBookingFactory();
//        e.book();
    }
}

```

我们从上面的代码可以看出当我们将内部类定义为private，protected的时候，除非在我们的类中，别的地方不可以被使用，这就造成很好的封装性，隐藏了具体实现的细节。通过调用接口，也可以向上转型，实现了内部可扩展性。这样的好处，类的内部互相调用非常安全且可扩展

https://www.jianshu.com/p/df18858d0378