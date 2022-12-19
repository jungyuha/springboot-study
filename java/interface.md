# 인터페이스(Interface)

**기록 ✍️**

#### author : jung yuha

#### **first Registered :** 2022-12-19 Mon

#### last modified : 2022-12-19 Mon

#### 참고링크

* [https://limkydev.tistory.com/197](https://limkydev.tistory.com/197)
* [https://wikidocs.net/217](https://wikidocs.net/217)

## \[1]인터페이스(Interface)의 개념과 기초

### 1) 인터페이스란?

* 인터페이스 구현은 아래와같이 **implements** 라는 키워드를 사용한다.
  * ```java
    class Kcp extends PayPg implements PgCo { // PgCo 인터페이스 구현 ..
    ```
* **객체의 다형성**을 구현한다.
  * **객체의 다형성 : 객체가 한 개 이상의 자료형 타입을 갖게되는 특성**

## \[2]인터페이스(Interface)의 필요성

### 예시 : 결제 프로세스

> 결제구분코드를 던지는 pay클래스가 있다.
>
> 결제 요청이 들어오면 결제구분코드를 던져준다.
>
> **KCP**가 오면 **KCP\_AAA**를 던져준다.
>
> **이니시스**가 오면 **INI\_AAA**를 던져준다.

### (1) sample.java

```java
class PayPg {
    String name;

    void setName(String name) {
        this.name = name;
    }
}

class Kcp extends PayPg {
}

class Inicis extends PayPg {
}

class Pay {
    void procPay(Kcp kcp) {  // 메서드 오버로딩
        System.out.println("KCP_AAA"); // KCP가 오면 KCP_AAA를 던져준다. 
    }

    void procPay(Inicis inicis) {  // 메서드 오버로딩
        System.out.println("INI_AAA"); // 이니시스가 오면 INI_BBB를 던져준다.
    }
}

public class Sample {
    public static void main(String[] args) {
        Pay pay = new Pay();
        Inicis inicis = new Inicis();
        Kcp kcp = new Kcp();
        pay.procPay(inicis);  // INI_BBB 출력
        pay.procPay(kcp);  // KCP_AAA 출력
    }
}
```

#### 설명

1. Pay 클래스는 KCP가 왔을 때, 이니시스가 왔을 때 **각각 다른** procPay 메서드가 호출된다.
2. **메서드 오버로딩** : Pay 클래스의 procPay 메서드처럼 입력값의 자료형 타입이 다를 경우(Kcp, Inicis)\
   **메서드 명을 동일하게 사용할 수 있다.**

#### ⭐️ 문제점

KCP ,이니시스 외에도 ksnet, Toss 등이 계속 추가된다면 Pay 클래스는 PG사가 추가될 때마다\
**매번 각 PG사의 결제 구분코드에 맞게 procPay 메서드를 추가해야 한다.**

### \[2] PgCo.java (인터페이스)

#### 1. 인터페이스 생성

```java
interface PgCo {
}
```

#### 2. 각 클래스의 PgCo 인터페이스 구현

```java
class PayPg {
    String name;

    void setName(String name) {
        this.name = name;
    }
}

class Kcp extends PayPg implements PgCo { // PgCo 인터페이스 구현 , 객체의 다형성
}

class Inicis extends PayPg implements PgCo { // PgCo 인터페이스 구현 , 객체의 다형성
}

class Pay {
//Kcp, Inicis은 각각 Kcp, Inicis의 객체임과 동시에 PgCo 인터페이스의 객체이기도 하다.
    void procPay(PgCo pgCo) { 
        System.out.println("...");  
    }
}

public class Sample {
    public static void main(String[] args) {
        Pay pay = new Pay();
        Inicis inicis = new Inicis();
        Kcp kcp = new Kcp();
        pay.procPay(inicis);  // INI_BBB 출력
        pay.procPay(kcp);  // KCP_AAA 출력
    }
}
```

* procPay 메서드의 입력으로 Kcp, Inicis을 각각 필요로 했지만 이제 이것을 **PgCo라는 인터페이스로 대체**해\
  1개의 메소드로 줄일 수 있다.(메서드 오버로딩도 사라진다.)
* Kcp, Inicis은 각각 Kcp, Inicis의 객체임과 동시에 **PgCo 인터페이스의 객체**이기도 하다.\
  따라서 , 위와같이 ⭐️ **PgCo를 자료형의 타입으로 사용할 수 있는 것** ⭐️이다.
  * **Kcp** => **Kcp** 클래스의 객체, **PgCo** 인터페이스의 객체
  * **Inicis** => **Inicis** 클래스의 객체, **PgCo** 인터페이스의 객체
  * 이는 객체가 한 개 이상의 자료형 타입을 갖게되는 특성을 보여준다.=> **객체의  다형성**
* **procPay 메서드는 pgCo 인터페이스를 구현한 객체가 공통적으로 사용하는 메서드가 된다.**

#### 인터페이스의 장점

1. 이제 어떤 PG사가 추가되더라도 Pay는 **procPay 메서드를 추가할 필요가 없다.**\

   * 다만 **PG사가 추가 될 때마다 다음과 같이 PgCo 인터페이스를 구현한 클래스를 작성**하기만 하면 된다.
     * ```java
       class KsNet extends PayPg implements PgCo { // PgCo 인터페이스 구현 , 객체의 다형성
       ```

#### 3. PgCo 인터페이스의 메서드 선언

```java
interface PgCo {
   String getPGName(); // 인터페이스의 메소드는 인터페이스에서 직접 구현하지 않는다.
}
```

* ⭐️ 인터페이스의 메서드는 메서드의 이름과 입출력에 대한 정의만 있고 **그 내용은 없다.**
* ⭐️ 인터페이스의 메서드는 **해당 인터페이스를 implements한 클래스들이 구현해야만 하는 것**이다

#### 4. PgCo **인터페이스를 implements한 각 클래스들의 메서드 구현**&#x20;

<pre class="language-java"><code class="lang-java">class Kcp extends PayPg implements PgCo { // PgCo 인터페이스 메서드 구현
<strong>    public String getPGName() {
</strong>        return "KCP";
    }
<strong>}
</strong>
class Inicis extends PayPg implements PgCo { // PgCo 인터페이스 메서드 구현
    public String getPGName() {
            return "INI";
        }
}
</code></pre>

#### 5. Pay 클래스의 **procPay 메서드 구현**&#x20;

```java
class Pay {
    void procPay(PgCo pgCo) { 
        System.out.println(pgCo.getPGName()+"_AAA");  
    }
}
```

## \[3]인터페이스(Interface)의 핵심

1. PG사 종류 만큼의 procPay 메서드가 필요했던 Pay 클래스를 PgCo 인터페이스를 이용하여 구현했더니\
   단 한개의 procPay 메서드로 구현이 가능해졌다. \
   **하지만 이는 인터페이스의 핵심이 아니다.**
2. Pay 클래스가 PG사의 종류(클래스 타입)에 의존적인 클래스에서\
   ⭐️ **PG사의 종류와 상관없는 독립적인 클래스가 되었다는 것** ⭐️이 바로 인터페이스의 핵심이다.
3. 즉, **동일한 목적 하에 동일한 기능을 수행하게끔 강제하는 것**이 바로 인터페이스의 역할이 되는 것이다.&#x20;

## \[4]인터페이스(Interface)의 특징

1. **강제성을 가진다**. : 상속과는 달리 인터페이스는 인터페이스의 메서드를 반드시 구현해야 한다.
   * 상속은 자식 클래스가 부모 클래스의 메서드를 오버라이딩하지 않고 사용할 수 있기에\
     해당 메서드를 반드시 구현해야 한다는 "강제성"을 갖지 않는다.

#### 인터페이스에서 메서드 구현하기

1.  **디폴트 메서드**

    * 인터페이스의 메서드는 몸통(구현체)을 가질 수 없지만 디폴트 메서드를 사용하면 실제 구현된 형태의 메서드를 가질 수 있다.
    * 디폴트 메서드는 메서드명 가장 앞에 "default" 라고 표기해야 한다.
    * PgCo 인터페이스를 구현한 Kcp, Inicis 등의 실제 클래스는 printPG 메서드를 구현하지 않아도 사용할 수 있다.

    <pre class="language-java"><code class="lang-java">interface PgCo {
       String getPGName(); // 인터페이스의 메소드는 인터페이스에서 직접 구현하지 않는다.
       
       default void printPG() {
        System.out.printf("pg name is %s\n", getPGName());
    <strong>   }
    </strong>}
    </code></pre>
2.  **스태틱 메서드**

    * `인터페이스명.스태틱메서드명` 과 같이 사용하여 일반 클래스의 스태틱 메서드를 사용하는 것과 동일하게 사용할 수 있다.
    * 인터페이스에 정의한 상수는 `public static final`을 생략해도 자동으로\
      &#x20;`public static final`이 적용된다.&#x20;

    ```java
    interface PgCo {
       String getPGName(); // 인터페이스의 메소드는 인터페이스에서 직접 구현하지 않는다.
       
       default void printPG() {
        System.out.printf("pg name is %s\n", getPGName());
       }
       // public static final int PAY_COUNT = 2;와 동일하다.
       int PAY_COUNT = 2;  // 인터페이스 상수
       
       static int payCnt() {
           return PAY_COUNT * 30;
       }
    }
    ```

이렇게 스태틱 메서드를 추가하면 다음과 같이 사용할수 있다.

```java
PgCo.payCnt();
```

