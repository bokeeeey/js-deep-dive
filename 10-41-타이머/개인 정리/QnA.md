1. 디바운스와 쓰로틀링에 대해 설명해주세요
   두 테그닉 모두 함수가 너무 많이 실행되지 않도록 제어하는 테크닉입니다. 성능 최적화와 리소스 관리에 많이 사용됩니다.

- 디바운스
  - 지정된 딜레이 안에 n번의 호출이 발생하는 경우 마지막 1번만 실행된다.
  - 기본적으로 함수 호출이 지연되는 것이다.
  - 지정된 딜레이 전에 호출을 요청하면 이전 호출이 취소되는 것이다.
- 쓰로틀링
  - 함수가 호출되는 횟수와 상관없이 일정 간격으로 함수를 실행한다.
  - 딜레이를 주는 방식은 디바운스와 동일하지만 설정한 시간 내에 추가적인 함수 호출 요청을 무시한다.
  - 딜레이가 끝나면 함수가 실행된다.
