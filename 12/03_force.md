힘-방향 배치 Force-Directed Placement
===

(참조) https://bl.ocks.org/mbostock/4062045

목표
---
- 네트워크 구조를 힘-방향 배치로 표현
- d3-force 사용에 대한 간단한 이해


d3-force
---
http://devdocs.io/d3~4/d3-force

- 입자의 물리적 운동을 시뮬레이션하는 기능을 제공 https://en.wikipedia.org/wiki/Verlet_integration
- 단위 시간동안 동일한 질량을 가진 입자가 특정 힘을 받을때 입자의 속도와 위치 변화

(참조) http://project.newsjel.ly/depressed

### d3.forceSimulation
- `nodes`를 전달하고 여러가지 `force`를 적용하여 물리현상을 시뮬레이션
- `alpha` 값이 `tick`마다 `alphaDecay` 비율에 따라 감소하며 `alphaTarget`을 향해 변화함. `alphaMin` 보다 `alpha` 값이 작아지면 운동이 중단됨.
- `node`에는 자동으로 `x,y,vx,vy`가 설정되며 `fx,fy`를 설정시 다른 값은 무시

### forces
- `vx,vy`나 `x,y`에 지속적인 영향을 줌
- `Centering, Collision, Links, Many-Body, Positioning` 등이 있음
- `.force('이름', force)` 형태로 세팅한다.

Force-Directed Placement
---


```javascript
var w = 800, h = 600;
var margin = {top:10, right:10, bottom: 10, left: 10};
var innerW = w - margin.right - margin.left,
  innerH = h - margin.top - margin.bottom;

var svg = d3.select('body').append('svg')
    .attr('width', w)
    .attr('height', h)
  .append('g')
    .attr('transform', 'translate('+ [margin.left, margin.top] + ')');
var simulation = d3.forceSimulation()
    .force('link', d3.forceLink().id(function(d) { return d.id; }))
    .force('charge', d3.forceManyBody())
    .force('center', d3.forceCenter(innerW / 2, innerH / 2));
```

### 시뮬레이션 설정

- 서로 연결된 노드들은 당기도록 `d3.forceLink`를 사용
 - 개별 링크 값의 `source`, `target` 요소를 보고 노드들을 결정
 - 당길때 각각의 노드를 구별할 수 있도록 `id` 메소드에 콜백함수를 전달
- 노드끼리 서로 밀어내도록 `d3.forceManyBody`를 적용
- 중앙으로 향하도록 `d3.forceCenter`를 사용
 - `d3.forceCenter`의 경우 속도가 아닌 위치를 직접 중앙으로 이동

```javascript
var simulation = d3.forceSimulation()
    .force('link', d3.forceLink().id(function(d) { return d.id; })) //노드별로 id를 보고 구별
    .force('charge', d3.forceManyBody())
    .force('center', d3.forceCenter(innerW / 2, innerH / 2));
```

### 스케일 및 DOM 설정
 - 실제로 위치를 결정하는 것은 시뮬레이션을 적용할 때. 현재는 DOM만 추가해둔다.
```javascript
d3.json('miserables.json', callback);
function callback(err, data) {
  if(err) return console.error(err);
  var cDomain = d3.set(data.nodes.map(function(d){return d.group;})) //d3.set을 통해 중복이 되지 않도록 d.group 모음
    .values().sort(function(a,b){return a-b;}); //번호 순서대로 정렬
      
  var c = d3.scaleOrdinal() // 색상은 group값을 통해 결정
    .domain(cDomain)
    .range(d3.schemeCategory20);

  var link = svg.append("g")
     .attr('class', 'links')
   .selectAll('line')
      .data(data.links)
   .enter().append('line'); //링크의 개수만큼 선을 추가

  var node = svg.append('g')
      .attr('class', 'nodes')
    .selectAll('circle')
      .data(data.nodes)
    .enter().append('circle')
      .attr('r', 4)
      .style('fill', function(d){return c(d.group)}); //노드의 개수만큼 원을 추가
  }
```

### 시뮬레이션 틱tick 이벤트 설정

- tick마다 node와 link 요소를 이동
 - link의 경우 source, target에 node 데이터가 연결

```javascript
simulation.nodes(data.nodes) //nodes 에 대항하는 값을 전달한다.
simulation.on('tick', function() { //매 tick마다 링크와 노드의 위치를 결정해준다.
    link.attr('x1', function(d){return d.source.x;}) //각각의 링크의 source와 target 값이 변형된 것을 확인할 수 있다.
      .attr('y1', function(d){return d.source.y;})
      .attr('x2', function(d){return d.target.x;})
      .attr('y2', function(d){return d.target.y;});
    node.attr('cx', function(d){return d.x;}) // node 값에도 x,y,vx,vy,index 값이 추가된다. 
      .attr('cy', function(d){return d.y;});
  })
```


- tick 설정 후에 `d3.forceLink`의 링크를 설정하면 매 tick 마다 simulation의 
```javascript
simulation.force('link') //link로 이름 지은 d3.forceLink() 시뮬레이터를 불러옴
  .links(data.links); //link 값을 전달
```


