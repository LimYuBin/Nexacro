# Difingo
* Difingo : Eclipse plugin + Web application library
* 서비스 기본 단위 : AutomationModel (외부 연동)
* 모델 생성 시 xam 파일과 java 파일 2개로 구성됨
* AutomationLogic.java : java Coding을 수행할 수 있는 영역
* BaseAutomationLogic.java : code generation 영역으로 GUI 작업 시 자동 생성되는 영역

# X-UP BUILDER MODEL 개발 예제
1. 단일 유형
    * 간단히 DB query, SAP RFC 등을 호출할 때, 사용하는 유형
    * drag and drop 후에 호출 대상을 설정함으로써 개발을 수행하는 유형

2. 복합 유형
    * 하나 이상의 호출이 있는 경우

3. 분기(if 구문)
    * if connection / elseIf connection

4. 루프(Loop)
    * DS Row Loop function

5. UserMethod
    * visual code 보다 더 간단
    * drag and drop으로 개발한 내용을 연동

6. 사용자 정의 ErrorCode & Error MSG 처리
    * ErrorCode, ErrorMSG 파라미터를 출력 type으로 설정
    * UserMethod에서 ErrorCode값과 ErrorMSG 값을 설정    

7. Http Session(Session Object)
    * Session Object의 값을 가져오거나 설정하는 방법
        * gerUserSession() : user sessio n 정보 가져옴
        * getAttribute, setAttribute 메소드 사용해서 session  값 관리
        * invalidate 메소드로 session 객체 파기