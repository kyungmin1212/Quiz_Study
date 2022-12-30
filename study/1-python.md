## Table of Contents

- [클래스(class), 객체(object), 인스턴스(instance), 어트리뷰트(attribute) 용어 설명](#1)
- [`__str__`과 `__repr__` 의 차이점](#2)
- [`__dict__` 와 `dir()` 의 차이점](#3)
- [`__doc__`](#4)
- [클래스 변수와 인스턴스 변수](#5)
- [`__init__`과 `__del__`](#6)
- [@classmethod 와 @staticmethod](#7)
- [다양한 special method (= magic method)](#8)
- [namedtuple](#9)
- [이터레이터(Iterator)와 제너레이터(Generator)](#10)
- [위치 인자와 키워드 인자의 패킹(packing)과 언패킹(unpacking)](#11)
- [filter, map](#12)
- [reduce, partial](#13)
- [global, local, nonlocal](#14)
- [클로저(closure)](#15)

---

## #1

### 클래스(class), 객체(object), 인스턴스(instance), 어트리뷰트(attribute) 용어 설명

- 클래스(Class)와 객체(Object)
    - 클래스와 객체는 붕어빵틀과 붕어빵의 관계
    - 붕어빵틀이 주어지면 그 안에 팥, 고구마, 슈크림, 김치, 초코 붕어빵등 형태에 맞는 붕어빵을 계속 찍어냄

- 객체(Object)와 인스턴스(instance)
    - 인스턴스는 객체를 실체화 시킨것
    - 붕어빵 재료들을 설정하고 그거를 이제 실제로 만들어내면 인스턴스가 됨(실제로 만들기 전 값을 세팅해놓은것이 객체)
    - 차를 통한 이해 : 클래스(그랜저 설계도), 객체(어떤 옵션을 추가한 실제 그랜저), 인스턴스(실제 그랜저 중 단 하나,(고유한 id))
    - 같다고 봐도 무방함

- 어트리뷰트(attribute)
    - 클래스 내부에 포함되어 있는 함수(메서드)나 변수 등을 의미

#### References
- https://www.codeit.kr/community/threads/8735?utm_source=google_paid&utm_medium=pmax&utm_campaign=da_purchase&utm_content=general&gclid=CjwKCAiA76-dBhByEiwAA0_s9aeUx4hqCpREAjeBDEwVDKOo0uFK0rQQJHO0-wNg_C5J2xK5zWV3vhoCMDcQAvD_BwE

---

## #2

### `__str__`과 `__repr__` 의 차이점

- `__str__` 는 사용자 레벨에서 디버깅, `__repr__` 은 엔지니어 레벨에서 디버깅
- print(인스턴스명)시 우선순위 
    - 1순위 : `__str__` , 2순위 : `__repr__`
    - print(repr(인스턴스명)) 하면 `__str__`이 존재하더라도 `__repr__` 출력
- 코드
    ```python
    class Student():
        def __init__(self, name, details):
            self._name = name
            self._details = details

        def __str__(self):
            return 'str : {} - {}'.format(self._name, self._details)

        def __repr__(self):
            return 'repr : {} - {}'.format(self._name, self._details)
        

    student1 = Student('철수', {'school':'inha','grade':1,'number':'010-1111-1111'})
    print(student1)
    print(repr(student1))
    '''
    str : 철수 - {'school': 'inha', 'grade': 1, 'number': '010-1111-1111'}
    repr : 철수 - {'school': 'inha', 'grade': 1, 'number': '010-1111-1111'}
    '''

    ```
- 인스턴스 한개만을 print할때만 `__str__`이 최우선순위, 자료구조형태에 들어갔을경우는 `__repr__` 형태로 출력
    ```python
    class Student():
        def __init__(self, name, details):
            self._name = name
            self._details = details

        def __str__(self):
            return 'str : {} - {}'.format(self._name, self._details)

        def __repr__(self):
            return 'repr : {} - {}'.format(self._name, self._details)
        
    student_list = []

    student1 = Student('철수', {'school':'inha','grade':1,'number':'010-1111-1111'})
    student2 = Student('짱구', {'school':'inha','grade':2,'number':'010-2222-2222'})
    student3 = Student('훈이', {'school':'inha','grade':1,'number':'010-3333-3333'})

    student_list.append(student1)
    student_list.append(student2)
    student_list.append(student3)

    print(student_list)

    for x in student_list:
        print(x)
    
    '''
    [repr : 철수 - {'school': 'inha', 'grade': 1, 'number': '010-1111-1111'}, repr : 짱구 - {'school': 'inha', 'grade': 2, 'number': '010-2222-2222'}, repr : 훈이 - {'school': 'inha', 'grade': 1, 'number': '010-3333-3333'}]
    str : 철수 - {'school': 'inha', 'grade': 1, 'number': '010-1111-1111'}
    str : 짱구 - {'school': 'inha', 'grade': 2, 'number': '010-2222-2222'}
    str : 훈이 - {'school': 'inha', 'grade': 1, 'number': '010-3333-3333'}
    '''
    ```

#### References
- https://wikidocs.net/134994
- [우리를 위한 프로그래밍 : 파이썬 중급](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%A4%91%EA%B8%89-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90)

---

## #3

### `__dict__` 와 `dir()` 의 차이점

- `인스턴스.__dict__`
    - 인스턴스 안에 들어있는 속 정보까지 모두 파악 가능(단, 클래스 변수는 출력되지 않음)
        ```python
        class Student():
            student_count = 0
            
            def __init__(self, name, details):
                self._name = name
                self._details = details
                Student.student_count+=1

            def __str__(self):
                return 'str : {} - {}'.format(self._name, self._details)

            def __repr__(self):
                return 'repr : {} - {}'.format(self._name, self._details)
            
            def __del__(self):
                Student.student_count-=1

        student1 = Student('철수', {'school':'inha','grade':1,'number':'010-1111-1111'})
        student2 = Student('짱구', {'school':'inha','grade':2,'number':'010-2222-2222'})

        print(student1.__dict__)
        '''
        {'_name': '철수', '_details': {'school': 'inha', 'grade': 1, 'number': '010-1111-1111'}}
        '''
        ```
- `dir(인스턴스)`
    - 클래스 안에서 사용되고 있는 메서드 이름이랑 변수명 모두 출력(클래스 변수명까지 출력됨)
    - 단, 안에 들어 있는 내용까지는 모름
    - 코드
        ```python
        class Student():
            student_count = 0
            
            def __init__(self, name, details):
                self._name = name
                self._details = details
                Student.student_count+=1

            def __str__(self):
                return 'str : {} - {}'.format(self._name, self._details)

            def __repr__(self):
                return 'repr : {} - {}'.format(self._name, self._details)
            
            def __del__(self):
                Student.student_count-=1

        student1 = Student('철수', {'school':'inha','grade':1,'number':'010-1111-1111'})
        student2 = Student('짱구', {'school':'inha','grade':2,'number':'010-2222-2222'})

        print(dir(student1))
        '''
        ['__class__', '__del__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__le__', '__lt__', '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__', '_details', '_name', 'student_count']
        '''
        ```

#### References
- [우리를 위한 프로그래밍 : 파이썬 중급](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%A4%91%EA%B8%89-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90)


---

## #4 

### `__doc__` 

- 함수나 클래스를 작성할때는 항상 doc 달아주기
- """ 사이에 내용 작성
    - 간단한 설명 + params 설명 + return 설명
    - `.__doc__`을 통해 함수나 클래스에 대핸 설명 참조가능
        ```python
        def get_news(keywords, client_id, client_secret):
            """
            네이버 검색 뉴스 API 사용해 특정 키워드들의 뉴스 검색
            :params list keywords: 키워드 리스트
            :params str client_id: 인증정보
            :params str client_secret: 인증정보
            :return news_items : API 검색 결과 중 뉴스 item들
            :rtype list
            """
        print(get_news.__doc__)
        '''
        네이버 검색 뉴스 API 사용해 특정 키워드들의 뉴스 검색
        :params list keywords: 키워드 리스트
        :params str client_id: 인증정보
        :params str client_secret: 인증정보
        :return news_items : API 검색 결과 중 뉴스 item들
        :rtype list
        '''
        ```

---

## #5

### 클래스 변수와 인스턴스 변수

- 클래스 변수는 모든 인스턴스가 공유함
    - 클래스 변수는 생성자 위에 작성
- 인스턴스 변수는 자신만의 것
- 아래 코드 처럼 학생 클래스에서 인스턴스가 생성되거나 사라질때마다 기록할수 있는 값이나 모든 인스턴스가 동일하게 사용하는 값들을 보통 클래스 변수로 사용
- 코드
    ```python
    class Student():
        student_count = 0
        
        def __init__(self, name, details):
            self._name = name
            self._details = details
            Student.student_count+=1

        def __str__(self):
            return 'str : {} - {}'.format(self._name, self._details)

        def __repr__(self):
            return 'repr : {} - {}'.format(self._name, self._details)
        
        def __del__(self):
            Student.student_count-=1

    student1 = Student('철수', {'school':'inha','grade':1,'number':'010-1111-1111'})
    student2 = Student('짱구', {'school':'inha','grade':2,'number':'010-2222-2222'})

    print(student1.student_count)
    print(student2.student_count)
    print(Student.student_count)
    '''
    2
    2
    2
    '''
    ```
    (인스턴스.클래스변수, 클래스.클래스변수 둘다 동일하게 나옴)

#### References
- [우리를 위한 프로그래밍 : 파이썬 중급](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%A4%91%EA%B8%89-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90)

---

## #6

### `__init__`과 `__del__`

- `__init__` 은 클래스를 통해 인스턴스가 생성될때마다 실행되는 메서드
    - `__init__` 에 있는 입력 인자를 인스턴스 생성시에 집어넣어줘야함
- `__del__` 은 `del 인스턴스명` 을 통해 인스턴스가 삭제될때마다 실행되는 메서드
- 코드
    ```python
    class Student():
        student_count = 0
        
        def __init__(self, name, details):
            self._name = name
            self._details = details
            Student.student_count+=1

        def __str__(self):
            return 'str : {} - {}'.format(self._name, self._details)

        def __repr__(self):
            return 'repr : {} - {}'.format(self._name, self._details)
        
        def __del__(self):
            Student.student_count-=1

    student1 = Student('철수', {'school':'inha','grade':1,'number':'010-1111-1111'})
    student2 = Student('짱구', {'school':'inha','grade':2,'number':'010-2222-2222'})

    print(Student.student_count)
    del student1
    print(Student.student_count)
    '''
    2
    1
    '''
    ```

---

## #7

### @classmethod 와 @staticmethod

- 일반적으로 클래스 내부에서 변수명에 접근할때는 클래스 메서드 또는 인스턴스 메서드를 사용하여 접근
    - 클래스 변수에 접근할때는 클래스 메서드 사용(클래스 메서드 위에 @classmehtod 데코레이터 사용)
    - 인스턴스 변수에 접근할때는 인스턴스 메서드 사용(아무런 표시 없음)
- @classmethod
    - self대신 cls로 작성
    - 코드
        ```python
        class Student():
            student_count = 0
            tuition = 1000000
            
            def __init__(self, name, details):
                self._name = name
                self._details = details
                Student.student_count+=1

            def __str__(self):
                return 'str : {} - {}'.format(self._name, self._details)

            def __repr__(self):
                return 'repr : {} - {}'.format(self._name, self._details)
            
            def __del__(self):
                Student.student_count-=1
            
            @classmethod
            def change_tuition(cls,price):
                cls.tuition = price

        student1 = Student('철수', {'school':'inha','grade':1,'number':'010-1111-1111'})
        student2 = Student('짱구', {'school':'inha','grade':2,'number':'010-2222-2222'})

        print(Student.tuition)
        Student.change_tuition(2000000)
        print(Student.tuition)
        '''
        1000000
        2000000
        '''

        ```
- @staticmethod
    - 클래스 메서드 인스턴스 메서드 둘다 아닌 일반적인 함수 이지만 클래스안에 존재할경우 @staticmethod 표시 (클래스와 관련이 있기 때문에 일반적인 함수이지만 클래스 안에 작성)
    - @classmethod 와 상당히 유사하지만 상속 부분에서 명백한 차이 존재
        - 상속을 받을 경우 classmethod에서 작성되었던 cls 부분이 상속받은 클래스 명을 의미함. 즉, 상속을 받았을때 상속 받은 대상에 따라 변화무쌍한 값을 얻고 싶다면 @classmethod를 사용하고 상속을 받든 말든 고정된 값을 가지고 싶다면 @staticmethod를 사용해서 cls 대신 그때 클래스 명을 직접 작성
        - 코드
            ```python
            class Language:
                default_language = "English"

                def __init__(self):
                    self.show = '나의 언어는' +' '+ self.default_language

                @classmethod
                def class_my_language(cls):
                    return cls()

                @staticmethod
                def static_my_language():
                    return Language()

                def print_language(self):
                    print(self.show)


            class KoreanLanguage(Language):
                default_language = "한국어"
                
            a = KoreanLanguage.static_my_language()
            b = KoreanLanguage.class_my_language()
            a.print_language()
            b.print_language()
            '''
            >>
            나의 언어는 English
            나의 언어는 한국어
            '''
            ```

#### References
- https://wikidocs.net/16074
- [우리를 위한 프로그래밍 : 파이썬 중급](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%A4%91%EA%B8%89-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90)

---

## #8

### 다양한 special method (= magic method)

- `__(메서드 이름)__` 이런 형태를 special method라고 함
- 파이썬이 내부적으로 구현된(빌트인) 메서드를 구현함
- 인스턴스끼리 연산을 하기 위해서는 직접 변수명을 언급하고 따로 함수를 만들어야하는 번거로움 존재.
    - special method를 이용한다면 인스턴스끼리 내가 정의한 방식때로 연산이 가능해짐
- 코드
    ```python
    class Vector(object):
        def __init__(self, *args):
            '''Create a vector, example : v = Vector(5,10)'''
            if len(args) == 0:
                self._x, self._y = 0, 0
            else:
                self._x, self._y = args

        def __repr__(self):
            '''Returns the vector infomations'''
            return 'Vector(%r, %r)' % (self._x, self._y)

        def __add__(self, other):
            '''Returns the vector addition of self and other'''
            return Vector(self._x + other._x, self._y + other._y)
        
        def __mul__(self, y):
            return Vector(self._x * y, self._y * y)

        def __bool__(self):
            return bool(max(self._x, self._y))


    # Vector 인스턴스 생성
    v1 = Vector(5,7)
    v2 = Vector(23, 35)
    v3 = Vector()

    # 매직메소드 미사용 (매번 연산할때마다 번거로움)
    print('매직메소드 미사용 : ', Vector(v1._x + v2._x, v1._y + v2._y)) 


    # 매직메소드 출력
    print(Vector.__init__.__doc__)
    print(Vector.__repr__.__doc__)
    print(Vector.__add__.__doc__)
    print(v1, v2, v3)
    print(v1 + v2)
    print(v1 * 3)
    print(v2 * 10)
    print(bool(v1), bool(v2))
    print(bool(v3))
    '''
    매직메소드 미사용 :  Vector(28, 42)
    Create a vector, example : v = Vector(5,10)
    Returns the vector infomations
    Returns the vector addition of self and other
    Vector(5, 7) Vector(23, 35) Vector(0, 0)
    Vector(28, 42)
    Vector(15, 21)
    Vector(230, 350)
    True True
    False
    '''
    ```

- 다양한 예시
    ```python
    __lt__(self, other) # self<other
    __le__(self, other) # self<=other
    __eq__(self, other) # self==other
    __ne__(self, other) # self!=other
    __gt__(self, other) # self>other
    __ge__(self, other) # self>=other
    __add__(self, other) # self+other
    __sub__(self, other) # self-other
    __mul__(self, other) # self*other
    __matmul__(self, other) # self@other
    __truediv__(self, other) # self/other
    __floordiv__(self, other) # self//other
    __mod__(self, other) # self%other
    __divmod__(self, other) # divmod(self,other)
    __pow__(self, other)# self**other
    __lshift__(self, other) # self<<other
    __rshift__(self, other) # self>>other
    __and__(self, other) # self&other
    __xor__(self, other) # self^other
    __or__(self, other) # self|other
    ```

####
- https://docs.python.org/3/reference/datamodel.html
- https://zzsza.github.io/development/2020/07/05/python-magic-method/
- [우리를 위한 프로그래밍 : 파이썬 중급](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%A4%91%EA%B8%89-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90)

---

## #9

### namedtuple

- tuple을 딕셔너리처럼 키를 지정해서 사용가능
- 다양한 선언 방법이 존재
- 코드
    ```python
    # 네임드 튜플 사용
    from collections import namedtuple

    # 네임드 튜플 선언 방법
    Point1 = namedtuple('Point', ['x', 'y'])
    Point2 = namedtuple('Point', 'x, y')
    Point3 = namedtuple('Point', 'x y')
    Point4 = namedtuple('Point', 'x y x class', rename=True) # Default=False 변수명이 같은 경우 rename=True로 지정하게되면 오류없이 변수이름이 수정됨


    # 출력
    print(Point1, Point2, Point3, Point4)

    # Dict to Unpacking
    temp_dict = {'x': 75, 'y': 55}
    temp = [52, 38]

    p1 = Point1(x=10, y=35)
    p2 = Point2(20, 40)
    p3 = Point3(45, y=20)
    p4 = Point4(10, 20, 30, 40)
    p5 = Point3(**temp_dict)
    p6 = Point3(*temp)

    print(p1, p2, p3, p4, p5, p6)

    '''
    <class '__main__.Point'> <class '__main__.Point'> <class '__main__.Point'> <class '__main__.Point'>
    Point(x=10, y=35) Point(x=20, y=40) Point(x=45, y=20) Point(x=10, y=20, _2=30, _3=40) Point(x=75, y=55) Point(x=52, y=38)
    '''
    ```

####
- [우리를 위한 프로그래밍 : 파이썬 중급](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%A4%91%EA%B8%89-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90)

---

## #10

### 이터레이터(Iterator)와 제너레이터(Generator)
- 이터레이터(Iterator)
    - Iterator란 값을 차례대로 꺼낼 수 있는 객체
    - iterator는 iterable한 객체를 내장함수 또는 iterable객체의 메소드를 통해 생성 가능
        - 파이썬 내장함수 iter()를 사용해 iterator 객체 생성 가능
            ```python
            a = [1,2,3]
            print(type(a))
            a_iter = iter(a)
            print(type(a_iter))
            '''
            <class 'list'>
            <class 'list_iterator'>
            '''
            ```
        - iterable 객체는 매직메서드 (`__iter__`)메서드를 가지고 있음. 이 메서드로 Iterator 만들기
            ```python
            a = [1,2,3]
            print(dir(a))
            print(hasattr(a, '__iter__'))

            a_iter = a.__iter__()
            print(type(a_iter))

            print(next(a_iter))
            print(next(a_iter))
            print(next(a_iter))
            print(next(a_iter))
            '''
            1
            2
            3
            StopIteration
            '''
            ``` 
- 제너레이터(Generator)
    - generator란 iterator 를 생성해주는 함수, 함수안에 yield 키워드 사용
    - 무한한 순서가 있는 객체를 모델링 가능
    - yield 예시
        ```python
        def test_generator():
            print('yield 1 전')
            yield 1
            print('yield 1과 2사이')
            yield 2
            print('yield 2와 3사이')
            yield 3
            print('yield 3 후')

        g = test_generator()
        next(g)
        next(g)
        next(g)
        next(g)
        '''
        yield 1 전
        yield 1과 2사이
        yield 2와 3사이
        yield 3 후

        StopIteration 발생
        '''

        ```
        (다음 4번째 next(g)를 실행할때 다음 yield가 있는지 코드를 더 내려갔는데('yield 3 후' 까지 출력) yield가 더이상 없고 함수가 끝났으므로 오류발생)
    - 무한한 순서 있는 객체 예시
        ```python
        def infinite_generator():
            count = 0
            while True:
                count+=1
                yield print(count)

        g = infinite_generator()
        next(g)
        next(g)
        next(g)
        next(g)
        next(g)
        next(g)
        next(g)
        '''
        1
        2
        3
        4
        5
        6
        7
        '''
        ```
    - iterable한 객체를 yield할 때는 yield from iterable객체 사용
        ```python
        def three_generator():
            a = [1, 2, 3]
            yield from a

        gen = three_generator()
        print(next(gen))
        print(next(gen))
        print(list(gen))
        print(next(gen))
        '''
        1
        2
        [3]

        StopIteration 발생
        '''
        ```
        (list로 묶어서 한번에 나오면 모든 yield가 다 나오게됨. 따라서 list로 모두 꺼낸후 next를 실행하게 되면 오류발생)
    - 리스트 컴프리헨션 방식을 ( )로 묶어주면 generator가 됨
        ```python
        a = (i for i in [10,20,30] if i>=20)
        print(a)
        print(next(a))
        print(next(a))
        print(next(a))
        '''
        <generator object <genexpr> at 0x0000017E62DA0350>
        20
        30

        StopIteration 발생
        '''
        ```
    - 클래스를 생성하고 `__iter__` 메서드를 작성하여 제너레이터 생성하기
        ```python
        class WordSplitGenerator:
            def __init__(self, text):
                self._text = text.split(' ')
            
            def __iter__(self):
                # print('Called __iter__')
                for word in self._text:
                yield word # 제네레이터
                return
            
            def __repr__(self):
                return f'WordSplit({self._text})'


        wg = WordSplitGenerator('Do today what you could do tomorrow')
        # 여기서 바로 next(wg) 하면 오류 발생

        wt = iter(wg) # iter(객체)를 해주게 되면 generator가 됨

        print(wg)
        print(wt)
        print(next(wt))
        print(next(wt))
        print(next(wt))
        print(next(wt))
        print(next(wt))
        print(next(wt))
        print(next(wt))
        '''
        WordSplit(['Do', 'today', 'what', 'you', 'could', 'do', 'tomorrow'])
        <generator object WordSplitGenerator.__iter__ at 0x0000010CB4060350>    
        Do
        today
        what
        you
        could
        do
        tomorrow
        '''
        ```
    - 참고 : generator를 생성안하고 `__next__` 메서드를 이용하여 바로 next 적용할수 있게 만들기 (위의 방법이 확실한 제너레이터 생성 방법, 이건 단지 참고용으로 작성)
        ```python
        class WordSplitIter:
            def __init__(self, text):
                self._idx = 0
                self._text = text.split(' ')
            
            def __next__(self):
                # print('Called __next__')
                try:
                    word = self._text[self._idx]
                except IndexError:
                    raise StopIteration('Stopped Iteration.')
                self._idx += 1
                return word

            def __repr__(self):
                return f'WordSplit({self._text})'


        wi = WordSplitIter('Do today what you could do tomorrow')

        print(wi)
        print(next(wi))
        print(next(wi))
        print(next(wi))
        print(next(wi))
        print(next(wi))
        print(next(wi))
        print(next(wi))       
        '''
        WordSplit(['Do', 'today', 'what', 'you', 'could', 'do', 'tomorrow'])
        Do
        today
        what
        you
        could
        do
        tomorrow
        ''' 
        ```
        
#### References
- https://wikidocs.net/16068
- https://wikidocs.net/16069
- [우리를 위한 프로그래밍 : 파이썬 중급](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%A4%91%EA%B8%89-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90)
---

## #11

### 위치 인자와 키워드 인자의 패킹(packing)과 언패킹(unpacking)
- 간단한 예시
    ```python
    a_list = [1,2,3,4,5]

    a, *b, c = a_list
    print(a,*b,c)
    print(a,b,c)
    '''
    1 2 3 4 5
    1 [2, 3, 4] 5
    '''
    ```
    - b변수 앞에 *를 붙여주게 되면 b가 리스트로 인식됨.)
        - (참고 : **를 두개 붙여주면 딕셔너리로 인식됨, 이 때는 키값이 필요)
    - 2,3,4 원소를 b로 packing된것)
    - 리스트 b 앞에 *를 붙여서 줄력하게 되면 리스트 b가 unpacking되어서 출력됨)    
- packing은 인자로 받은 여러개의 값을 하나의 객체로 합쳐서 받을 수 있도록 함
    - 함수에서 여러값을 길이 제한없이 받고 싶을 경우 사용
    - 위치 인자 패킹 (* 사용)
        ```python
        def sum_all(*numbers):
            result = 0
            for number in numbers:
                result += number
            return result

        print(sum_all(1, 2, 3)) # 6
        print(sum_all(1, 2, 3, 4, 5, 6)) # 21
        '''
        6
        21
        '''
        ```
        (숫자를 나열해서 전달하게 되면 numbers라는 list로 묶여서 함수안에서 실행됨)    
    - 키워드 인자 패킹 (** 사용)
        ```python
        def print_family_name(father, mother, **sibling):
            print("아버지 :", father)
            print("어머니 :", mother)
            if sibling:
                print("호적 메이트..")
                for title, name in sibling.items():
                    print('{} : {}'.format(title, name))

        print_family_name("홍길동", '심사임당', 누나='김태희', 여동생='윤아')
        '''
        아버지 : 홍길동
        어머니 : 심사임당
        호적 메이트..
        누나 : 김태희
        여동생 : 윤아
        '''
        ```
        (키워드를 지정해서 넘겨주게 되면 sibling이라는 dictionary로 묶어서 함수안에서 실행됨)
    - 위치 인자와 키워드 인자 패킹 동시 사용
        ```python
        def print_family_name(*args, **kwargs):
            if args:
                for name in args:
                    print(name)
            else:
                print('가족 이름 데이터가 존재하지 않습니다.')
            
            if kwargs:
                for k,v in kwargs.items():
                    print(f'{k}의 이름은 {v}')

        print_family_name('홍길동','신사임당',여자친구='아이유',친구='김태희')
        print()
        print_family_name(여자친구='아이유',친구='김태희')
        '''
        홍길동
        신사임당
        여자친구의 이름은 아이유
        친구의 이름은 김태희

        가족 이름 데이터가 존재하지 않습니다.
        여자친구의 이름은 아이유
        친구의 이름은 김태희
        '''
        ```
        (키워드 인자와 위치 인자를 바꿔서 작성하면 오류 발생)
        ```python
        print_family_name(여자친구='아이유',친구='김태희','홍길동','신사임당')
        '''
        SyntaxError: positional argument follows keyword argument
        '''
        ```
- unpacking은 여러개의 객체를 포함하고 있는 하나의 객체를 풀어줌
    - 리스트를 unpacking 하는 경우는 *, 딕셔너리 unpacking하는 경우 **를 사용

    - 리스트 unpacking
        ```python
        a_list = [1,2,3,4]

        a,b,c,d = *a_list
        print(a,b,c,d)
        '''
        SyntaxError: can't use starred expression here
        '''
        ```
        - 이런식의 언패킹은 *가 없더라도 동작하는 코드이기때문에 사용안함(오류도 발생함)
        - 함수에 매개변수가 여러개일경우 리스트를 함수의 입력인자로 주고싶을때 함수(*리스트) 로 사용
            ```python
            a_list = [1,2,3,4]

            def sum(a,b,c,d):
                return a+b+c+d

            print(sum(*a_list))
            '''
            10
            '''
            ```
    - 딕셔너리 unpacking
        - 리스트와 마찬가지로 딕셔너리로 함수의 입력인자로 넣어줄때 사용
            ```python
            a_dict={'a':1,'b':2,'c':3}

            def sum(a,b,c):
                return a+b+c

            print(sum(**a_dict))
            '''
            6
            '''
            ```
            (a_dict의 키값이 함수의 매개변수와 동일해야지 함수에 입력인자로 사용 가능)



#### References
- https://wikidocs.net/22801

---

## #12

### filter, map

- map과 filter 모두 첫 번째 인자에 함수, 두 번째 인자에 리스트나 딕셔너리와 같은 iterable 한 데이터를 인자로 받음(리스트의 경우 key값이 함수의 인자로 들어감)
- mapr과 filter 모두 iterable한 데이터들을 함수에 넣어 generator를 반환
- map은 함수에 값을 집어넣어 함수에서 return 값들을 반환
- filter는 함수에 값을 집어넣어 조건식에 맞는 값들만 return
- map과 filter 비교 코드
    ```python
    a=[1,2,3,4,5]

    def map_func(num):
        return num*100

    def filter_func(num):
        return num*100>=300

    print(list(map(map_func,a)))
    print(list(filter(filter_func,a)))
    '''
    [100, 200, 300, 400, 500]
    [3, 4, 5]
    '''
    ```
- map과 filter의 사용예시
    ```python
    a=[1,2,3,4,5]

    def map_func(num):
        return num*100

    def filter_func(num):
        return num>=300

    print(list(filter(filter_func,map(map_func,a))))
    '''
    [300, 400, 500]
    '''
    ```
#### References
- [우리를 위한 프로그래밍 : 파이썬 중급](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%A4%91%EA%B8%89-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90)

---

## #13

### reduce, partial

- reduce와 partial 모두 functools 안에 존재
- reduce는 누적 연산
    - 예시
        ```python
        from functools import reduce

        def sum_func(a,b):
            return a+b

        print(reduce(sum_func, range(1,11))) # 누적 
        # 1+2+~10 -> 3+3+4+5+~+10 -> 6+4+5+~+10 -> 45+10 -> 55
        # (((((((((1+2)+3)+4)+5)+6)+7)+8)+9)+10)
        '''
        55
        '''
        ```
- parital은 인수 고정
    - 예시
        ```python
        from functools import partial

        def calculate(a,b,c,d):
            return a*(b+1)*(c+2)*(d+3)

        two = partial(calculate,2) # 제일 앞에 인자에 고정됨

        print(two(9,8,7))

        two_three = partial(two,9) # two 함수의 매개변수는 (b,c,d) 로줄어듬, 그 중 제일 앞에 b를 9로 고정
        print(two_three(8,7))
        '''
        2000
        2000
        '''
        ```
    - 기존 함수를 이용하여 새로운 함수를 파생시킬경우 유용
        ```python
        from functools import partial

        def add_mul(choice, *args):
            if choice == "add":
                result = 0
                for i in args:
                    result = result + i
            elif choice == "mul":
                result = 1
                for i in args:
                    result = result * i
            return result

        add = partial(add_mul, 'add')
        mul = partial(add_mul, 'mul')

        print(add(1,2,3,4,5))  # 15 출력
        print(mul(1,2,3,4,5))  # 120 출력
        '''
        15
        120
        '''
        ```

#### References
- [우리를 위한 프로그래밍 : 파이썬 중급](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%A4%91%EA%B8%89-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90)
- https://wikidocs.net/109304

---

## #14

### global, local, nonlocal
- global은 전역변수, local은 지역변수 의미
- 전역변수와 지역변수 살펴보기
    - 예시 1 (함수안에서 전역 변수값은 변경하지 않고 출력이나 이용하는 경우)
        ```python
        a = 10

        def a_func():
            print(a)
            
        a_func()
        '''
        10
        '''
        ```
        ```python
        a = 10

        def a_func():
            b = a+1
            print(b)
            
        a_func()
        '''
        11
        '''
        ```
    - 예시 2 (함수안에서 전역 변수랑 동일한 변수명을 선언하는 경우)
        ```python
        a = 10

        def a_func():
            a = 20
            print(f'a_func안에서의 a값 : {a}')
            
        a_func()
        print(f'a_func밖에서의 a값 : {a}')
        '''
        a_func안에서의 a값 : 20
        a_func밖에서의 a값 : 10
        '''
        ```
    - 예시 3 (함수안에서 전역 변수값을 이용하여 동일한 변수명을 선언하는 경우)
        ```python
        a = 10

        def a_func():
            a = a+10
            print(f'a_func안에서의 a값 : {a}')
            
        a_func()
        print(f'a_func밖에서의 a값 : {a}')
        '''
        (a = a+10 에서 오류발생)
        UnboundLocalError: local variable 'a' referenced before assignment
        '''
        ```
    - 정리
        - 예시 1의 경우를 살펴보면 함수 내에서 전역 변수값을 출력하거나 이용하는데에는 아무런 문제가 없음.
        - 예시 2의 경우를 살펴보면 함수 내에서 전역 변수명과 동일한 지역 변수명을 선언해도 아무런 상관없음. (단, 함수가 끝나면 지역변수로 사용되었던 변수는 사라짐)
        - 예시 3의 경우를 살펴보면 함수 내에서 전역 변수값을 이용하여 전역변수명과 동일한 지역변수명을 사용하였는데 이런 경우는 실행 불가 (즉, 예시 1과 예시 2는 가능하지만 예시 1과 예시 2를 섞은 예시 3은 실행 불가)
- global을 통해 함수내에서 전역변수 값 변경해주기
    - 전역변수 사용 예시에서 전역 변수명과 동일한 지역 변수명을 선언했던 함수의 예시(예시 2와 예시 3)에서 global 선언해보기
        ```python
        a = 10

        def a_func():
            global a
            a = 20
            print(f'a_func안에서의 a값 : {a}')
            
        a_func()
        print(f'a_func밖에서의 a값 : {a}')
        '''
        20
        20
        '''
        ```
        ```python
        a = 10

        def a_func():
            global a
            a = a+10
            print(f'a_func안에서의 a값 : {a}')
            
        a_func()
        print(f'a_func밖에서의 a값 : {a}')
        '''
        20
        20
        '''
        ```
    - 위 예시를 보게되면 함수내에서 `global 전역변수명` 을 선언해주게 되면 함수내에서 전역변수를 변경시킬수 있다는 것을 알수있음
- nonlocal 사용하기
    - nonlocal은 함수내에서 선언한 지역변수명을 그 함수 내부의 함수에서 사용하고 싶을 경우 선언
    - nonlocal이 적용이 안되는 경우
        ```python
        a = 10

        def a_func():
            nonlocal a
            a = a+10
            print(f'a_func안에서의 a값 : {a}')
            
        a_func()
        print(f'a_func밖에서의 a값 : {a}')
        '''
        SyntaxError: no binding for nonlocal 'a' found
        '''
        ```
        (nonlocal은 함수내의 지역변수를 함수내부의 함수에서 사용하고 싶을 경우 사용)
    
    - nonlocal이 적용되는 경우
        ```python
        a=10

        def a_func():
            a = 20
            def b_func():
                nonlocal a
                a = a+10 # nonlocal을 통해 b_func 바로 바깥 함수인 a_func의 지역변수 a 변경
                print(f'b_func안에서의 a값 : {a}') # 30
            b_func()
            print(f'b_func밖에서의 a값 : {a}') # 30
            
        a_func()
        print(f'a_func밖에서의 a값 : {a}') # 10 (a_func의 지역변수인 a는 지역변수로서 의미가 있음)
        '''
        b_func안에서의 a값 : 30
        b_func밖에서의 a값 : 30
        a_func밖에서의 a값 : 10
        '''
        ```
        ```python
        def a_func():
            a = 20
            def b_func():
                nonlocal a
                a = a+10 # nonlocal을 통해 b_func 바로 바깥 함수인 a_func의 지역변수 a 변경
                print(f'b_func안에서의 a값 : {a}') # 30
            b_func()
            print(f'b_func밖에서의 a값 : {a}') # 30
            
        a_func()
        print(f'a_func밖에서의 a값 : {a}') # a_func() 밖에서는 a라는 변수가 없기 때문에 오류 발생
        '''
        b_func안에서의 a값 : 30
        b_func밖에서의 a값 : 30
        NameError: name 'a' is not defined 
        '''
        ```
    - nonlocal을 이용한다면 함수의 return 값을 함수내부의 함수로 지정하게 된다면 함수내부의 지역변수값을 기억해둘수 있음(클로저)
        ```python
        a=10

        def a_func():
            a = 20
            def b_func():
                nonlocal a
                a = a+10 # nonlocal을 통해 b_func 바로 바깥 함수인 a_func의 지역변수 a 변경
                return a
            print(f'a_func의 시작시점의 a값 : {a}') 
            return b_func

        c = a_func()
        print(c()) # b_func 실행 20->30
        print(c()) # b_func 실행 30->40
        d = a_func() # 다시 함수를 선언했기때문에 d에서는 a가 20부터 시작
        print(c()) # b_func 실행 40->50 

        print(f'a_func밖에서의 a값 : {a}') # 10
        '''
        a_func의 시작시점의 a값 : 20
        30
        40
        a_func의 시작시점의 a값 : 20
        50
        a_func밖에서의 a값 : 10
        '''
        ```
- 변수에 들어간 값이 리스트일경우도 비슷하지만 다른점이 존재하여 주의 필요
    - 단순히 변수에 숫자가 들어갈때의 예시와 모두 동일한 결과가 나옴.
        - 예시 1
            ```python
            a = [10]

            def a_func():
                b = a+[20]
                print(b)
                
            a_func()
            print(a)
            '''
            [10, 20]
            [10]
            '''
            ```
        - 예시 2
            ```python
            a = [10]

            def a_func():
                a = [20]
                print(f'a_func안에서의 a값 : {a}')
                
            a_func()
            print(f'a_func밖에서의 a값 : {a}')
            '''
            a_func안에서의 a값 : [20]
            a_func밖에서의 a값 : [10]
            '''
            ```
        - 예시 3
            ```python
            a = [10]

            def a_func():
                a = a+[20]
                print(f'a_func안에서의 a값 : {a}')
                
            a_func()
            print(f'a_func밖에서의 a값 : {a}')
            '''
            UnboundLocalError: local variable 'a' referenced before assignment
            '''
            ```
    - 하지만 함수내부에서 global을 선언하지 않아도 append나 del을 이용하게 되면 리스트를 가지고 있는 전역변수의 내용이 변경됨
        - 예시
            ```python
            a = [10]

            def a_func():
                a.append(20)
                del a[0]
                print(f'a_func안에서의 a값 : {a}')
                
            a_func()
            print(f'a_func밖에서의 a값 : {a}')
            '''
            a_func안에서의 a값 : [20]
            a_func밖에서의 a값 : [20]        
            '''
            ```

---

## #15

### 클로저(closure)
- 클로저는 자신을 둘러싼 스코프의 상태값을 기억하는 함수
- 함수가 클로저가 되기 위한 조건
    - (클로저가 되기 위한 함수) 내부에 (함수)가 존재해야함
    - (클로저가 되기 위한 함수 **내부의 함수**)가 nonlocal을 통해 (클로저가 되기 위한 함수)의 상태값을 참조해야함
    - (클로저가 되기 위한 함수)는 (클로저가 되기 위한 함수 **내부의 함수**)를 return 해야함
- 클래스를 통해 클로저와 같은 기능을 만들수도 있음
    - 예시 1
        ```python
        class Averager():
            def __init__(self):
                self._series = []

            def __call__(self, v): # 객체명을 함수명처럼 사용할수 있게 해줌
                self._series.append(v)
                print(f'inner >>> {self._series} / {len(self._series)}')
                return sum(self._series) / len(self._series)


        # 인스턴스 생성
        averager_cls = Averager()

        # 누적
        print(averager_cls(15))
        print(averager_cls(35))
        print(averager_cls(40))

        '''
        inner >>> [15] / 1
        15.0
        inner >>> [15, 35] / 2
        25.0
        inner >>> [15, 35, 40] / 3
        30.0
        '''   
        ```
    - 예시 2
        ```python
        class Sum:
            def __init__(self, start):
                self.value = start

            def __call__(self, n):
                self.value += n
                return self.value
            
        sum_ins = Sum(10)

        print(sum_ins(20))
        print(sum_ins(30))
        '''
        30
        60
        '''
        ```
- 클로저 예시
    - 예시 1
        ```python
        # 클로저(Closure) 사용
        def closure_ex1():
            # Free variable
            series = []
            # 클로저 영역
            def averager(v):
                # series = [] # 주석 해제 후 확인
                series.append(v) # 리스트는 nonlocal 안해도 append가 됨, 하지만 명확하게 하기 위해 nonlocal series를 적어줘도 됨
                print(f'inner >>> {series} / {len(series)}')
                return sum(series) / len(series)
            
            return averager

        avg_closure1 = closure_ex1()

        print(avg_closure1(15))
        print(avg_closure1(35))
        print(avg_closure1(40))
        '''
        inner >>> [15] / 1
        15.0
        inner >>> [15, 35] / 2
        25.0
        inner >>> [15, 35, 40] / 3
        30.0
        '''
        ```
    - 예시 2
        ```python
        def sum_fun(m):
            value = m
            def plus(n):
                nonlocal value
                value += n
                return value
            return plus

        a = sum_fun(10)
        print(a(20))
        print(a(30))
        '''
        30
        60
        '''
        ```

#### References
- [우리를 위한 프로그래밍 : 파이썬 중급](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%A4%91%EA%B8%89-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90)
---

## #16

### 데코레이터(Decorator)
- 함수를 입력 인자로 받아 결괏값을 내는 경우 데코레이터로 작성해주면 편하게 사용 가능
- 대표적인 예로는 함수의 시간을 측정하기 위해 데코레이터를 만들수가 있음
- 데코레이터 함수 작성 규칙
    ```python
    def 데코레이터명(func):
        def 함수(*args,**kwargs):
            result = func(*args,**kwargs)
            # 함수 안에다가 func이 실행될때마다 같이 출력되거나 작동시키고 싶은 코드 작성
            return result
        return 함수

    @데코레이터명
    def A_func():
        pass

- 예시
    ```python
    import time

    def perf_clock(func):
        def perf_clocked(*args):
            # 함수 시작 시간 
            start_time = time.perf_counter() # time.time 보다 정밀하게 실행 시간을 측정하는 방법
            result = func(*args)
            # 함수 종료 시간 계산
            end_time = time.perf_counter()
            # 실행 함수명
            name = func.__name__
            # 함수 매개변수 
            arg_str = ', '.join(repr(arg) for arg in args)
            # 결과 출력
            print(f'[{end_time-start_time:.5f}] {name}({arg_str})) -> {result}') 
            return result 
        return perf_clocked

    # 데코레이터 사용 (코드 실행 순서 : perf_clock -> perf_clocked -> return)
    @perf_clock
    def time_func(seconds):
        time.sleep(seconds)

    @perf_clock
    def sum_func(*numbers):
        return sum(numbers)

    time_func(1.5) 
    sum_func(100, 150, 250, 300, 350)

    print('*'*10)
    # 데코레이터 미사용
    def time_func(seconds):
        time.sleep(seconds)

    def sum_func(*numbers):
        return sum(numbers)

    none_deco1 = perf_clock(time_func)
    none_deco1(1.5) 

    none_deco2 = perf_clock(sum_func)
    none_deco2(100, 150, 250, 300, 350)

    '''
    [1.50992] time_func(1.5)) -> None
    [0.00000] sum_func(100, 150, 250, 300, 350)) -> 1150
    **********
    [1.50245] time_func(1.5)) -> None
    [0.00000] sum_func(100, 150, 250, 300, 350)) -> 1150
    '''
    ```

#### References
- [우리를 위한 프로그래밍 : 파이썬 중급](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%A4%91%EA%B8%89-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90)
---


## #17

### 코루틴(Coroutine)
- yield를 통해 메인루틴과 서브루틴을 왔다갔다함
- 코루틴은 단일 쓰레드 이용
- 멀티 쓰레드를 사용하면 운영체제가 운영체제 커널의 알고리즘 스케줄러에 따라 실행 중인 쓰레드를 전환시키지만, 코루틴을 사용하면 프로그래머가 전환이 발생하는 시기를 제어할 수 있음.
- 코루틴 상태
    - GEN_CREATED : 처음 대기 상태
    - GEN_RUNNING : 실행 상태
    - GEN_SUSPENDED : yield 대기 상태
    - GEN_CLOSED : 실행 완료 상태
- 코루틴 예시
    - 예시 1
        ```python
        def coroutine1():
            print('>>> coroutine started.')
            i = yield
            print('>>> coroutine received : {}'.format(i))

        # 제네레이터 선언
        cr1 = coroutine1()

        print(cr1, type(cr1))

        next(cr1)
        next(cr1)
        '''
        >>> coroutine started.
        >>> coroutine received : None
        StopIteration
        '''
        ```
        (yield 부분에 값을 send를 통해서 보내줘야하는데 아무런 값을 보내지 않았기 때문에 None이 출력됨)
        (next를 하게되면 yield있는 부분까지 코드 실행)
    - 예시 2
        ```python
        def coroutine2(x):
            print('>>> coroutine started : {}'.format(x))
            y = yield x
            print('>>> coroutine received : {}'.format(y))
            z = yield x + y
            print('>>> coroutine received : {}'.format(z))
            
        cr2 = coroutine2(10)

        cr2.send(100)
        '''
        TypeError: can't send non-None value to a just-started generator  
        '''
        ```
        (yield에 도착해야지만 send를 통하여 값 전달가능. 반드시 처음에는 next실행 필요)
    - 예시 3
        ```python
        def number_coroutine():
            while True:        # 코루틴을 계속 유지하기 위해 무한 루프 사용
                x = (yield)    # 코루틴 바깥에서 값을 받아옴, yield를 괄호로 묶어야 함
                print(x)
                
        co = number_coroutine()
        next(co)      # 코루틴 안의 yield까지 코드 실행(최초 실행)

        co.send(1)    # 코루틴에 숫자 1을 보냄
        co.send(2)    # 코루틴에 숫자 2을 보냄
        co.send(3)    # 코루틴에 숫자 3을 보냄
        '''
        1
        2
        3
        '''
        ```
        <p align="center"><img src="../img/coroutine.jpg" width="40%" height="30%"></img></p>
#### References
- https://stackoverflow.com/questions/1934715/difference-between-a-coroutine-and-a-thread
- https://dojang.io/mod/page/view.php?id=2418
- [우리를 위한 프로그래밍 : 파이썬 중급](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%A4%91%EA%B8%89-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90)

---

## #18

###



#### References

---

## #19

###



#### References

---

## #20

###



#### References

---

## #21

###



#### References

---


## #100

### subprocess(자식프로세스 관리)
- 파이썬에서 자식 프로세스를 관리할 때는 **subprocess 내장 모듈**을 사용하는 것이 가장 좋음
    - subprocess 모듈을 사용하면 하위 프로세스를 쉽게 실행 가능

- subprocess 생성 코드1 (subprocess.run)
    - 코드
        ```python
        import subprocess

        result = subprocess.run('echo hi',capture_output=True,encoding='cp949',shell=True)
        # capture_output=True 로 지정하면 stdout에 실행결과를 저장, 오류는 stderr에 저장
        # window 기준 cp949, 다른 운영체제는 utf-8

        result.check_returncode()
        print(result.stdout)
        '''
        hi
        '''

        ```

- subprocess 생성 코드2 (subprocess.Popen)
    - Popen을 사용하면 파이썬이 다른 일을 하면서 주기적으로 자식 프로세스의 상태를 검사(polling)가능
    - 코드
        ```python
        import subprocess

        proc = subprocess.Popen('timeout 1',encoding='cp949',shell=True) 
        # 윈도우는 timeout, 다른 운영체제는 sleep
        while proc.poll() is None:
            print('작업 중...')
            # 메인 프로세스에서 시간이 걸리는 작업을 여기서 수행해주면 됨

        print('종료 상태',proc.poll())

        '''
        작업 중...
        작업 중...
        작업 중...
        작업 중...
        종료 상태 0
        '''
        ```
        (proc.poll()은 subprocess가 끝나지 않으면 계속 None을 반환)

    - 참고코드1
        ```python
        import subprocess
        import time

        start = time.time()
        proc = subprocess.Popen('timeout 1',encoding='cp949',shell=True)

        end = time.time()
        print(f'{end-start:.3f}초 소모')
        '''
        0.010초 소모
        '''
        ```
        (메인 프로세스가 먼저 종료되어버림. -> 1초를 기다려주지 않음)
    
    - 참고코드2 (run 은 대기를 시켜줌)
        ```python
        import subprocess
        import time

        start = time.time()
        proc = subprocess.run('timeout 1',encoding='cp949',shell=True)

        end = time.time()
        print(f'{end-start:.3f}초 소모')
        '''
        0.589초 소모
        '''
        ```

- 여러개의 자식 프로세스를 병렬로 실행하기
    - 코드
        ```python
        import subprocess
        import time

        start = time.time()
        sleep_procs = []
        for i in range(10):
            proc = subprocess.Popen(f'timeout {i+1}',encoding='cp949',shell=True)
            sleep_procs.append(proc)

        for proc in sleep_procs:
            proc.communicate() # communicate()를 해주면 서브프로세스가 종료될때까지 대기

        end = time.time()
        delta = end - start
        print(f'{delta:.3} 초만에 끝남')
        '''
        9.8 초만에 끝남
        '''

        ```
        (모든 프로세스가 순차적으로 진행됐다면 55초 소모)   
        (proc.communicate()를 해주었기 때문에 모든 자식프로세스가 끝날때까지 메인프로세스가 대기함)

- communicate() 좀 더 이해해보기
    - 코드1
        ```python
        import subprocess
        import time

        start = time.time()
        sleep_procs = []
        for i in range(10):
            proc = subprocess.Popen(f'timeout {i+1}',encoding='cp949',shell=True)
            sleep_procs.append(proc)


        sleep_procs[0].communicate()

        end = time.time()
        delta = end - start
        print(f'{delta:.3} 초만에 끝남')

        sleep_procs[3].communicate()
        end = time.time()
        delta = end - start
        print(f'{delta:.3} 초만에 끝남')

        sleep_procs[9].communicate()
        end = time.time()
        delta = end - start
        print(f'{delta:.3} 초만에 끝남')
        '''
        1.03 초만에 끝남
        4.08 초만에 끝남
        10.1 초만에 끝남
        '''
        ```
    
    - 코드2
        ```python
        import subprocess
        import time

        start = time.time()
        sleep_procs = []
        for i in range(10):
            proc = subprocess.Popen(f'timeout {i+1}',encoding='cp949',shell=True)
            sleep_procs.append(proc)


        sleep_procs[0].communicate()

        end = time.time()
        delta = end - start
        print(f'{delta:.3} 초만에 끝남')

        sleep_procs[9].communicate()
        end = time.time()
        delta = end - start
        print(f'{delta:.3} 초만에 끝남')

        sleep_procs[3].communicate()
        end = time.time()
        delta = end - start
        print(f'{delta:.3} 초만에 끝남')
        '''
        0.81 초만에 끝남
        9.8 초만에 끝남
        9.8 초만에 끝남
        '''
        ```
        (3번 프로세스는 9번프로세스가 끝날때 이미 끝나있기때문에 대기시간이 필요없이 바로 지나감)
    
    - 자식 프로세스가 멈추는 경우나 교착 상태를 방지하기 위해 communicate 메서드에 대해 timeout 파라미터 사용가능
        ```python
        import subprocess
        import time

        start = time.time()
        sleep_procs = []
        for i in range(10):
            proc = subprocess.Popen(f'timeout {i+1}',encoding='cp949',shell=True)
            sleep_procs.append(proc)


        sleep_procs[3].communicate(timeout=1)

        end = time.time()
        delta = end - start
        print(f'{delta:.3} 초만에 끝남')
        '''
        >>> 오류 발생
        subprocess.TimeoutExpired: Command 'timeout 4' timed out after 1.0 seconds
        '''

        ```

---
