# 1. 넥사크로 개요
* 넥사크로 : 개발자 + 디자이너 => 통합개발도구
* 확장자 : xdfl(XML 파일)
* 실행 방법 : javascript(.js)로 Generate 하여 Deploy
* Web : Render Engine, Script Engine
* App : Render Engine, V8 Script Engine
* 한번 Generate된 소스는 원본소스로 되돌릴 수 없음
---
* Environment -> Variables
    * 값을 셋팅하고 삭제해주는 스크립트 작성해주어야 함 (Local Storage에 저장되기 때문)
    * 평문 : 암호화해서 넣어주어야 함
* TypeDefinition -> Objects
    * 넥사크로 플랫폼 Engine Library
    * 기능 추가 또는 삭제
* TypeDefinition -> Services
    * Resource Service : 디자이너 영역
    * User Service : 개발자 영역
* Application Information
    * Application의 global 영역
    * Datasets : 2차원 형태의 데이터
    * Variables : 어플리케이션 실행됐다가 종료되면 메모리에서 삭제됨
    * Application_Desktop : 결과물 소스 파일로 자동 생성


# 2. 개발환경 설정과 HELLO
* javascript 문법과 동일
* 다른 점
    * SCOPE 정의 : alert 창 띄울 때도 this.alert("Hello");
    * 변수 선언 : this.변수명
    * 컴포넌트 설정 : this.컴포넌트.set_속성명
    * 컴포넌트 호출 : this.컴포넌트.속성명
---
* function의 파라미터 2개 
    * obj : this.Button
    * e : Event
---
* event 등록
    * 더블클릭 -> Script
    * Properties 이용


# 3. 컴포넌트
* 단일 컴포넌트 : Static / Button / Edit / MaskEdit / TextArea / Calender / Checkbox / Spin / ImageViewer / ProgressBar
* 목록형 컴포넌트 : Combo / Listbox / Radio / Grid / Menu / PopupMenu
* 컨테이너 컴포넌트 : Div / Tab / PopupDiv
---
* Generate 이후 실행 :  Generate -> application
* Skip 하지 않고 Generate : Regenerate -> Application
* Launch : 어플리케이션 실행
* 컴포넌트 설정 후 붙여넣기 : ctrl + shift + V
* 컴포넌트 경로명 찾아오기 : 오른쪽 마우스 -> COPY_ID
* 상단 step : 페이지(form) 개수
* Mast Edit(++ 아이콘) / Edit( | 아이콘)
    * 타입 변경 시 type, format 속성 모두 지정
    * {###} : * 처리
    * password -> true (Edit에서 사용)
* url 속성 : form 미리 만들어놓은 것 가져올 수 있음
    

# 4. 데이터 바인드
* 단일 컴포넌트 바인딩 : 해당 컬럼을 컴포넌트에 끌어다 놓기
* 목록형 컴포넌트 바인딩
    * 콤보박스 : 드래그(popup창 2번째) + innerdataset 설정(popup창 1번째)
    * radio data 설정 : innerdataset 속성에서
    * radio 정렬 : columncount 속성에서
    * radio 방향 설정 : direction 속성에서
* CSS 적용 : cssclass 속성 이용
* 정렬 : Distribute Vertically by Specified Value -> -1
* 휴지통 아이콘(dataset) : 데이터셋 설정


# 5. 그리드
* 그리드 편집 : 그리드 더블클릭
* 마우스 오른쪽 -> INSERT :  앞쪽에 컬럼 추가
* 마우스 오른쪽 -> ADD : 맨 뒤쪽에 컬럼 추가
---
* text : 컬럼명 편집
* bind : 데이터 바인딩
* expr : cell에 들어갈 데이터를 정의해주는 수식 작성
    <pre><code>//예약어 사용 또는 사용자 함수 script에 작성해놓기
    EMPL_ID + FULL_NAME
    currow + 1
    dataset.getRowCount() + "건" //dataset 대신에 comp.parent.ds_emp 사용해도 됨(SCOPE 정의)
    dataset.getSum("SALARY")
    GENDER=="M" ? "남자" : GENDER=="여" ? "여자" : "기타" //삼항연산
    nexacro.round(12.8745,2) //Math.round 처럼 복잡하지 않음</code></pre>
---
* zisplaytype : 사용자에게 보여주는 상태
* edittype : 편집모드로 변경되었을 때 상태
    * 위 2가지 mask로 바꿨을 때 -> maskeditformat 정의해주어야 함
    * 위 2가지 combo로 바꿨을 때 -> combocodecol, combodatacol 정의해주어야 함


# 데이터 통신
<pre><code>//서비스 연동 -> Code Snippet에 저장해놓고 사용
//Retrieve Button
this.btn_retrieve_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
{
	this.transaction("strSelect", //서비스 id
				    "SvcURL::select_emp.jsp?arg=1&arg2=2", //서비스 url, SvcURL은 Services에 있는 경로
					"", //저장
					"ds_emp=out_emp", //조회 client ds = server ds
					"arg=1 arg2=" + nexacro.wrapQuote("2 3"), //Post 방식 : 데이터에 공백이 있을 경우 nexacro.wrapQuote() 사용
					"fn_call"); //문장의 끝 function(callback 함수)						
};

this.fn_call = function(svcid, ecd, emsg) //ecd : 에러코드, emsg : 에러메세지
{
	if(ecd >= 0)
	{
		if(svcid == "strSelect")
		{
			this.alert(this.ds_emp.getRowCount() + "건 조회");
		} else if(svcid == "strSave")
		{
			this.alert("저장성공");
		}
	} else {
		alert("Error " + ecd + ":" + emsg);
	}
}


//Add Button
this.btn_add_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
{
	this.ds_emp.addRow();
	this.ds_emp.setColumn(This.ds_emp.rowposition,"GENDER","W"); //GENDER column의 default값이 W
};

//Delete Button
this.btn_del_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
{
	this.ds_emp.deleteRow(this.ds_emp.rowposition);
};

//Save Button
this.btn_save_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
{
	this.transaction("strSave", //서비스 id
				    "SvcURL::save_emp.jsp?arg=1&arg2=2", //서비스 url, SvcURL은 Services에 있는 경로
					"in_emp=ds_emp:U", //저장 server ds = client ds, :U는 저장할 때 무조건 붙이기
					"ds_emp=out_emp", //조회 client ds = server ds
					"arg=1 arg2=" + nexacro.wrapQuote("2 3"), //Post 방식 : 데이터에 공백이 있을 경우 nexacro.wrapQuote() 사용
					"fn_call"); //문장의 끝 function(callback 함수)		
};

//onload
this.Form_onload = function(obj:nexacro.Form,e:nexacro.LoadEventInfo)
{
	this.transaction("strCode", //서비스 id
				    "SvcURL::select_code.jsp?arg=1&arg2=2", //서비스 url, SvcURL은 Services에 있는 경로
					"", //저장
					"ds_dept=out_dept ds_pos=out_pos", //조회 client ds = server ds
					"arg=1 arg2=" + nexacro.wrapQuote("2 3"), //Post 방식 : 데이터에 공백이 있을 경우 nexacro.wrapQuote() 사용
					"fn_call"); //문장의 끝 function(callback 함수)		
};
</code></pre>

* 컴포넌트 Resize
    * 컴포넌트 1개 %로 설정(Resize)해놓고 다른 컴포넌트와 연결하면 따라감
    * Fit to Content by both(width, height) 화면 크기 조정될 때, 컴포넌트 크기 자동 조정
    * Position -> fittocontent : 글의 양에 따라 컴포넌트 사이즈 조정
    * onbindingvaluechanged : fittocontent를 거는 컴포넌트가 바인딩이 되어있을 때, 이벤트 추가하고 this.resetScroll(); 해주어야 함