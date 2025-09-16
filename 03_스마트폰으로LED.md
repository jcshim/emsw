# 📘 실습 안내서

## 스마트폰 버튼으로 Wi-Fi를 통해 서버를 거쳐 시리얼로 아두이노 LED 제어하기

---

## 1. 전체 개요

이 실습에서는 \*\*스마트폰 앱(App Inventor)\*\*에서 버튼을 누르면,
➡ Wi-Fi를 통해 \*\*서버(Processing)\*\*로 신호가 전달되고
➡ 서버가 **시리얼 통신**을 통해 **아두이노**에 명령을 보내서
➡ LED가 켜졌다 꺼지는 구조를 직접 구현합니다.

👉 핵심 개념:
**“스마트폰 → 서버 → 아두이노”** 라는 IoT 기본 구조 이해하기!

---

## 2. 준비물

* 스마트폰 (App Inventor 실행 가능)
* PC (Processing 설치, 아두이노 IDE 설치)
* 아두이노 보드 (UNO, Mega, Nano 등)
* USB 케이블
* LED(내장 13번 핀 LED 사용)

---

## 3. 흐름도

```
[스마트폰 버튼] → [Wi-Fi 신호] → [서버(Processing)] → [시리얼 통신] → [아두이노] → [LED ON/OFF]
```

---

## 4. 스마트폰 (App Inventor) 설정

1. **버튼 만들기**

   * 버튼 2개 생성:

     * `버튼 ON` → 문자 `'1'` 전송
     * `버튼 OFF` → 문자 `'0'` 전송

2. **서버 주소 설정**

   * PC에서 `cmd → ipconfig` 실행
   * IPv4 주소 확인 (예: `192.168.0.15`)
   * App Inventor의 Web 컴포넌트 주소 입력:

     ```
     http://192.168.0.15:12345
     ```

3. **App Inventor 동작 예시**

   * ON 버튼 클릭 → `'1'` 전송
   * OFF 버튼 클릭 → `'0'` 전송

👉 **Tip**: 학생들에게 **버튼 → 신호 → 서버로 전달** 구조를 그림으로 보여주면 직관적으로 이해됩니다.

---

## 5. 서버 (Processing) 프로그램

서버는 **중계기** 역할을 합니다.
스마트폰에서 받은 `'0'`/`'1'` 신호를 아두이노에 시리얼로 전달합니다.

```java
import processing.serial.*;
import processing.net.*;

Serial p;    // 아두이노 시리얼
Server s;    // 서버
Client c;

void setup(){
  // 아두이노가 연결된 포트 (예: COM6, macOS/Linux는 "/dev/ttyUSB0")
  p = new Serial(this, "COM6", 9600);
  // 서버 포트 번호: 12345
  s = new Server(this, 12345);
}

void draw(){
  c = s.available();
  if(c != null){
    String g = c.readString();
    // 마지막 문자만 추출 (0 또는 1)
    g = g.substring(g.length()-1);
    println("받은 신호: " + g);
    p.write(g);
  }
}
```

👉 **실행 방법**

1. Processing 코드 실행 → 서버가 열림
2. 스마트폰에서 버튼 클릭 → 서버 창에 `'0'` 또는 `'1'` 표시됨
3. 서버가 아두이노로 전달

---

## 6. 아두이노 프로그램

아두이노는 서버에서 받은 `'0'` 또는 `'1'` 신호에 따라 LED를 제어합니다.

```cpp
void setup() {
  Serial.begin(9600);
  pinMode(13, OUTPUT);   // 내장 LED
}

void loop() {
  if(Serial.available() > 0){
    char a = Serial.read();
    if(a == '0'){
      digitalWrite(13, LOW);   // LED 끄기
    }
    if(a == '1'){
      digitalWrite(13, HIGH);  // LED 켜기
    }
  }
}
```

---

## 7. 실습 과정 요약

1. **스마트폰(App Inventor)**: 버튼 클릭 시 `"0"` 또는 `"1"` 전송
2. **서버(Processing)**: 문자열에서 마지막 문자 추출 후 아두이노로 전달
3. **아두이노**: 신호 해석 →

   * `'1'` → LED 켜짐
   * `'0'` → LED 꺼짐

---

## 8. 실습 포인트

* 학생들이 직접 버튼을 누르면 LED가 반응하므로 성취감을 얻을 수 있음
* IoT 기본 구조(스마트폰–서버–아두이노)를 직관적으로 체험 가능
* 확장:

  * LED 대신 모터 제어
  * 센서 데이터 스마트폰으로 전송
  * 릴레이 제어로 가전제품 ON/OFF

---

## 9. 문제 해결 (FAQ)

* **LED가 안 켜져요**
  → 아두이노 포트(COM 번호) 확인
* **App Inventor에서 연결이 안 돼요**
  → 스마트폰과 PC가 같은 Wi-Fi에 연결되어 있는지 확인
* **서버에서 신호가 안 보여요**
  → Processing 실행 중인지 확인
