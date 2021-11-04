# 1. 개발 시 유의사항
* 넥사크로 컴포넌트 : Dataset / Grid / Container Component / Form
* 비동기로 실행
* 동일출처정책(Cross domain) : 웹 브라우저 실행시 화면 페이지와 데이터 통신시 호출하는 서버 페이지 도메인이 일치해야 함
* 서비스 경로 사용시 Full경로가 아닌 PrefixID 사용
    * ex) SvcURL::select_emp.jsp
* CacheLevel
    * none : 사용하지 않음
    * session : 어플리케이션을 종료하지 않고 화면만 닫았을 때, cache에 있는 것을 올려줌
    * dynamic : 화면을 열고 닫을 때마다 내용이 바뀌었을 때 서버에서 데이터 가져와서 보여주고, 바뀌지 않았을 때 cache에 있는 것을 올려줌 (default)
    * static : 현재 사용 x
* transaction 하나의 호출로 N개의 Dataset 가져오도록 코딩
    * 공백으로 구분하면 됨
* 서비스 호출 시 데이터가 없는 경우에도 반드시 Layout 필요
    * 서버에서 받아온 Dataset으로 치환됨
    * useclientlayout : Dataset 속성, true로 하면 값만 가져오고 레이아웃은 만든 것 그대로
* Dataset의 Column을 이용한 수식 계산 시 Type을 사용
    * 그룹별 소계
    * 그리드 합계 : getSum("SALARY") -> getSum("nexacro.toNumber(SALARY)")
    * Sort 기능 : Column Type이 기준, 형 변환 불가
* onload, onrowsetchanged, onworposchanged 등 Event 객체에 e.reason이 있는 경우 반드시 분기 처리

# 2. Dataset
* Property
    * enableevent, keystring, rowcount, rowposition, useclientlayout
* Method
    * addColumn, addRow, insertRow, deleteRow, getColumn, setColumn, getOrgColumn, getDeletedColumn, filter, findRow, getAvg, getCaseAvg, clearData, reset, copyData, copyRow
* Event
    * onload, cancolumnchange, oncolumnchanged, canrowposchange, onrowposchanged

* 데이터셋의 Column 개수, Row 개수, 컬럼명 구하기
    <pre><code>this.btn_Exe1_1_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        var nColCount = this.Dataset1.getColCount(); // this.Dataset1.colcount;
        var nRowCount = this.Dataset1.getRowCount(); // this.Dataset1.rowcount;
        trace("Col Count=" + nColCount + " : Row Count=" + nRowCount);	
        
        this.txtRtn1.set_value("Col Count=" + nColCount + " : Row Count=" + nRowCount);
        
        for(var i=0; i < nColCount; i++){
            //var sColID = this.Dataset1.getColID(i);
            //trace("Col ID=" + sColID);
            var objCol = this.Dataset1.getColumnInfo(i); 
            trace(objCol.id + " : " + objCol.type + " : " + objCol.size + ":" + objCol.size + ":" +objCol.prop);
        }

    };
    </code></pre>

* 컬럼 추가
    <pre><code>// Exe 1-2
    this.btn_Exe1_2_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        this.Dataset1.addColumn("COL_CHK", "STRING", 1);
    };
    </code></pre>

* 단일조건 검색
    <pre><code>this.btn_Exe2_1_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        var nRow = this.Dataset1.findRow("EMPL_ID", "KR120"); //index값을 얻는 것
        var sVal = this.Dataset1.getColumn(nRow, "FULL_NAME"); //index 바탕으로 이름 가져옴

        trace(sVal);	
        this.txtRtn1.set_value(sVal);	
    };
    </code></pre>

* 복합조건 검색 (단, 첫번째 레코드만 return)
    <pre><code>this.btn_Exe2_2_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        var nRow = this.Dataset1.findRowExpr("DEPT_CODE == 'K10' && SALARY <= 5000");
        var sVal = this.Dataset1.getColumn(nRow, "FULL_NAME");
        trace(sVal);	
        this.txtRtn1.set_value(sVal);	
    };
    </code></pre>

* 검색한 레코드 목록으로 return
    <pre><code>this.btn_Exe2_3_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        var sText = "";
        var arrRow = this.Dataset1.extractRows("DEPT_CODE=='K10'");
        for(var i=0; i < arrRow.length; i++){
            var sValue = this.Dataset1.getColumn(arrRow[i], "FULL_NAME");
            trace(sValue);
            sText += sValue + "\n";
        }
        
        this.txtRtn1.set_value(sText);
    };
    </code></pre>

* 조건 검색 + 평균 계산
    <pre><code>this.btn_Exe3_1_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        var nAvgM  = this.Dataset1.getCaseAvg("GENDER=='M'", "SALARY");
        var nAvgW1 = this.Dataset1.getCaseAvg("GENDER=='W'", "SALARY"); 
        var nAvgW2 = this.Dataset1.getCaseAvg("GENDER=='W'", "SALARY",0, -1, false); //false일 때 null, undefined, ""(empty string)도 계산에 포함
        trace("Man Avg=" + Math.round(nAvgM, 2) + " : Woman Avg=" + nAvgW1 + " : " + nAvgW2);
        this.txtRtn1.set_value("Man Avg=" + Math.round(nAvgM, 2) + " : Woman Avg=" + nAvgW1 + " : " + nAvgW2);
    };
    </code></pre>

* 컬럼끼리 더해서 평균 계산
    <pre><code>this.btn_Exe3_2_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        var nAvg = this.Dataset1.getAvg("SALARY+BONUS");
        trace("Salary+Bonus AVG=" + nAvg);
        this.txtRtn1.set_value("Salary+Bonus AVG=" + nAvg);
    };
    </code></pre>

* 내림차순, 오름차순 정렬
    <pre><code>this.btn_Exe3_3_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        this.Dataset1.set_keystring("S:-HIRE_DATE"); //내림차순, S는 Sorting, G는 Grouping
        //this.Dataset1.set_keystring("S:+HIRE_DATE"); //오름차순
    };
    </code></pre>

* 조건 + 필터링
    <pre><code>this.btn_Exe3_4_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        this.Dataset1.filter("GENDER == 'M' && MARRIED == '0'");	
    };
    </code></pre>

* 특정 문자열 포함한 경우 필터링
    <pre><code>this.btn_Exe3_Filter_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        // like filter --> String.indexOf()
        var sText = "e"; //시용자 입력 : "e" 대신 this.Edit0.value 형식
        this.Dataset1.filter("FULL_NAME.toUpperCase().indexOf('" + sText.toUpperCase() + "') >= 0");
    };
    </code></pre>

* 필터링 하기 전과 후의 레코드 개수 비교
    <pre><code>this.btn_Exe3_5_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        var nCnt   = this.Dataset1.getRowCount(); //필터링 후 레코드 개수
        var nCntNF = this.Dataset1.getRowCountNF(); //필터링 전 레코드 개수
        trace(nCnt + " : " + nCntNF);
        this.txtRtn1.set_value(nCnt + " : " + nCntNF);
    };
    </code></pre>

* Row 삽입 
    <pre><code>this.btn_Exe4_1_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        var nRow  = this.Dataset4.insertRow(0); //0: 존재하지 않는 행의 상태
        var nType = this.Dataset4.getRowType(nRow);
        trace("Insert Rowtype: " + nType);
        this.txtRtn2.set_value("Insert Rowtype: " + nType);
    };
    </code></pre>

* 특정 Row의 값 변경, 레코드 상태 값 확인
    <pre><code>this.btn_Exe4_2_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        this.Dataset4.setColumn(1, "FULL_NAME", "Nexacro");
        var nType = this.Dataset4.getRowType(1);
        trace("Update Rowtype: " + nType);
        this.txtRtn2.set_value("Update Rowtype: " + nType);
    };
    </code></pre>

* 변경 전과 후의 데이터 확인
    <pre><code>this.btn_Exe4_3_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        var sCurData = this.Dataset4.getColumn(1, "FULL_NAME");
        var sOrgData = this.Dataset4.getOrgColumn(1, "FULL_NAME");	
        trace("Cur Data=" + sCurData + " : Org Data=" + sOrgData);	   
        this.txtRtn2.set_value("Cur Data=" + sCurData + " : Org Data=" + sOrgData);
    };
    </code></pre>

* Row 여러개 삭제
    <pre><code>this.btn_Exe4_4_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        var arrRow = [3,4,5]; 
        this.Dataset4.deleteMultiRows(arrRow);\
    };
    </code></pre>

* 삭제한 레코드 개수와 삭제된 첫번째 데이터
    <pre><code>this.btn_Exe4_5_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        var nDelCnt  = this.Dataset4.getDeletedRowCount();	
        var sDelData = this.Dataset4.getDeletedColumn(0, "FULL_NAME"); //삭제할 때는 맨 마지막 Row부터 삭제
        trace("Del Count=" + nDelCnt + " Del Data=" + sDelData);	
        this.txtRtn2.set_value("Del Count=" + nDelCnt + " Del Data=" + sDelData);		
    };
    </code></pre>

* 데이터 변경여부 체크
    <pre><code>this.btn_Exe4_Check_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        this.fn_checkdata(this.Dataset4);
    };
    </code></pre>
    <pre><code>this.fn_checkdata = function(objDs)
    {
        if(objDs.getDeletedRowCount() > 0) {
            this.alert("삭제");
            //return true;
        }
        for(var i=0; i < objDs.rowcount; i++)
        {
            var nRowType = objDs.getRowType(i);
            if(nRowType == 2 || nRowType == 4) {
                this.alert("신규/변경");
                //return true;
            }
        }
    };
    </code></pre>

* 창 닫기 전에 변경 데이터 확인 메세지박스
    <pre><code>this.Exe_Dataset_onbeforeclose = function(obj:nexacro.Form,e:nexacro.CloseEventInfo)
    {
        if(this.fn_checkdata(this.Dataset4)) {
            return "변경된 데이터가 존재합니다. 닫으시겠습니까?";
        }
    };
    </code></pre>

* Dataset 복사
    <pre><code>this.btn_Exe5_1_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        this.Dataset5.copyData(this.Dataset4);
        this.Grid5.createFormat();
    };
    </code></pre>

* Dataset에 입력, 수정한 정보도 모두 복사
    <pre><code>this.btn_Exe5_2_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        this.Dataset5.assign(this.Dataset4);
        this.Grid5.createFormat();
    };
    </code></pre>

* 조건을 만족하는 Row만 복사
    <pre><code>this.btn_Exe5_3_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        var nFromRow = this.Dataset4.findRow("EMPL_ID", "KR040");
        var nToRow   = this.Dataset5.addRow();
        this.Dataset5.copyRow(nToRow, this.Dataset4, nFromRow);	
    };
    </code></pre>

* 조건을 만족하는 Row 중 특정 컬럼만 복사
    <pre><code>this.btn_Exe5_4_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        var nFromRow = this.Dataset4.findRow("EMPL_ID", "KR210");
        var nToRow   = this.Dataset5.addRow();
        var sCol = "EMPL_ID=EMPL_ID, FULL_NAME=FULL_NAME";	
        this.Dataset5.copyRow(nToRow, this.Dataset4, nFromRow, sCol);
    };
    </code></pre>

* 이벤트 잠시 멈추기
    <pre><code>this.btn_Exe6_5_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        this.Dataset6.set_enableevent(false); //이벤트 잠시 동작 멈춤 -> tracelog에 아무것도 찍히지 않음
        for(var i=0; i < 10; i++)
        {
            this.Dataset6.setColumn(i, "FULL_NAME", "Modify2");
        }
        this.Dataset6.set_enableevent(true); //이벤트 다시 시작
    };
    </code></pre>

# 3. Grid
* this.그리드.getCellCount("body") : body 밴드 Cell의 개수
* this.그리드.getFormatColCount() : column의 개수

* 그리드의 head의 컬럼을 클릭하면 오름차순/내림차순으로 정렬 
    <pre><code>this.Grid1_onheadclick = function(obj:nexacro.Grid,e:nexacro.GridClickEventInfo)
    {
        var sColId = obj.getCellProperty("body", e.cell, "text").split(":");
        var objDs = obj.getBindDataset(); //obj에 바인드되어있는 데이터셋을 가져옴
        trace(sColId + "----------" + sColId[1]); //선택한 Column의 text 정보 ---------- 선택한 Column의 ID 정보
        objDs.set_keystring("S:+" +sColId[1]); //오름차순으로 Sort
    };
    </code></pre>

* 그리드 head의 체크박스를 누르면 전체선택/전체해제
    <pre><code>this.Grid2_onheadclick = function(obj:nexacro.Grid,e:nexacro.GridClickEventInfo)
    {
        if(e.cell == 0){
            var nValue = this.Grid2.getCellText(-1,0) //-1: head
            nValue = (nValue == "1" ? "0" : "1");
            this.Dataset2.set_enableevent(false);
            for(var i=0; i < this.Dataset2.rowcount; i++) {
            this.Dataset2.setColumn(i, "COL_CHK", nValue);
            }
            this.Dataset2.set_enableevent(true);
            //head
            this.Grid2.setCellProperty("Head",0,"text",nValue);
            }
    };
    </code></pre>

* 그리드 컬럼 이동, 컬럼 사이즈 변경 -> event의 cellmovingtype, cellsizingtype
    <pre><code>this.btn_Exe3_1_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        this.Grid3.set_cellmov
        ingtype("col");
        this.Grid3.set_cellsizingtype("col");
    };
    </code></pre>

* 그리드 3번째 Column과 Row까지 고정 (스크롤할 때) -> 고정할 마지막 컬럼까지 드래그하고 band를 left로
    <pre><code>this.btn_Exe3_2_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
  {
      //Column Fix
      this.Grid3.setFormatColProperty(2, "band", "left");

        //Row Fix
        this.Grid3.setFixedRow(2);
    };
    </code></pre>

* 그리드 컬럼 사이즈 지정, 컬럼 숨기기 
    <pre><code>this.btn_Exe3_4_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        this.Grid3.setFormatColProperty(2, "size", 100);
        this.Grid3.setFormatColProperty(5, "size", 0);
    };
    </code></pre>

* 그리드에 depth가 있을 때 (폴더 계층)
    * 그리드는 level 컬럼을 가지고 있어야 함
    * displaytype : treeitemcontrol
    * edittype : tree
    * treelevel : level
    * 전계층 보고싶을 때
        *  treeinitstatus : expand, all
    * 체크박스 안보고 싶을 때
        * treeusecheckbox를 false로 설정

* CORP, DEPT 별 소계 
    <pre><code>this.btn_Exe5_1_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        this.Dataset5.set_keystring("G:-CORP,+DEPT");
        var sExpr = "expr:(dataset.getRowLevel(currow) == 2 ? CORP+' Sum' : CORP)";
        this.Grid5.setCellProperty("body", 0, "text", sExpr);
        sExpr = "expr:(dataset.getRowLevel(currow) == 0 ? DEPT : (dataset.getRowLevel(currow) == 1 ? DEPT + ' Sum' : ''))";
        this.Grid5.setCellProperty("body", 1, "text", sExpr);
    };
    </code></pre>

* SUMMARY Row 추가하고, SALARY 총 합계
    <pre><code>this.btn_Exe5_2_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        this.Grid5.appendContentsRow("summary");
        this.Grid5.set("summary", 4, "text", this.Dataset5.getSum("SALARY"));
    };
    </code></pre>

* 그리드 소계 상단에 표시
    * reversesubsum : true
    * summarytype: top

* 그리드 사이즈에 맞춤
    * autofittype : col

* Row가 아니라 Cell로 선택되도록 함
    * selecttype : cell

* edit 컴포넌트에 작성한 것 바로 반영하도록 함
    <pre><code>this.Edit00_oninput = function(obj:nexacro.Edit,e:nexacro.InputEventInfo)
    {
        obj.updateToDataset();
    };
    </code></pre>

# 4. Container Component
* 팝업창 특정 위치에 띄우기
    <pre><code>this.btn_Exe1_1_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
        {
            var nX = 0;	
            var nY = obj.height;
            this.PopupDiv1.trackPopupByComponent(this.btn_Exe1_1, nX, nY);	
        };
    </code></pre>

* Contents로 구성된 PopupDiv1을 버튼 하단에 오픈
    <pre><code>this.PopupDiv1_btn_Exe1_Close_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        var sDept = this.Dataset1.getColumn(this.Dataset1.rowposition, "DEPT_NAME");
        var sName = this.Dataset1.getColumn(this.Dataset1.rowposition, "FULL_NAME");
        var sID   = this.Dataset1.getColumn(this.Dataset1.rowposition, "EMPL_ID");
        
        this.edt_dept.set_value(sDept);
        this.edt_name.set_value(sName);
        this.edt_id.set_value(sID);	
        this.PopupDiv1.closePopup();	
    };
    </code></pre>

* Linked Form으로 구성된 PopupDiv2를 버튼 하단에 오픈 -> Link된 Form에서 값을 Array로 넘겨줌
    <pre><code>//Link된 Form에서 값을 Array로 넘겨줌
    this.btn_Exe2_Close_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        var arrRtn = [];
        arrRtn[0] = this.Dataset2.getColumn(this.Dataset2.rowposition, "DEPT_NAME");
        arrRtn[1] = this.Dataset2.getColumn(this.Dataset2.rowposition, "FULL_NAME");
        arrRtn[2] = this.Dataset2.getColumn(this.Dataset2.rowposition, "EMPL_ID");
        this.parent.parent.PopupDiv2.closePopup(arrRtn);
    };
    </code></pre>
    <pre><code>this.btn_Exe2_1_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        var nX = parseInt(0);	
        var nY = parseInt(obj.height);
        this.PopupDiv2.trackPopupByComponent(obj
                                        , nX
                                        , nY
                                        , null
                                        , null
                                        , "fn_pDivCallback"); //callback 함수	
    };
    </code></pre>

* 달력 숨김
    * popuptype : none(숨김) | normal(펼침)

* 달력 값 셋팅
    <pre><code>//1. dropdown
    this.cal_from_ondropdown = function(obj:nexacro.Calendar,e:nexacro.EventInfo)
    {
        this.PopupDiv3.form.cal_from.set_value(this.cal_from.value);
        this.PopupDiv3.form.cal_to.set_value(this.cal_to.value);
        this.PopupDiv3.trackPopupByComponent(this.cal_from, 0, obj.height);
    };
    </code></pre>
    <pre><code>//2. confirm button
    this.PopupDiv3_btn_confirm_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        var sFrom = this.PopupDiv3.form.cal_from.value;
        var sTo = this.PopupDiv3.form.cal_to.value;
        
        this.cal_from.set_value(sFrom);
        this.cal_to.set_value(sTo);
        this.PopupDiv3.closePopup();
    };
    </code></pre>

* 달력 From값이 To값보다 클 때
    <pre><code>this.PopupDiv3_oncloseup = function(obj:nexacro.PopupDiv,e:nexacro.EventInfo)
        {
            if(this.cal_from.value > this.cal_to.value){
                this.alert("End Date Should be later than From Date");
                this.cal_to.set_value("");
            }
        };
    </code></pre>

# 4. Form
* 모달 팝업창 오픈
    <pre><code>this.btn_Exe3_1_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        var objChildFrame = new ChildFrame();
        objChildFrame.init("chf_popup1"
                        , 0
                        , 0
                        , 400
                        , 300
                        , null
                        , null
                        , "Exe::Exe_Form_Popup.xfdl");

        objChildFrame.set_openalign("center middle");
        objChildFrame.set_overlaycolor("RGBA(196,196,196,0.5)")
        objChildFrame.set_dragmovetype("all");
        //	objChildFrame.set_resizable(false);
        //	objChildFrame.set_showstatusbar(false);
            
        var objParam = { param1:this.Edit3_1.value
                    , param2:this.Edit3_2.value
                    , param3:this.Dataset3 };
                    
        objChildFrame.showModal( this.getOwnerFrame() //모달 방법
                            , objParam
                            , this
                            , "fn_popupCallback");	
    };
    </code></pre>
    <pre><code>
    this.fn_popupCallback = function(strPopupID, strReturn)
        {
            if(strReturn == undefined){
                return;
            }

            if(strPopupID == "chf_popup1"){
                trace("Return Value: " + strReturn);
                var arrRtn = strReturn.split(":");
                this.Edit3_1.set_value(arrRtn[0]);
                this.Edit3_2.set_value(arrRtn[1]);
            }
        };
    </code></pre>

* 모달리스 팝업창 오픈
    <pre><code>this.btn_Exe3_3_onclick = function(obj:nexacro.Button,e:nexacro.ClickEventInfo)
    {
        var nW = 400;
        var nH = 300;

        var objApp = nexacro.getApplication();
        var nLeft = (objApp.mainframe.width  / 2) - Math.round(nW / 2);
        var nTop  = (objApp.mainframe.height / 2) - Math.round(nH / 2) ;		

        nLeft = system.clientToScreenX(this, nLeft);
        nTop  = system.clientToScreenY(this, nTop);
        
        var sOpenStyle = "showtitlebar=true showstatusbar=false "
                    + "resizable=true autosize=true titletext=Modeless Popup";

        var objParam = { param1:this.Edit3_1.value
                    , param2:this.Edit3_2.value
                    , param3:this.Dataset3 };

        nexacro.open("chf_popup2" //모달리스 방법
                , "Exe::Exe_Form_Popup.xfdl"
                , this.getOwnerFrame()
                , objParam
                , sOpenStyle
                , nLeft
                , nTop
                , nW
                , nH
                , this);
        
    };


    this.fn_return = function(objDs)
    {
        this.Dataset3.copyData(objDs);
        trace(objDs.saveXML());
    }
    </code></pre>

