# Arduino &#8594; Temperature
## 주요 사항
* 센서 형번: _MLX90614_ 
* 사용 라이브러리: _Adafruit_
* 현재 작성된 코드들은 캡슐화를 마친 상태이나, tempOnly.ino의 실제적인 검증은 이루어지지 않음. Temp.ino는 센서 정상 작동 확인 용도이며 실제적으로 작동함을 확인함. 다른 센서와의 융합을 위해서는 캡슐화된 상태인 tempOnly.ino를 활용하기 바람. 아두이노 IDE에서 컴파일은 제대로 되는 것 확인.

## 헤더 파일 설명
### public
* 혹시 내부 값을 건드릴까봐 **private**과 **public**으로 나눴다. 외부에서 사용을 원하는 경우 **public**에서 정의된 값을 사용하면 된다.
#### begin
센서 초기화. 온도 센서의 경우 설정 값이 따로 필요 없으므로 현재 상태, 온도 측정이 평균치를 낼 수 있을 만큼 이루어졌는지 등의 모든 값을 0으로 초기화 한다. 생성자 호출하면서 자동으로 이 과정은 이루어진다.
#### update
온도 값을 읽고 최신 5개의 체온 샘플을 통해서 현재 상태를 추정한다.
* newTemp: 측정한 온도를 임시로 저장.
* tempHistory: tempIndex를 바탕으로 해서, 가장 오래 된 측정 값 자리에 newTemp를 저장한다.
* tempCount: 초기에 5개 미만의 온도 샘플이 있는 경우는 모두 정상 상태로 간주하기 위해서 정의된 값이다. 
#### getTemperature
가장 최신의 온도 값을 반환한다. 이번 프로젝트 상에서는 디버깅용이다.
#### getStatus
현재 상태를 반환한다. 내부 계산 데이터에 쓰는 것을 방지하기 위해서 getStatus를 따로 정의했다.
#### isTempOkay
currentState가 0(정상)이면 1, 그 외에는 모두 0을 반환한다.
### private
#### getAverageTemp
tempHistory에 저장된 값을 바탕으로 평균 온도를 계산한다.
#### updateTemperatureState
getAverageTemp를 통해 얻은 평균 온도를 바탕으로 현재 상태를 업데이트한다.
* 0: 정상
* 1: 이상 상태 돌입
* 2: 이상 상태 지속 중

## 발견된 문제점 및 해결 사항
* 방사율 고려한 캘리브레이션 필요함
    -> 최근 10개의 값의 평균을 사용해서 제어하는 방안 생각 중, 알고리즘 자체는 동일. 데이터 처리만 요
    -> 2025.05.19.자로 최근 5개의 값을 사용해 상태 값을 반환하도록 설정함.
* 현재 모델은 체온과 주변온도 측정 가능. 체온 측정 시에 피부와 접촉 시 온도가 상당히 높게 측정되는 현상 발견됨.
    * 해결 방안
        1. 우선 체온 평균치 사용으로 해결. -> 앞에서 설명함.
        2. 신태하 조장이 3d 프린팅으로 외부 프레임으로 생성, 접촉 불가능하게 함.