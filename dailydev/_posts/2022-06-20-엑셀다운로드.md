---
layout: post
title: Javascript로 Excel 다운로드해보자
description: >
  Javascript Excel 다운로드
hide_description: false
category: dailydev
---


- Table of Contents
{:toc .large-only}

공공기관 웹프로젝트를 하다 보면 엑셀을 많이 요구 하게 됩니다. 웹에 익숙하지 않은 이용자들이나 기존의 문서방식
이나 데이터를 조작해서 보고 싶어하는 경우 등 엑셀을 통해 파일을 주고 받기도 하기 때문인거 같다.
Javascript로 어렵지 않게 구현 할 수 있다.

## Javascript

```javascript
function excelDownload(title){ 
	var fileName = title + '.xls'; //엑셀파일명 설정 
	var sheetName = ''; //시트이름
	var excelTable = '<table><tr><td style=""background-color":"#D8E4BC","font-weight":"bold","height":"50px">셀내용</td></tr></table>';
	//시트내용
	//엑셀내용을 html 테이블로 만들수있다. 테이블형태처럼 엑셀이 만들어 진다고 볼수있다. tr = 엑셀행 td=엑셀열 td text = 셀내용
	//td에 스타일을 지정하면 셀의 스타일이 적용된다.
	var html = '<html xmlns:x="urn:schemas-microsoft-com:office:excel">';
	html = html + '<head><meta http-equiv="content-type" content="application/vnd.ms-excel; charset=UTF-8">';
	html = html + '<xml><x:ExcelWorkbook><x:ExcelWorksheets><x:ExcelWorksheet>';
	html = html + '<x:Name>' +  sheetName + '</x:Name>';
	html = html + '<x:WorksheetOptions><x:Panes></x:Panes></x:WorksheetOptions></x:ExcelWorksheet>';
	html = html + '</x:ExcelWorksheets></x:ExcelWorkbook></xml></head><body>';
	html = html + excelTable;
	html = html + '</table></body></html>';
	var useragent = window.navigator.userAgent; //엑셀 다운로드를 위한 사용자 에이전트를 이용한 브라우저 감지
	var msie = ua.indexOf("MSIE "); // 브라우저가 익스플로러 인지 확인을 위한 변수
	alert("엑셀 다운로드 완료");
	if (msie > 0 || !!navigator.userAgent.match(/Trident.*rv\:11\./)) {
		if (window.navigator.msSaveBlob) {
		var blob = new Blob([html], { // 엑셀데이터를 담기 위한 블랍객체 선언 및 MIME타입 설정
		type: "application/csv;charset=utf-8;"
		});
		navigator.msSaveBlob(blob, fileName);  // 익스플로러이면 blobsave 지원
		}
	} else {
		var blob2 = new Blob([html], {
			type: "application/csv;charset=utf-8;"
		});
		var filename = fileName;
		var elem = window.document.createElement('a');
		elem.href = window.URL.createObjectURL(blob2);
		elem.download = filename;
		document.body.appendChild(elem);
		elem.click();
		document.body.removeChild(elem);
		//익스플로러가 아닌경우 바로 다운이 되지 않기때문에 임의로 버튼을 만들어
		//클릭처리하면 다운로드가 된다.
		}
}
```