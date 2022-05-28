# Data classes

data classes help to write more data oriented classes

```python
from dataclasses import dataclass, field


@dataclass
class Person:
    name: str
    address: str
    active

def main() -> None:
    person = Person(name="joe", address="123 Some St")
    print(person)


if __name__ == "__main__":
    main()
```
Output:
>Person(name='joe', address='123 Some St')  


To delete name of the person:
```python
del person.name
```

___Default value___ for primitive types like bool, integer, float, string:
```python
@dataclass
class Person:
    name: str
    address: str
    active: bool = True

def main() -> None:
    person = Person(name="joe", address="123 Some St")

```

___Default value___ for more complicated types like list:
```python
@dataclass
class Person:
    email_addresses: list[str] = field(default_factory=list)

```

We actually pass a function:
```python
def generate_random_id() -> str:
    return "some_random_id"


@dataclass
class Person:
    id: str = field(default_factory=generate_random_id)
```

For preventing an instance var like id to be initialized and ___remove from initializer___:
```python
@dataclass
class Person:
    id: str = field(init=False, default_factory=generate_random_id)
```

Suppose we want a var named search_text that is constructed after initialization of the Person class. So we remove it from initializer and use ___post_init___ like this:
```python
@dataclass
class Person:
    name: str
    address: str
    active: bool = True
    email_addresses: list[str] = field(default_factory=list)
    id: str = field(init=False, default_factory=generate_random_id)
    search_text: str = field(init=False)

    def __post_init__(self) -> None:
        self.search_text = f"{self.name} {self.address}"

```

to indicate that a var is protected and internal to the class we use underscore:
```python
@dataclass
class Person:
    name: str
    address: str
    _search_text: str = field(init=False)

    def __post_init__(self) -> None:
        self._search_text = f"{self.name} {self.address}"

```

To prevent a var to be printed we set repr to False:
```python
@dataclass
class Person:
    name: str
    address: str
    _search_text: str = field(init=False, repr=False)

```

if we freeze the Person, we can not change it after initialization:
```python
@dataclass(frozen=True)
class Person:
    name: str

```

### Python 3.10

Code below means you have to use keywords when initializing:
```python
@dataclass(kw_only=True)
class Person:
    name: str
    address: str

person = Person(name="joe", address="123 Some St")
```

And if you set to False:
```python
@dataclass(kw_only=False)
class Person:
    name: str
    address: str

person = Person("joe", "123 Some St")
```

A python class is actually a really advanced dictionary. When you create an instance of a class there is going to be a dunder dictionary object that contains the reference to all the instance variables. It is a dictionary from stringto the value of the instance var. so we could print name like:
```python
print(person.__dict__["name"])
```

With code below class uses slot instead of dictionary to access the instance vars which is much faster:
```python
@dataclass(slots=True)

```

⚠️ The issue is that Slots break when you try to use multiple inheritance. We get error if we run the code below because PersonEmployee inherits from Person and Employee which both use Slots and PersonEmployee does not know which list of Slots it should rely on for inheritance relationship:
```python
@dataclass(slots=True)
class Employee:
    address: str


@dataclass(slots=True)
class Person:
    name: str


class PersonEmployee(Person, Employee):
    pass
```


[Reference](https://www.youtube.com/watch?v=CvQ7e6yUtnw)
