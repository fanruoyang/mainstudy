##### 新函数
 
**nullable: 1.提高代码质量，配合swift--可能为空**

- 语法  可以为空，在初始化参数的时候，可以添加属性
- swift是强语法

         @property (nonatomic, strong,nullable) NSString* name;
         @property (nonatomic, strong) NSString* _Nullable name1;

**nonull :不能为空，但是OC弱语法，无法报错   **
**null_resettable  没有处理为空的情况 UIView---正确使用懒加载的方式**
-  get方法不能返回nil，set可以传入为空
-  必须要处理为空的情况，重写set方法

**null_unspecified:不确定是否为空**
