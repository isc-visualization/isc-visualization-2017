SVG로 바챠트 그리기
===

SVG로 바 그리기
---

목표
---
- d3와 SVG을 이용하여 바챠트를 그려본다


- SVG 세팅

```javascript
d3.select('#chart').append('svg');
```

```javascript
var w = 120;
var h = 500;
var svg = d3.select('chart').append('svg')
  .attr('width', w)
  .attr('height', h);

```

- 데이터 연결 후 rect 삽입

```javascript
var dataset= [200, 300, 400, 500, 100];

var bar = svg.selectAll('rect.bar')
    .data(dataset)
  .enter().append('rect')
    .attr('class', 'bar')
    .attr('x', 0)
    .attr('y', 0)
    .attr('width', 20)
    .attr('height', 500);
```

- 데이터 값에 따라 위치, 높이 설정

```javascript
bar.attr('x', function(d,i) {
  return i*(20+4);
})
```

```javascript
var padding = 4;
var barWidth = w/dataset.length - padding;

bar.attr('x', function(d,i) {
  return i*(barWidth + padding);
})
.attr('width', barWidth)
.attr('height', function(d){return d;})
```

```javascript
.attr('y', function(d) {
  return h - d;
})
```

- 스타일링

```javascript
.attr('fill', function(d) {
    return 'rgb(0, 0, ' + (d / 2) + ')';
});

```

레이블 추가 Label
---
- 텍스트 http://devdocs.io/svg/element/text

- 데이터 연결하고 텍스트 추가
```javascript
var label = svg.selectAll('text.label')
    .data(dataset)
   .enter().append('text.labe')
    .attr('x', 0)
    .attr('y', 0)
    .attr('text', function(d){return d;})
```


- 위치 바꾸기
```javascript
label.attr('x', function(d,i) {
    return i*(barWidth + padding);
  })
  .attr('y', function(d) {
    return h - d;
  })
```

```javascript
label.attr('text-anchor', 'middle') //text-anchor: start, middle, end
  .attr('dx', function(d,i) {
    return barWidth/2;
  })
  .attr('dy', '1em') //em	Relative to the font-size of the element
```


- 스타일링
```javascript
bar.style("font-family", "sans-serif")
  .style("font-size", "11px")
  .style("fill", "white");
```
