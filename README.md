# property-


     @property内存管理策略的选择
      非ARC
      1> copy : 只用于NSString\block；
      2> retain : 除NSString\block以外的OC对象；
      3> assign : 基本数据类型、枚举、结构体（非OC对象），当2个对象相互引用，一端用retain，一端用assign。
 
      
      ARC
      1> copy : 只用于NSString\block；
      2> strong : 除NSString\block以外的OC对象；
      3> weak : 当2个对象相互引用，一端用strong，一端用weak；
      4> assgin : 基本数据类型、枚举、结构体（非OC对象）。


在O-C里面有个值对象的概念，当你新定义一个属性是值对象时就应该用copy来修饰。那么都什么对象是值对象呢？

值对象是指封装了基本值（属于 C 数据类型）且提供与该值相关的服务的对象。值对象以对象形式表示标量类型。Foundation 框架向您提供了以下类（这些类产生对象，用于字符串、二进制数据、日期与时间、数字以及其他值）：
NSString和NSMutableString

NSData和NSMutableData

NSDate

NSNumber

NSValue

读前小提示：对于深浅复制有一个清楚的了解，对于学习oc的朋友来说，至关重要。那么首先，我们要明白深浅复制是如何定义的呢。这里为了便于朋友们理解，定义如下。


   浅 复 制：在复制操作时，对于被复制的对象的每一层复制都是指针复制。
   深 复 制：在复制操作时，对于被复制的对象至少有一层复制是对象复制。
   完全复制：在复制操作时，对于被复制的对象的每一层复制都是对象复制。

        注：1、在复制操作时，对于对象有n层是对象复制，我们可称作n级深复制，此处n应大于等于1。
              2、对于完全复制如何实现（目前通用的办法是：迭代法和归档），这里后续是否添加视情况而定，
              暂时不做讲解。
          3、指针复制俗称指针拷贝，对象复制也俗称内容拷贝。
          4、一般来讲，
                 浅层复制：复制引用对象的指针。
 

               深层复制：复制引用对象内容。
            这种定义在多层复制的时候，就显得模糊。所以本文定义与它并不矛盾。
            反而是对它的进一步理解和说明。           

retain：始终是浅复制。引用计数每次加一。返回对象是否可变与被复制的对象保持一致。

copy：对于可变对象为深复制，引用计数不改变;对于不可变对象是浅复制，
         引用计数每次加一。始终返回一个不可变对象。

mutableCopy：始终是深复制，引用计数不改变。始终返回一个可变对象。

不可变对象：值发生改变，其内存首地址随之改变。
   可变对象：无论值是否改变，其内存首地址都不随之改变。
   引用计数：为了让使用者清楚的知道，该对象有多少个拥有者（即有多少个指针指向同一内存地址）。
 
 最近有一个好朋友问我，什么时候用到深浅复制呢？那么我就把我所总结的一些分享给大家，希望能帮助你们更好的理解深浅复制！

那么先让我们来看一看下边数组类型的转换
1、不可变对象→可变对象的转换：
       NSArray *array1= [NSArray arrayWithObjects:@"a",@"b",@"c",@"d",nil];
       NSMutableArray  *str2=[array1 mutableCopy];
2、可变对象→不可变对象的转换：
    NSMutableArray *array2   = [NSMutableArray arrayWithObjects:@"aa",@"bb",@"cc",@"dd",nil];
       NSArray *array1=[  array2    Copy];
3、可变对象→可变对象的转换（不同指针变量指向不同的内存地址）：
       NSMutableArray *array1= [NSMutableArray arrayWithObjects:@"a",@"b",@"c",@"d",nil];
       NSMutableArray  *str2=[array1 mutableCopy];
通过上边的两个例子，我们可轻松的将一个对象在可变和不可变之间转换，并且这里不用考虑内存使用原则（即引用计数的问题）。没错，这就是深拷贝的魅力了。
4、同类型对象之间的指针复制（不同指针变量指向同一块内存地址）：
  a、
   NSMutableString *str1=[NSMutableString stringWithString:@"two day"];
   NSMutableString *str2=[str1   retain];
   [str1  release];
  b、
   NSArray *array1= [NSArray arrayWithObjects:@"a",@"b",@"c",@"d",nil];
   NSArray  *str2=[array1 Copy];
   [array1 release];
 
   通俗的讲，多个指针同时指向同一块内存区域，那么这些个指针同时拥有对该内存区的所有权。所有权的瓜分过程，这时候就要用到浅拷贝了。
则简化为：
问：什么时候用到深浅拷贝？
答：深拷贝是在要将一个对象从可变（不可变）转为不可变（可变）或者将一个对象内容克隆一份时用到；
     浅拷贝是在要复制一个对象的指针时用到。

 
亲爱的读者朋友，下面是我用于验证的详细代码。对于验证还能得出什么结论，我希望朋友们能自己多多发掘一下。这里只做以上几点总结。对于本文有任何疑问请与我联系，欢迎指出本文不足的地方，谢谢！
复制代码
 #import<Foundation/Foundation.h>

int main (int argc, const char * argv[])

{

    @autoreleasepool {

 

    ===========================第一种：非容器类不可变对象==================

  

        NSString *str1=@"one day";

        

          printf("\n初始化赋值引用计数为::::%lu",str1.retainCount);

        NSString *strCopy1=[str1 retain];

          printf("\n继续retain引用计数为:::%lu",str1.retainCount);

        NSString *strCopy2=[str1 copy];

          printf("\n继续copy后引用计数为::::%lu",str1.retainCount);

        NSString *strCopy3=[str1 mutableCopy];

                printf("\n继续mutableCopy后为:::%lu\n",str1.retainCount);

        

        printf("\n非容器类不可变对象\n原始地址::::::::::%p",str1);

        printf("\nretain复制::::::::%p",strCopy1);

        printf("\ncopy复制::::::::::%p",strCopy2);

        printf("\nmutableCopy复制:::%p",strCopy3);

    //这里说明该类型不存在引用计数的概念

  // 初始化赋值引用计数为：18446744073709551615

  // 继续retain引用计数为：18446744073709551615

  // 继续copy后引用计数为：18446744073709551615

  // 继续mutableCopy后为：18446744073709551615

 小提示：这里很多人都说是赋值，所以就好解释这里没引用计数的概念。而且也能解释为什么

         NSString *strCopy2=[str1 copy];

         NSMutableString  *strCopy2=[str1 copy]; 

         这样都不会报错的原因了。那既然只是简单赋值为什么要这么麻烦呢，直接

         NSString *strCopy2=*str1;

         NSMutableString  *strCopy2=*str1;

         其实大家都看出来了，这里是指针变量，只存在“指针的复制”，

         跟赋值概念完全不同，虽然这里看起来很像。

         原来该类型是字符串常量时，系统会为我们优化，声明了多个字符串，

              但是都是常量，且内容相等，那么系统就只为我们申请一块空间。

  疑问： 深复制=浅复制+赋值吗？ 

         赋值过程：输入数据→寄存器处理→开辟内存→写入数据。

         一次深复制，可以得到被复制对象指针，并进行一次赋值操作。

   //非容器类不可变对象

   //原始地址::::::::::0x1000033d0

   //retain复制::::::::0x1000033d0//浅复制

   //copy复制::::::::::0x1000033d0//浅复制

   //mutableCopy复制:::0x10010c420//深复制

   

      printf("\n");
复制代码
 


 ==============================第二种：容器类不可变对象=================

    
复制代码
   NSArray *array1= [NSArray arrayWithObjects:@"a",@"b",@"c",@"d",nil];

        

          printf("\n初始化赋值引用计数为::::::::::::%lu",array1.retainCount);

        NSArray *arrayCopy1 = [array1 retain];

          printf("\n继续retain后引用计数为:::::::::%lu",array1.retainCount);

        NSArray *arrayCopy2 = [array1 copy];

          printf("\n继续copy后引用计数为:::::::::::%lu",array1.retainCount);

        NSArray *arrayCopy3 = [array1 mutableCopy];

          printf("\n继续mutableCopy后引用计数为::::%lu\n",array1.retainCount);

        

    printf("\n容器类不可变数组\n原始地址::::::::::%p\t\t%p",array1,[array1 objectAtIndex:1]);

        printf("\nretain复制::::::::%p\t%p",arrayCopy1,[arrayCopy1 objectAtIndex:1]);

        printf("\ncopy复制::::::::::%p\t%p",arrayCopy2,[arrayCopy2 objectAtIndex:1]);

        printf("\nmutableCopy复制:::%p\t%p",arrayCopy3,[arrayCopy3 objectAtIndex:1]);

        

   

    //初始化赋值引用计数为::::::::::::1

    //继续retain后引用计数为:::::::::2

    //继续copy后引用计数为:::::::::::3

    //继续mutableCopy后引用计数为::::3

    //容器类不可变数组

    //原始地址::::::::::0x10010c6b0 0x100003410

    //retain复制::::::::0x10010c6b0 0x100003410//浅复制

    //copy复制::::::::::0x10010c6b0 0x100003410//浅复制

    //mutableCopy复制:::0x10010c760 0x100003410//深复制

    

        printf("\n");
复制代码
 
 

 
 ===============第三种：非容器类可变对象==================

      
复制代码
 NSMutableString *str2=[NSMutableString stringWithString:@"two day"];

        

          printf("\n初始化赋值引用计数为::::::::::::%lu",str2.retainCount);

        NSMutableString *strCpy1=[str2 retain];

          printf("\n继续retain后引用计数为:::::::::%lu",str2.retainCount);

        NSMutableString *strCpy2=[str2 copy];

          printf("\n继续copy后引用计数为:::::::::::%lu",str2.retainCount);

        NSMutableString *strCpy3=[str2 mutableCopy];

                printf("\n继续mutableCopy后引用计数为::::%lu\n",str2.retainCount);

        

        printf("\n非容器类可变对象\n原始地址::::::::::%p",str2);

        printf("\nretin复制::::::::%p",strCpy1);

        printf("\ncopy复制::::::::::%p",strCpy2);

        printf("\nmutableCopy复制:::%p",strCpy3);

       

 

         //初始化赋值引用计数为::::::::::::1

         //继续retain后引用计数为:::::::::2

         //继续copy后引用计数为:::::::::::2

         //继续mutableCopy后引用计数为::::2

         //非容器类可变对象

         //原始地址::::::::::0x10010c560

         //retain复制::::::::0x10010c560//浅复制

         //copy复制::::::::::0x100102720//深复制

       //mutableCopy复制:::0x10010c880//深复制

         

        printf("\n");
复制代码
 
 

 
 =========================第四种：容器类可变对象======================

 
复制代码
 NSMutableArray *array2   = [NSMutableArray arrayWithObjects:@"aa",@"bb",@"cc",@"dd",nil];

        

         printf("\n初始化赋值引用计数为::::::::::%lu",array2.retainCount);

       NSMutableArray *arrayCpy1 = [array2 retain];

         printf("\n继续retain后引用计数为:::::::%lu",array2.retainCount);

       NSMutableArray *arrayCpy2=[array2 copy];

         printf("\n继续copy后引用计数为:::::::::%lu",array2.retainCount);

       NSMutableArray *arrayCpy3 = [array2 mutableCopy];

         printf("\n继续mutableCopy后引用计数为::%lu\n",array2.retainCount);

        

       printf("\n容器类可变数组\n原始地址:::::::::::%p\t%p",array2,[array2 objectAtIndex:1]);

       printf("\nretain复制:::::::::%p\t%p",arrayCpy1,[arrayCpy1 objectAtIndex:1]);

       printf("\ncopy复制:::::::::::%p\t%p",arrayCpy2,[arrayCpy2 objectAtIndex:1]);

       printf("\nnmutableCopy复制:::%p\t%p",arrayCpy3,[arrayCpy3 objectAtIndex:1]);

       

        

         //初始化赋值引用计数为::::::::::1

         //继续retain后引用计数为:::::::2

         //继续copy后引用计数为:::::::::2

         //继续mutableCopy后引用计数为::2

         //容器类可变数组

         //原始地址:::::::::::0x10010e6c0 0x1000034b0

         //retain复制:::::::::0x10010e6c0 0x1000034b0//浅复制

         //copy复制:::::::::::0x10010e790 0x1000034b0//深复制

         //nmutableCopy复制:::0x10010e7c0 0x1000034b0//深复制

         

        

    }

    return 0;

}
