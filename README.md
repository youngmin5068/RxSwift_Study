# RxSwift_Study

## RxSwift 기본 구성 요소
> observable , Operator, Scheduler

### 1. Observable (관찰 가능한)
> Observable<T> 
  - T 형태의 데이터 snapshot을 '전달' 할 수 있는 일련의 이벤트를 비동기적으로 생성하는 기능
  - 하나 이상의 observers(관찰자)가 실시간으로 어떤 이벤트에 반응
  - 3가지 유형의 이벤트만 방출 ( next, error, completed )


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
