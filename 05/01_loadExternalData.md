외부 데이터 연결
====
- [d3-request](http://devdocs.io/d3~4/d3-request) 모듈을 활용
- [XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)를 이용하여 다양한 종류의 파일을 쉽게 가져올 수 있다.
- 정해진 파일 양식을 가져오는 것 이외에도 커스텀 리퀘스트를 만들 수 있다.

TEXT
---

```javascript
d3.text("sample.txt", function(error, text) {
  if (error) throw error;
  console.log(text);
});
```

DSV
---
- Delimiter Separated Values
- 보통 CSV(Comma)나 TSV(Tab)를 사용한다. (클라이언트에선 `.xls, .xlsx`는 불러올 수 없음)
  - 실제론 그냥 텍스트 파일일뿐
- 한글 인코딩을 자동으로 확인 못하기 때문에 맥/윈도우 운영체제에 따라 엑셀로 dsv 포맷 저장/불러오기 할때 문제가 생기는 경우 많음, 
  - 일단 UTF-8으로 통일
  - (참고): https://www.libreoffice.org/ 리브레 오피스에서 불러오면 인코딩 자유롭게 설정 가능

```javascript
d3.csv("sample.csv", function(error, data) {
  if (error) throw error;
  console.log(data);
});
```

- csv는 모든 형태를 스트링으로 인식하므로, 타입을 직접 지정해줘야함.
- 행 변환 함수(row conversion function)를 전달하여 결과를 미리 변형하는 것이 좋음

```javascript
function row(d) { //row conversion function
  return {
    orderDate : d3.timeParse('%m/%d/%Y')(d['Order Date']),
    orderId : +d["Order ID"],
    sales : d['Sales']
  }  
}

function callback(error, data) {
  if (error) throw error;
  console.log(data);
}

var url = "sample.csv";
d3.csv(url, row, callback);
```

- 참고 `d3.timeParse` : 스트링 형태의 시간을 Date 오브젝트로 변환하는 함수를 내놓음
```javascript
var parser = d3.timeParse('%m/%d/%Y')
console.log(parser('10/13/2010'));
```


- 위의 함수를 풀어쓰면 아래와 같다.

```javascript
// .row, .get 사용
d3.csv(url)
  .row(row)
  .get(callback);

// d3.request 활용
d3.request(url)
    .mimeType("text/csv")
    .response(function(xhr) { return d3.csvParse(xhr.responseText, row); })
    .get(callback);
```

- 데이터 불러와서 찍어보기
```javascript
function callback(error, data) {
  if (error) throw error;
  d3.select('body').selectAll('p')
    .data(data)
    .enter().append('p')
    .text(function(d){return d.orderId + ' | ' + d.orderDate + ' | ' + d.sales})
}
```



JSON
---
- [JSON](http://json.org/)(JavaScript Object Notation) 형식의 파일
- 웹상에서 데이터 전송을 위해 정해진 포맷으로 자바스크립트 오브젝트의 포맷과 동일하다,
- 일반적인 표형식과 달리 위계 구조의 자료를 표기하기 편리하다.

```json
{"menu": {
  "id": "file",
  "value": "File",
  "popup": {
    "menuitem": [
      {"value": "New", "onclick": "CreateNewDoc()"},
      {"value": "Open", "onclick": "OpenDoc()"},
      {"value": "Close", "onclick": "CloseDoc()"}
    ]
  }
}}
```

```javascript
d3.json('sample.json', function(err, data) {
  if (error) throw error;
  console.log(data);
  console.log(data.popup.menuitem);
});
```

```javascript
d3.request(url)
    .mimeType("application/json")
    .response(function(xhr) { return JSON.parse(xhr.responseText); })
    .get(callback);
```
