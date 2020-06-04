，例如[operator>>,operator<<](#>>and<<)，左侧为iostream对象
- 两者皆可：建议设为方法，方法可是virtual，而友元函数不能

参考重载运算符表：Pg.355

#### 关系运算符 ???

utility头文件中，命名空间std::rel_ops包含operator!=,>,<=,>=的模板。在类中实现operator==,<，就会自动获得这些模板运算符

++i（前缀递增），变量+1，然后返回新值
i++（后缀递增），变量+1，但返回旧值

Cls& operator++();  // 前缀递增
Cls operator++(parType);  // 后缀递增,只有参数类型，无实际参数，以与前缀递增operator++区分

	// 可返回引用
	Sheet& operator++(){
		set(mValue + 1);
		return *this;
	}
	// 需创建1个临时对象保存原有数据，并返回此临时对象（是本地变量，不能被引用）
	Sheet oerator++(){
		Sheet temp(*this);
		set(mValue + 1);
		return temp;
	}

#### >><<

>>,<<左侧为stream类，但不能向stream类添加方法，因此只能在右侧类的<<和>>函数改为全局友元函数（friend）

class RhsClass{
