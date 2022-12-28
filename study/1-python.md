## Table of Contents

- [클래스(class), 객체(object), 인스턴스(instance), 어트리뷰트(attribute) 용어 설명](#1)
- [`__str__`과 `__repr__` 의 차이점](#2)
- [`__dict__` 와 `dir()` 의 차이점](#3)
- [`__doc__`](#4)
- [클래스 변수와 인스턴스 변수](#5)
- [`__init__`과 `__del__`](#6)
- [@classmethod 와 @staticmethod](#7)
- [다양한 special method (= magic method)](#8)
- [](#)
- [](#)
- [](#)
- [](#)

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
