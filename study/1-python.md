## Table of Contents

- [subprocess(자식프로세스 관리)](#1)
- [](#2)

---

## #1

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

#### References

---

## #2

### 

#### References