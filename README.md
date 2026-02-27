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
```