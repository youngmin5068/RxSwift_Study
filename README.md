# RxSwift_Study

## RxSwift 기본 구성 요소
> observable , Operator, Scheduler

### 1. Observable (관찰 가능한)
> Observable<T> 
  - T 형태의 데이터 snapshot을 '전달' 할 수 있는 일련의 이벤트를 비동기적으로 생성하는 기능
  - 하나 이상의 observers(관찰자)가 실시간으로 어떤 이벤트에 반응
  - 3가지 유형의 이벤트만 방출 ( next, error, completed )
  
#### Observable
  - 비동기적
  - marble diagram: 시간의 흐름에 따라서 값을 표시하는 방법
  - rxmarbles.com 참고
  
#### Observable 생명주기
  
  
--------
  
  

### 2. Operator (연산자)
> 연산 작용
  - filter, map 등과 같은 이벤트 방충
  
### 3. Scheduler
> Rx에서 dispatchqueue와 비슷  
> 개발자가 직접 생성할 일은 거의 없음.

--------
  
## RxSwift 설치
  
### CocoaPods
 
```swift
  # Podfile
use_frameworks!

target 'YOUR_TARGET_NAME' do
    pod 'RxSwift', '6.2.0'
    pod 'RxCocoa', '6.2.0'
end

# RxTest and RxBlocking make the most sense in the context of unit/integration tests
target 'YOUR_TESTING_TARGET' do
    pod 'RxBlocking', '6.2.0'
    pod 'RxTest', '6.2.0'
end
```

  
----------------
  
 ## Observable의 작은 범위 3가지 
  - Single
  - Maybe
  - Completable
  
 ### Single
  > .success, .error
  1. .success = next + success ( 파일 다운로드 , 사진 저장 등등 )  
  2. .error = next + error
  
 ### Maybe
  > .success, .completed, .error   
  
 ### Completable
  > .completed, .error

  
------------------
 ## Subject
  
 ### PublishSubject
  > 빈 상태로 시작하여 새로운 값만을 subscriber에 방출한다.
  
  <img width="769" alt="스크린샷 2021-12-31 오후 2 17 06" src="https://user-images.githubusercontent.com/61230321/147804783-20eea446-ba5f-44da-ba53-77d4bbc09df7.png">
  
  ```swift
  let publishSubject = PublishSubject<String>()

publishSubject.onNext("1. 안녕하세요")

let 구독자1 = publishSubject
    .subscribe(onNext:{
        print("첫번째 구독자 : \($0)")
    })

publishSubject.onNext("2. 들리세요?")
publishSubject.on(.next("3. 안 들리세요?"))

구독자1.dispose()

let 구독자2 = publishSubject
    .subscribe(onNext:{
        print("두번째 구독자 : \($0)")
    })
publishSubject.onNext("4. 여보세요")
publishSubject.onCompleted()

publishSubject.onNext("5. 끝났습니다.")

구독자2.dispose()


publishSubject
    .subscribe {
        print($0.element ?? $0)
    }.dispose()

publishSubject.onNext("6. 출력될까요")

  
  //결과
 // -----publishSubject-----
//첫번째 구독자 : 2. 들리세요?
//첫번째 구독자 : 3. 안 들리세요?
//두번째 구독자 : 4. 여보세요
//completed
  ```

  
  ### BehaviorSubject
  > 하나의 초기값을 가진 상태로 시작하여, 새로운 subscriber에게 초기값 또는 최신값을 방출한다.
  
<img width="769" alt="스크린샷 2021-12-31 오후 2 17 52" src="https://user-images.githubusercontent.com/61230321/147804822-dd7577ae-99dd-4c5b-b0ce-adca05b26dd5.png">
  
  ```swift
  enum SubjectError: Error {
    case error1
}

let behaviorSubject = BehaviorSubject<String>(value: "0. 초기값")

behaviorSubject.onNext("1. 첫번째 값")

behaviorSubject.subscribe{
    print("첫번째 구독 : ",$0.element ?? $0)
}.disposed(by: disposebag)

//behaviorSubject.onError(SubjectError.error1)

behaviorSubject.subscribe{
    print("두번째 구독 : ",$0.element ?? $0)
}.disposed(by: disposebag)

let value = try? behaviorSubject.value()
print(value)
  
  //결과
  //첫번째 구독 :  1. 첫번째 값
//두번째 구독 :  1. 첫번째 값
//Optional("1. 첫번째 값")
  ```

  
  ### ReplaySubject
  > 버퍼를 두고 초기화하며, 버퍼 사이즈 만큼의 값들을 유지하면서 새로운 subscriber에게 방출한다.
  <img width="769" alt="스크린샷 2021-12-31 오후 2 18 41" src="https://user-images.githubusercontent.com/61230321/147804849-c7acbc54-8cbb-45f4-945b-caa9d56064f6.png">

  ```swift
  let replaySubject = ReplaySubject<String>.create(bufferSize: 3)

replaySubject.onNext("1. 여러분")
replaySubject.onNext("2. 안녕하세요")
replaySubject.onNext("3. 저는")
replaySubject.onNext("4. 김영민입니다.")

replaySubject.subscribe{
    print("첫번째 구독자 : ", $0.element ?? $0)
}.disposed(by: disposebag)

replaySubject.subscribe{
    print("두번째 구독자 : ", $0.element ?? $0)
}.disposed(by: disposebag)

replaySubject.onNext("5. 반갑습니다.")
replaySubject.onNext("6. 이제 끝이에요")
  
  //결과
  //첫번째 구독자 :  2. 안녕하세요
//첫번째 구독자 :  3. 저는
//첫번째 구독자 :  4. 김영민입니다.
//두번째 구독자 :  2. 안녕하세요
//두번째 구독자 :  3. 저는
//두번째 구독자 :  4. 김영민입니다.
//첫번째 구독자 :  5. 반갑습니다.
//두번째 구독자 :  5. 반갑습니다.
//첫번째 구독자 :  6. 이제 끝이에요
//두번째 구독자 :  6. 이제 끝이에요
  ```
---------
  ## Filtering Operator
> ignore, elementAt, filter, skip,skipwhile,skipuntil, take, takewhile,takeuntil, enumerate, distinctUntilChanged
  
  1. ignore
  - onCompleted() 전까지 모든 onNext 무시. 
  2. elementAt
  - .element(at: int) 를 통해 int 번째 onNext에서 방출. 
  3. filter
  - 조건에 따른 필터링 가능 (예 : 2의 배수만 방출시키기). 
  4. skip
  - .skip(int) 를 통해 int값 만큼 skip 후에 방출. 
  5. skipwhile
  - .skip(while: 조건문) 조건문이 false 일 때부터 방출. 
  6. skipuntil
  - .skip(until: Observable) 은 현재 Observable이 다른 Observable이 나오기 전까지 skip. 
  7. take
  - .take(int) 를 통해 처음부터 int만큼 방출.  
  8. takewhile
  - .take(while: 조건문) 조건문이 false이기 전까지 방출.  
  9. takeuntil
  - .take(until: Observable) 은 현재  Observable이 다른 Observable이 나오기 전까지 방출.  
  > skip과 take는 반대 개념
  
  10. enumerate
  - .enumerate() 를 통해 방출 시, (index: ,element: ) 형식으로 방출.  
  11. distinctUntilChanged
  - 연달아 중복되는 값은 방출하지 않고, 처음 값만 방출. 연달아 중복 아닌 경우 다시 방출.  
  - (ex: "나는","니는","영민","영민","이다","영민" -> "나는","영민","이다","영민")
  
  
  
