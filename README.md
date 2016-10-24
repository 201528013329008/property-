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

