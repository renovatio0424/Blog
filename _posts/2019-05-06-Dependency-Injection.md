---
title: "[Programming] 의존성 주입 쉽게 이해하기"

categories:
  - TDD

tags:
  - Dependency Injection
  - Dagger2

author: Reno

toc: true
toc_label: "차례"
toc_icon: "list"

comments: true
---

*이 글은 의존성 주입(Dependency Injection)에 대해 깊은 이해를 다루기 보다는 왜 써야하고 어떤 점이 좋은지에 대해 알기 쉽게 설명하는 글입니다*

# 의존성 주입이란 무엇인가??
프로그래밍에서 의존성이란 무엇일까요??

실생활을 예를 들어 설명해볼게요

![오토바이](/assets/images/post_image/오토바이.jpg)

우리가 오토바이를 하나 구매했다고 가정해봅시다

오토바이를 잘 타고 다니다가 갑자기 바퀴에 펑크가 난거에요

그래서 정비소에 가서 바퀴를 교체하려고 하는데 **'그 회사에서 만든 바퀴로만 교체할 수 있다'** 고 하네요??

아니 왜 다른 오토바이 바퀴들로도 교체할 수 있게 만들지 왜 꼭 **'그 바퀴로만 교체하게'** 만들어서 힘들게 할까요?

이 오토바이는 자사 회사 바퀴에 <span style="font-family:Papyrus; font-size:3em;">**의존적**</span> 이군요 :expressionless:

실제로 프로그래밍에서 이런 경우가 자주 생깁니다.

각 객체가 독립적이어야 되지만 각 객체와 **협력** 을 해야되기 때문에 서로에게 의존적인 경우가 생깁니다.

하지만 유연한 코드를 위해 최대한 의존성을 줄여야 겠죠??

<br><span style="font-family:Papyrus; font-size:2em;">의존성을 줄이려면 어떻게 해야할까요??</span>

혹시 다들 이케아에서 가구 사보신적 있나요??

![이케아2](/assets/images/post_image/ikea2.jpg)

제가 최근에 이케아에서 책상을 산적이 있어요

처음에는 그냥 카탈로그에 있는 부품대로 가구를 사려다가

책상 높이 조절을 할 수 있으면 좋겠다는 생각에 높이 조절이 가능한 다리를 찾았죠

다행히 책상이랑 다리랑 호환이 되서 구매를 한 기억이 있어요 ㅎㅎ

만약 카탈로그에 명시되어 있는 대로만 호환이 된다면 할 수 없었겠죠??

객체도 마찬가지 입니다. 우리가 만약 **의존성이 필요한 객체를 명시한다면**

***다른 객체로 교체해야할 상황이 발생했을 때*** 어려움을 겪을 겁니다

그럼 어떻게 해야 의존성이 적어질까요??

객체에서 의존성이 필요한 객체를 명시하지 않고

<span style="font-family:Papyrus; font-size:2em;">객체를 사용하는 곳에서 / 사용하려는 객체를 직접 만들어 주입한다면?</span>

<span style="font-family:Papyrus; font-size:2em;"> = 가구를 사용하는 사람이 / 직접 필요한 부품을 골라서 조립한다면?</span>

의존성이 줄어들겠죠 ㅎㅎ

# 의존성 주입을 어떻게 하나??

제가 샘플 코드를 한번 만들어 봤습니다 ㅎㅎ

오토바이를 작동하는 프로그램을 만들어봤습니다

의존성 주입을 하기 전에는 이런 식으로 만들 수 있겠네요
```java

/**
* 의존성을 주입하기 전의 코드
*/

public class NoDIExample {
    public static void main(String[] args) {
        MotorCycleImpl myMotorCycle = new MotorCycleImpl();
        myMotorCycle.run();
    }
}

public class MotorCycleImpl implements MotorCycle {
    MotorImpl motor;
    Wheel wheel;
    MotorCycleFrame frame;

    public MotorCycleImpl() {
        this.motor = new MotorImpl();         /*의존적인 객체들 ...*/
        this.wheel = new Wheel();             /*의존적인 객체들 ...*/
        this.frame = new MotorCycleFrame();   /*의존적인 객체들 ...*/
    }

    @Override
    public void run() {
        motor.ready();
        motor.run();
    }
}

public class MotorImpl implements Motor {
    private boolean isRunning = false;

    @Override
    public void ready() {
        this.isRunning = true;
    }

    @Override
    public void run() {
        System.out.print("run ==3");
    }

    public boolean isRunning() {
        return isRunning;
    }
}

public class Wheel {
    Tire tire;
    WheelFrame wheelFrame;

    public Wheel() {
        this.tire = new Tire();               /*의존적인 객체들 ...*/
        this.wheelFrame = new WheelFrame();   /*의존적인 객체들 ...*/
    }

}

//나머진 빈 생성자만 있기 때문에 생략
```
이 코드에서 의존성을 주입해 볼까요?

```java
public class Di_Sample {

    public static void main(String[] args) {
        /*의존성을 주입함으로써 생기는 boiler plate 코드들 ...*/
        MotorImpl myMotor = new MotorImpl();
        Tire myTire = new Tire();
        WheelFrame myWheelFrame = new WheelFrame();
        MotorCycleFrame myMotorCycleFrame = new MotorCycleFrame();
        Wheel myWheel = new Wheel(myTire, myWheelFrame);
        /*End*/
        MotorCycleImpl myMotorCycle = new MotorCycleImpl(myMotor, myWheel, myMotorCycleFrame);
        myMotorCycle.run();
    }
}

public class MotorCycleImpl implements MotorCycle {
    MotorImpl motor;
    Wheel wheel;
    MotorCycleFrame frame;

    /*의존성 주입하기*/
    public MotorCycleImpl(MotorImpl motor, Wheel wheel, MotorCycleFrame motorCycleFrame) {
        this.motor = motor;
        this.wheel = wheel;
        this.frame = motorCycleFrame;
    }

    @Override
    public void run() {
        motor.ready();
        motor.run();
    }
}

public class Wheel {
    Tire tire;
    WheelFrame wheelFrame;

    /*의존성 주입하기*/
    public Wheel(Tire tire, WheelFrame wheelFrame) {
        this.tire = tire;
        this.wheelFrame = wheelFrame;
    }

}

```

의존성을 주입함으로써 의존성이 줄어드는 것을 확인했지만

의존성을 주입함으로써 boiler plate 코드들이 생겼군요 ...

가독성이 매우 떨어지네요 ㅠㅠ

어떻게 해결하면 좋을까요??

# DI Framework의 필요성

그래서 개발자들은 DI Framework를 사용하게 된 겁니다.

어짜피 필요한 객체들은 정해져 있으니 Framework를 통해 자동으로 생성해 주입하겠다는 것이죠.

여러 DI Framework가 존재하지만 그중에서도 저는 Dagger2를 사용해보았습니다.

Dagger2에 대해 잘 모르시는 분들을 위해 간단하게 설명을 하자면

* @Component : Component는 Module을 통해 의존성 주입을 관리합니다.  
* @Module : Module은 의존성 주입할 객체를 만들어줍니다.
* @Inject : Component에서 주입할 객체를 명시해줍니다.

자세한 내용은 [Dagger 공식홈페이지](https://google.github.io/dagger/) 에서 공부하시거나 구글링을 해보시면 좋을듯 하네요 ㅎㅎ

제가 공부할 때 많이 참고한 영상도 첨부해봅니다 ㅎㅎ

<style>.embed-container { position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; } .embed-container iframe, .embed-container object, .embed-container embed { position: absolute; top: 0; left: 0; width: 100%; height: 100%; }</style><div class='embed-container'><iframe src='https://www.youtube.com/embed/plK0zyRLIP8' frameborder='0' allowfullscreen></iframe></div>

그럼 샘플 코드를 확인해볼까요??

각 객체들은 위 코드에서 써져있는 것과 동일하고 나머지는 아래와 같습니다.

```java

public class Dagger_Sample {
    public static void main(String[] args) {
        Application app = new Application();
        app.run();
    }
}

class Application {
    //의존성을 주입합니다
    @Inject
    MotorCycleImpl motorCycle;

    void run() {
        //필요한 모듈을 주입할 Component를 생성합니다.
        MotorCycleComponent component = DaggerMotorCycleComponent.builder()
                .motorCycleModule(new MotorCycleModule())
                .build();

        //컴포넌트를 주입합니다
        component.inject(this);

        motorCycle.run();
    }
}

//어떤 Module을 주입하는 Component 인지 명시해줍니다.
@Component(modules = MotorCycleModule.class)
public interface MotorCycleComponent {
    void inject(Application application);
}

//Module에 대해 명시해줍니다
@Module
public class MotorCycleModule {

    //주입할 MotorImpl를 만듭니다
    @Provides
    MotorImpl provideMotor(){
        return new MotorImpl();
    }

    //주입할 MotorCycleFrame를 만듭니다
    @Provides
    MotorCycleFrame provideMotorCycleFrame(){
        return new MotorCycleFrame();
    }

    //주입할 Tire를 만듭니다
    @Provides
    Tire provideTire(){
        return new Tire();
    }

    //주입할 WheelFrame를 만듭니다
    @Provides
    WheelFrame provideWheelFrame(){
        return new WheelFrame();
    }

    //주입할 MotorCycleImpl를 만듭니다
    @Provides
    MotorCycleImpl provideMotorCycle(MotorImpl motor, Wheel wheel, MotorCycleFrame frame){
        return new MotorCycleImpl(motor, wheel, frame);
    }

    //주입할 Wheel를 만듭니다
    @Provides
    Wheel provideWheel(Tire tire, WheelFrame wheelFrame){
        return new Wheel(tire, wheelFrame);
    }
}
```

코드를 보시면 Component와 Module을 생성한 코드가 생기긴 했지만

실제 동작하는 곳에서는 boiler plate 코드가 없어진 모습을 확인할 수 있습니다.

# 마무리
사실 의존성 주입을 하는 이유는 단순히 유연한 코드를 만들기 위해서만은 아닙니다

요새 많이들 고민하고 있는 **Testable Architecture** 를 구현하기 위해서 이기도 하죠

제가 요새 TDD에 관심을 가지고 있기 때문에 꼭 짚고 넘어가야할 개념을 정리하기 위해 글을 적어보았는데

도움이 되셨으면 좋겠네요 ㅎㅎ

추가적으로 위 샘플 코드는 저의 [GitHub](https://github.com/renovatio0424/DIExample)에 공개 해두었으니 필요하시면 확인하시면 될것 같아요 :smiley:
