## 탄생 배경

초기에는 하나의 클래스 안에 온갖 코드가 존재했습니다. 기능 별로 아무리 모듈화 하였다고 한들, DB 꺼내오는 로직과 화면에 출력할 로직 등이 뒤죽박죽 섞여 지저분한 코드로 가득했습니다. 이러한 코드는 개발자 본인이 유지 보수하기에도 복잡하고, 다른 개발자가 투입되면 분석하고 유지 보수 업무를 돕기 굉장히 오랜 시간이 걸릴 수 밖에 없습니다.

코드 내에서 서로의 역할을 분리하여 각자의 역할에 집중할 수 있게 개발한다면 유지 보수성, 애플리케이션의 확장성, 유연성 등의 장점을 살릴 수 있겠다고 판단하여 생겨난 것이 바로 MVC 패턴입니다.

## 정의

MVC 패턴은 `Model-View-Controller` 의 약자로, 개발 과정에서 3가지 형태로 역할을 나누어 개발하는 방법론을 뜻합니다. 

![image](https://user-images.githubusercontent.com/56083021/140029343-75cf3543-c399-4565-9b2e-431cc313e74e.png)

### Model

데이터베이스, 상수, 변수 등과 같은 애플리케이션의 정보나 데이터를 관리하는 역할을 합니다. '애플리케이션에서 `무엇` 을 할지 정해야 한다.', '내부 비즈니스 로직을 처리해야 한다.'와 같은 요구 사항이 나온다면 Model을 설계한다고 이해하면 됩니다.

### 특징

1. 뷰나 컨트롤러에 대해서 어떤 정보도 알지 못합니다.
- 데이터 변경이 일어났을 때 모델에서 화면 UI를 직접 조정할 수 있도록 뷰를 참조하는 내부 속성을 가져서는 안 됩니다. 예를 들어, 모델에 의해서 네모 박스의 내용이 모델의 속성에 따라 바뀌는 것은 가능하지만, 네모 박스의 글자 폰트나 크기가 변해서는 안 됩니다. 이는 뷰에 영역이기 때문이죠.

1. 변경이 일어나면, 변경 통지에 대한 처리 방법을 구현해야 합니다.
- 모델의 속성 중 텍스트 정보가 변경된다면, 이벤트를 발생하여 누군가에게 전달해야 하며, 누군가 모델을 변경하도록 요청하는 이벤트를 보냈을 때 이를 수신할 수 있는 처리 방법을 구현해야 합니다. 즉, 외부에서 모델의 값을 업데이트할 수 있어야 하고 업데이트 내용을 누군가에게 알릴 수 있어야 합니다.

## View

input 텍스트, 체크 박스 항목 등과 같은 사용자 인터페이스 요소를 나타냅니다. 화면에 무엇인가를 보여주기 위한 역할이라고 생각하면 됩니다. 

### 특징

1. 모델이 가지고 있는 정보를 따로 저장해서는 안됩니다.
- 화면에 글자를 표시하기 위해 모델이 가지고 있는 정보를 전달 받기만 해야 하고, 그 정보를 유지 하기 위해 뷰 내부에 값을 저장하면 안됩니다.

1. 모델이나 컨트롤러에 대해서 어떤 정보도 알지 못합니다.
- 뷰는 데이터를 받으면 화면에 표시해 주는 역할만 해야 합니다.

1. 변경이 일어나면, 변경 통지에 대한 처리 방법을 구현해야 합니다.
- 모델과 동일합니다. 외부에서 뷰의 값을 업데이트 할 수 있어야 하고 업데이트 내용을 누군가에게 알릴 수 있어야 합니다.

## Controller

데이터와 사용자 인터페이스 요소들을 연결하는 Bridge 역할을 합니다. 사용자가 데이터를 클릭하고 수정하는 것에 대한 이벤트를 처리한다고 생각하면 됩니다.

### 특징

1. 모델이나 뷰에 대해서 알고 있어야 합니다.
- 모델이나 뷰는 서로의 존재를 모르고 업데이트 사항을 외부로 알리거나 받는 역할만 하는데, 컨트롤러 이들을 중재하기 위해 모두 알고 있어야 합니다.

1. 모델이나 뷰의 변경을 모니터링 해야 합니다.
- 모델이나 뷰의 변경 통지를 받으면 이를 해석해서 각각의 구성 요소에게 통지를 해야 합니다. 또한, 애플리케이션의 메인 로직은 컨트롤러가 담당하게 됩니다.

## MVC 패턴을 사용한 예제

### RPG 게임에서 캐릭터의 상태를 변경하는 과정

### 실제 코드

**[Controller]**

```java
public class CharacterController {
    private CharacterModel model;
    private CharacterView view;

    public CharacterController(CharacterModel model, CharacterView view) {
        this.model = model;
        this.view = view;
    }

    // State change
    public void setCharacterName(String name) {
        model.setName(name);
    }

    public String getCharacterName() {
        return model.getName();
    }

    // State change
    public void setCharacterLevel(int level) {
        model.setLevel(level);
    }

    public int getCharacterLevel() {
        return model.getLevel();
    }

    // State change
    public void setCharacterLife(int life) {
        model.setLife(life);
    }

    public int getCharacterLife() {
        return model.getLife();
    }

    // View selection(Rendering)
    public void updateView() {
        view.printView(model);
    }
}
```

**[Model]**

```java
public class CharacterModel {
    private String name;
    private int level;
    private int life;

    public CharacterModel(String name, int level, int life) {
        this.name = name;
        this.level = level;
        this.life = life;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getLevel() {
        return level;
    }

    public void setLevel(int level) {
        this.level = level;
    }

    public int getLife() {
        return life;
    }

    public void setLife(int life) {
        this.life = life;
    }
}
```

**[View]**

```java
public class CharacterView {
    // State query
    // 개인적으로 Model을 직접 View의 인자로 넘기는 것보다는
    // UI에 보여줄 속성만 뽑아낸 DTO를 생성하여
    // CharacterDTO를 만들어 View의 인자로 넘기는 것을 선호한다.
    // 간단한 예제를 위해 이렇게 표현하였다.
    public void printView(CharacterModel model) {
        System.out.println("Name :: " + model.getName());
        System.out.println("Level :: " + model.getLevel());
        System.out.println("Life :: " + model.getLife());
        System.out.println();
    }
}
```

**[Main]**

```java
public class MVCMain {

    public static void main(String[] args) {
        CharacterModel model = new CharacterModel("Jayon", 20, 3);
        CharacterView view = new CharacterView();

        CharacterController controller = new CharacterController(model, view);
        controller.updateView();

        controller.setCharacterLevel(controller.getCharacterLevel() + 1);
        controller.updateView();

        controller.setCharacterLife(controller.getCharacterLife() + 10);
        controller.updateView();

        controller.setCharacterName("Jinyoung");
        controller.updateView();
    }
}
```

**[실행 결과]**

```java
Name :: Jayon
Level :: 20
Life :: 3

Name :: Jayon
Level :: 21
Life :: 3

Name :: Jayon
Level :: 21
Life :: 13

Name :: Jinyoung
Level :: 21
Life :: 13
```

## MVC 패턴의 장점과 한계

### 장점

- 각 구성 요소를 분리함으로써 팀원이 각자 맡은 부분의 개발에만 따로 집중할 수 있게 만들어 개발의 효율성과 유저 보수성 및 확장성을 보장합니다.

### 한계

- Model과 View가 서로에 대한 정보를 가지고 있지 않고 독립적이긴 하지만, 다수의 Model과 해당하는 다수의 View가 하나의 Controller를 통해 소통하므로 Model과 View의 의존성이 완벽하게 분리되지는 않습니다.
- 따라서 Controller 하나에 많은 Model과 View가 복잡하게 연결되어 있는 상황이 생길 수 있습니다.
- 이렇게 MVC가 너무 복잡하고 비대해지면, 새 기능을 추가할 때마다 코드 분석/수정/테스트가 어려워집니다.
- 이를 보완하기 위해 MVP, MVVM, Viper, Clean Architecture, Flux, Redux, RxMVVM... 등 수많은 패턴들이 생겨났습니다.

![image](https://user-images.githubusercontent.com/56083021/140029371-3f606aad-6a9a-48b3-851e-99bd667ee5e3.png)

## 출처

[https://m.blog.naver.com/jhc9639/220967034588](https://m.blog.naver.com/jhc9639/220967034588)

[https://medium.com/@jang.wangsu/디자인패턴-mvc-패턴이란-1d74fac6e256](https://medium.com/@jang.wangsu/%EB%94%94%EC%9E%90%EC%9D%B8%ED%8C%A8%ED%84%B4-mvc-%ED%8C%A8%ED%84%B4%EC%9D%B4%EB%9E%80-1d74fac6e256)

[https://www.freecodecamp.org/news/the-model-view-controller-pattern-mvc-architecture-and-frameworks-explained/](https://www.freecodecamp.org/news/the-model-view-controller-pattern-mvc-architecture-and-frameworks-explained/)

[https://www.crocus.co.kr/1539](https://www.crocus.co.kr/1539)
