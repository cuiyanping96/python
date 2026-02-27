# python
```python
from datetime import datetime

class Person:
    max_age = 120
    planet = '火星'
    def __init__(self, name, age, gender=None):
        self.name = name
        self.age = age
        self.gender = gender  # 新增gender属性
    
    def speak(self,msg):
        print(f'我叫{self.name}，年龄：{self.age}，想说：{msg}')
    
    @staticmethod
    def is_adult(year):
        current_year = datetime.now().year
        age = current_year-int(year)
        return age >= 18
    def run(self,distance):
        print(f'{self.name}疯狂的奔跑了{distance}米')
    @classmethod
    def change_planet(cls,value):
        cls.planet = value
    @classmethod
    def create(cls,info_str):
        name,year,gender = info_str.split('-')
        current_year = datetime.now().year
        age = current_year - int(year)
        return cls(name,age,gender)
result = Person.is_adult(1994)
print(result)
p1 = Person('zs',20)
p2 = Person('ls',20)

def speak():
    print('hhhhhh')

p1.speak=speak
Person.change_planet('月球')
p3 = Person.create('梨花-1994-nv')

print(Person.__dict__)
print(p1.planet)
print(p2.planet)
print(p3.__dict__)
class Student(Person):
    def __init__(self,name,age,gender,stu_id,grade):
        super().__init__(name,age,gender)
        # Person.__init__(self,name,age,gender)
        self.stu_id = stu_id
        self.grade = grade
    def study(self):
        print(f'我是P{self.name}，我在努力学习，只取做到{self.grade}年级第一')
    def speak(self,msg):
        super().speak(msg)
        print(f'我是学生，我重写了Person的speak方法，我想说{msg}')

s1 = Student('lH',16,'NA','2026001','sec')
print(s1.__dict__)
print(type(s1))
s1.study()
s1.speak('我先执行super（）。speak，在执行student的speak')
```

| `__new__` | 真正的「构造方法」（创建实例对象） | 比 `__init__` 更早触发，返回实例对象 | ```python
class Person:
    # 控制实例创建（极少重写，除非单例模式）
    def __new__(cls, *args, **kwargs):
        instance = super().__new__(cls)  # 调用父类创建实例
        print("实例创建中...")
        return instance
    
    def __init__(self, name, age):
        self.name = name
        self.age = age

p = Person("张三", 20)  # 先触发__new__，再触发__init__
``` |
| `__del__` | 析构方法（销毁实例） | 实例被垃圾回收时触发（极少用） | ```python
class Person:
    def __del__(self):
        print(f"{self.name} 实例被销毁")

p = Person("张三", 20)
del p  # 手动删除实例，触发__del__
``` |

### 二、字符串表示（高频）
控制实例的「字符串输出格式」，让打印/查看实例时更友好。

| 魔法方法 | 作用 | 触发场景 | 示例 |
|----------|------|----------|------|
| `__str__` | 用户友好的字符串表示 | `print(实例)` / `str(实例)` 触发 | ```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    # 面向用户的输出
    def __str__(self):
        return f"Person(name={self.name}, age={self.age})"

p = Person("张三", 20)
print(p)  # 输出：Person(name=张三, age=20)
``` |
| `__repr__` | 开发者友好的字符串表示（可还原实例） | `repr(实例)` / 交互式解释器触发 | ```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    # 面向开发者的输出（推荐可直接复制创建实例）
    def __repr__(self):
        return f"Person('{self.name}', {self.age})"

p = Person("张三", 20)
print(repr(p))  # 输出：Person('张三', 20)
``` |

> 小贴士：如果只重写一个，优先重写 `__repr__`（`print` 会 fallback 到 `__repr__`）。

### 三、属性访问控制（高频）
控制实例属性的「读取、赋值、删除」，常用于属性校验、隐藏内部逻辑。

| 魔法方法 | 作用 | 触发场景 | 示例 |
|----------|------|----------|------|
| `__getattr__` | 访问**不存在的属性**时触发 | `实例.不存在的属性` | ```python
class Person:
    def __init__(self, name):
        self.name = name
    
    # 访问不存在的属性时触发（兜底）
    def __getattr__(self, attr):
        if attr == "age":
            return 18  # 兜底返回默认值
        raise AttributeError(f"无属性 {attr}")

p = Person("张三")
print(p.age)  # 输出：18（触发__getattr__）
# print(p.gender)  # 报错：AttributeError
``` |
| `__setattr__` | 给**任意属性赋值**时触发（包括初始化） | `实例.属性 = 值` | ```python
class Person:
    def __init__(self, name, age):
        self.name = name  # 触发__setattr__
        self.age = age    # 触发__setattr__
    
    # 赋值时校验（比如年龄不能为负）
    def __setattr__(self, attr, value):
        if attr == "age" and (not isinstance(value, int) or value < 0):
            raise ValueError("年龄必须是正整数")
        # 必须通过父类赋值，否则递归死循环
        super().__setattr__(attr, value)

p = Person("张三", 20)
# p.age = -5  # 报错：ValueError
``` |
| `__getattribute__` | 访问**任意属性**时触发（优先级最高） | `实例.任意属性` | ```python
class Person:
    def __init__(self, name):
        self.name = name
    
    # 所有属性访问都会触发（慎用，容易出问题）
    def __getattribute__(self, attr):
        print(f"正在访问属性：{attr}")
        return super().__getattribute__(attr)

p = Person("张三")
print(p.name)  # 先打印「正在访问属性：name」，再输出「张三」
``` |

### 四、比较运算（常用）
自定义实例之间的比较逻辑（如 `==`、`<`、`>`）。

| 魔法方法 | 作用 | 触发场景 | 示例 |
|----------|------|----------|------|
| `__eq__` | 等于（`==`） | `实例1 == 实例2` | ```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    # 自定义“相等”逻辑：姓名+年龄相同则相等
    def __eq__(self, other):
        if not isinstance(other, Person):
            return False
        return self.name == other.name and self.age == other.age

p1 = Person("张三", 20)
p2 = Person("张三", 20)
p3 = Person("李四", 20)
print(p1 == p2)  # True
print(p1 == p3)  # False
``` |
| `__lt__` | 小于（`<`） | `实例1 < 实例2` | ```python
class Person:
    def __init__(self, age):
        self.age = age
    
    # 按年龄比较大小
    def __lt__(self, other):
        return self.age < other.age

p1 = Person(18)
p2 = Person(20)
print(p1 < p2)  # True
``` |
| `__ne__` | 不等于（`!=`） | `实例1 != 实例2` | 若未重写，默认基于 `__eq__` 取反 |

### 五、容器/序列相关（自定义容器类用）
用于自定义「列表、字典」类的容器，实现索引、迭代等功能。

| 魔法方法 | 作用 | 触发场景 | 示例 |
|----------|------|----------|------|
| `__len__` | 获取长度 | `len(实例)` | ```python
class StudentList:
    def __init__(self):
        self.students = []
    
    def __len__(self):
        return len(self.students)
    
    def add(self, student):
        self.students.append(student)

sl = StudentList()
sl.add("张三")
sl.add("李四")
print(len(sl))  # 输出：2
``` |
| `__getitem__` | 索引访问 | `实例[索引]` | ```python
class StudentList:
    def __init__(self):
        self.students = []
    
    def __getitem__(self, index):
        return self.students[index]

sl = StudentList()
sl.students = ["张三", "李四"]
print(sl[0])  # 输出：张三
``` |
| `__iter__` | 迭代 | `for...in 实例` | 需返回迭代器（配合 `__next__`） |

### 六、其他常用魔法方法
| 魔法方法 | 作用 | 触发场景 |
|----------|------|----------|
| `__add__` | 加法（`+`） | `实例1 + 实例2`（自定义数值/拼接逻辑） |
| `__enter__`/`__exit__` | 上下文管理器 | `with 实例 as f:`（比如文件操作） |
| `__call__` | 让实例可调用 | `实例()`（把实例当函数用） |

### 总结
1. **核心高频魔法方法**（必掌握）：
   - 初始化：`__init__`
   - 字符串表示：`__str__`、`__repr__`
   - 属性控制：`__getattr__`、`__setattr__`
   - 比较运算：`__eq__`、`__lt__`
   - 容器相关：`__len__`、`__getitem__`

2. **核心原则**：
   - 魔法方法由 Python 自动触发，无需手动调用（比如不要写 `p.__init__()`）；
   - 重写魔法方法的目的是让自定义类更符合 Python 原生类型的行为（比如打印实例时更友好、支持比较运算）；
   - 除非有明确需求，否则不要重写 `__new__`、`__getattribute__`（容易引入隐蔽问题）。
