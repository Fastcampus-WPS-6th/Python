# 모듈과 패키지

## 모듈

지금까지 셸에서 진행한 실습의 경우, 하나의 모듈 내부에서 작업한 것으로 취급된다.

파이썬 파일은 각각 하나의 모듈로 취급되며, 실행이나 함수의 정의, 단순 변수의 모음 등등 여러 역할을 한다.

#### 모듈 불러오기 (import)

기본 게임모듈에서, 부가적인 기능들을 불러와서 사용하는 형태로 코드를 작성해본다.

**module/shop.py**

```python
def buy_item():
    print('Buy item!')

buy_item()
```

**module/game.py**

```python
def play_game():
    print('Play game!')

play_game()
```

**module/lol.py**

```python
import game
import shop

print('= Turn on game =')
game.play_game()
shop.buy_item()
```

#### \_\_name\_\_변수 

`lol.py`가 실행될 때, `game`과 `shop`가 import되는 순간 해당 코드가 실행되어 버리는 문제가 있다.

이 때, 파이썬 인터프리터를 이용해 실행한 코드인지를 확인하여 단순히 `import`한 모듈의 경우 실행을 막는 방식을 사용할 수 있다.

각 모듈은 자신의 이름을 가지며, 모듈 이름은 모듈의 전역변수 `__name__`에서 확인 할 수 있다.

```
print(__name__)
```

파이썬 인터프리터가 실행한 모듈의 경우, `__main__`이라는 이름을 가진다. 따라서 `python <파일명>`으로 실행한 경우에만 동작할 부분은 if문으로 감싸준다.

**module/shop.py**

```python
def buy_item():
    print('Buy item!')

if __name__ == '__main__':
    buy_item()
```

**module/game.py**

```python
def play_game():
    print('Play game!')

if __name__ == '__main__':
    play_game()
```

다시 `lol.py`를 실행해본다.


#### lol.py 리펙토링

```python
import game
import shop

def turn_on():
    print('= Turn on game =')

    while True:
        choice = input('What would you like to do?\n  1: Go to Shop, 2: Play Game, 0: Exit\n    Input : ')
        if choice == '0':
            break
        elif choice == '1':
            shop.buy_item()
        elif choice == '2':
            game.play_game()
        else:
            print('Choice not exist')
        print('-----------------------')

    print('= Turn off game =')

if __name__ == '__main__':
    turn_on()
```

#### 네임스페이스 (Namespace)

각 모듈은 독립된 네임스페이스(이름공간)를 가진다. 메인으로 사용되고 있는 모듈이 아닌 import된 모듈의 경우, 해당 모듈의 네임스페이스를 사용해 모듈 내부의 데이터에 접근한다.

같은 함수명을 가진 모듈을 2개 만들고, 한 쪽에서 다른 한 쪽의 모듈을 import 한 뒤 각각의 모듈의 함수를 실행시켜본다.

#### from을 사용해 모듈의 함수를 직접 import

`import 모듈명`의 경우, 모듈의 이름이 전역 네임스페이스에 등록되어 `모듈명.함수`로 사용가능하다.

모듈명을 생략하고 모듈 내부의 함수를 쓰고 싶다면, `from 모듈명 import 함수명`으로 불러들일 수 있다.

#### from 모듈명 * 을 사용해 모듈 내 모든 식별자 (변수, 함수) import

#### as로 별칭 할당

`from 모듈명 import` 또는 `import 모듈명`에서, 같은 모듈명이 존재하거나 혼동 될 수 있을 경우, 뒤에 `as`를 붙여 사용할 모듈명을 변경할 수 있다.

#### 커맨드라인에서 인자 전달

프로그램 실행 시 인자를 전달 할 수 있다. 파이썬의 내장`sys`모듈의 `argv`리스트에서 확인 할 수 있다.

리스트의 첫 번째 항목은 모듈명이 전달되므로, 2번째 항목부터 전달 된 값을 확인 할 수 있다.

## 패키지

패키지는 모듈들을 모아 둔 특별한 **폴더**를 뜻한다. (일반적인 폴더와는 다르다)

폴더를 패키지로 만들면 계층 구조를 가질 수 있으며, 모듈들을 해당 패키지에 모을 수 있는 역할을 한다.

패키지를 만들 때는 패키지로 사용할 폴더에 `__init__.py`파일을 넣어주면, 해당 폴더는 패키지로 취급된다. (비어있어도 무관하다)

`shop.py`와 `game.py`를 `func`패키지에 넣어본다.

```
├── func
│   ├── __init__.py
│   ├── game.py
│   └── shop.py
└── lol.py
```

패키지는 모듈과 동일하게 import할 수 있으며, 위와 같이 func패키지에 모듈들을 넣은 경우에는

`from func import game, shop`으로 기존 코드의 변경 없이 패키지에서 모듈을 가져오는 방식을 사용할 수 있다.

또는 단순히 `import func`후 `func.game`, `func.shop`을 사용하는 방식도 가능하다.

#### *, \_\_all\_\_

패키지에 포함된 하위 패키지 및 모듈을 불러올 때, `*`을 사용하면 해당 모듈의 모든 식별자들을 불러온다.

이 때, 각 모듈에서 자신이 import될 때 불러와질 목록을 지정하고자 한다면 `__all__` 을 정의하면 된다.

패키지 자체를 import시에 자동으로 가져오고 싶은 목록이 있다면, 패키지의 `__init__.py`파일에 해당 항목을 import해주면 된다.


## 실습

1. 위 프로그램에 `friends`패키지를 만들고, `send_message`함수를 가진 `chat`모듈을 추가한다.  
`send_message`는 `input`을 이용해 2개의 인자를 받으며(친구명, 메세지), 실행 시 `print`함수를 통해 메세지를 보냈다는 문구를 출력한다.
2. 프로그램을 실행했을 때, `3`을 입력하면 `send_message`함수를 실행하도록 한다.
