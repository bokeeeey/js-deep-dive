- 질문 1: 데이터 프로퍼티와 접근자 프로퍼티의 차이는 무엇인가요?
- 답변 1: 
  데이터 프로퍼티: 데이터 자체를 제공받을 수 있는 프로퍼티로 , 내부 슬롯 [[Value]], [[Writable]], [[Enumerable]], [[Configurable]]을 통해 구현됩니다.
  접근자 프로퍼티: 데이터에 접근할 수 있는, 그러니까, 세터, 게터 함수처럼, 데이터 접근 및 변경을 담당하는 프로퍼티라고 정리할 수 있습니다. 접근자 프로퍼티는 [[Get]], [[Set]], [[Enumerable]], [[Configurable]]로 구성됩니다.
- 답변 2:
  키워드 중심 내용 정리
    데이터 프로퍼티: 키와 값으로 구성된 일반적인 프로퍼티로, 값을 포함한다.
    접근자 프로퍼티: 자체적으로 값을 가지지 않는다.
   두 개념의 가장 큰 차이점은 자체적으로 값을 포함하는지 아닌지 여부입니다. 데이터 프로퍼티는 키와 값으로 이루어진 일반적인 프로퍼티로 값을 포함하고 있습니다. 이와 다르게, 접근자 프로퍼티는 값을 읽거나 접근할 때 사용하는 접근자 함수로 이루어져 있고, 자체적으로 값을 가지고 있지 않습니다.

- 질문 2: 프로퍼티가 생성될 때 데이터 프로퍼티는 어떤 어트리뷰트를 갖고있고 어떤 값으로 초기화되는지 이야기해주세요
- 답변: 프로퍼티가 생성될 때 [[Value]]의 값은 프로퍼티 값으로 초기화되며, [[Writable]], [[Enumerable]], [[Configurable]]의 값은 true로 초기화된다.
