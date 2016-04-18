calculate scale
---
```js

exports.calculateScales = (chartWidth, chartHeight, xValues, yValues, xDomain, yDomain) => {

  var xScale, yScale, xdomain, ydomain;
  xDomain = xDomain || [], yDomain = yDomain || [];

  if (xValues.length > 0 && Object.prototype.toString.call(xValues[0]) === '[object Date]') {
    xScale = d3.time.scale()
      .range([0, chartWidth]);
  } else {
    xScale = d3.scale.linear()
      .range([0, chartWidth]);
  }
  xdomain = d3.extent(xValues);
  if(xDomain[0] !== undefined && xDomain[0] !== null) xdomain[0] = xDomain[0];
  if(xDomain[1] !== undefined && xDomain[1] !== null) xdomain[1] = xDomain[1];
  xScale.domain(xdomain);

  if (yValues.length > 0 && Object.prototype.toString.call(yValues[0]) === '[object Date]') {
    yScale = d3.time.scale()
      .range([chartHeight, 0]);
  } else {
    yScale = d3.scale.linear()
      .range([chartHeight, 0]);
  }

  ydomain = d3.extent(yValues);
  if(yDomain[0] !== undefined && yDomain[0] !== null) ydomain[0] = yDomain[0];
  if(yDomain[1] !== undefined && yDomain[1] !== null) ydomain[1] = yDomain[1];
  yScale.domain(ydomain);

  return {
    xScale: xScale,
    yScale: yScale
  };

};
```