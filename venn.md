# Venn diagram in javascript

자바스크립트로 벤 다이어그램을 그리는게 생각보다 쉽지 않다는걸 깨달았다.
뭐.. 다행히 이미 이런 문제를 해결한 사람이 만들어둔 라이브러리를 이용해서 쉽게 할 수 있었다.
바로 [https://github.com/benfred/venn.js](https://github.com/benfred/venn.js) 이것!

각 원에 대해 크기를 지정해주고, 겹치는 부분은 두 개(혹은 그 이상)를 `sets`로 지정해서 사이즈를 지정해주면 constraint-solving을 통해 적절한 위치에 원을 그려준다.

```javscript
let sets = [{"size":1000000,"sets":["Bohemian Rhapsody"]},{"size":884753,"sets":["Bohemian Rhapsody (2011 Remaster) (영화 '수어사이드 스쿼드' 삽입곡)"]},{"size":88476,"sets":["Bohemian Rhapsody","Bohemian Rhapsody (2011 Remaster) (영화 '수어사이드 스쿼드' 삽입곡)"]},{"size":878896,"sets":["알라스카"]},{"size":87890,"sets":["Bohemian Rhapsody","알라스카"]},{"size":878150,"sets":["Bohemian Rhapsody"]},{"size":87815,"sets":["Bohemian Rhapsody","Bohemian Rhapsody"]},{"size":877765,"sets":["위로"]},{"size":87777,"sets":["Bohemian Rhapsody","위로"]},{"size":876457,"sets":["Try Everything"]},{"size":87646,"sets":["Bohemian Rhapsody","Try Everything"]}];
let paper = document.createElement('<div>');
paper.id = 'venn';
document.getElementsByTagName('body')[0].appendChild(paper);
let chart = venn.VennDiagram();
d3.select("#venn").datum(sets).call(chart);
```

<img src="https://raw.githubusercontent.com/kcy1019/TIL/images/venn.png" width="800px">
