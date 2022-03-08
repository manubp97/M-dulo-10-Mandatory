# M-dulo-10-Mandatory
![Alt text](./capture.PNG?raw=true "Map")
# Map of COVID cases in Spain

In the map below, the number of covid cases are shown with circles, their size is relative to the amount of cases. With the button we can compare the number of cases in two different times, 22 of April of 2021 as the initial stat and 4 of March of 2022. The number of cases is accumulated since the start of the pandemy.

# Installation

Clone the repository

```bash
git clone <repository>
```

Open VSCode and run a terminal.

```bash
npm install
```

```bash
npm install topojson-client --save
```

```bash
npm install @types/topojson-client --save-dev
```

```bash
npm install d3-composite-projections --save
```

```bash
npm install @types/node --save-dev
```
```bash
npm start
```

### Max affected communities:

```typescript
const calculateMaxAffected = (dataset: ResultEntry[]) => {
  return dataset.reduce(
    (max, item) => (item.value > max ? item.value : max),
    0
  );
};
```
### Max radious
```typescript
const calculateAffectedRadiusScale = (maxAffected: number) => {
  return d3.scaleLinear().domain([0, maxAffected]).range([0, 20]);
};
```

### Scale the radius:

```typescript
const calculateRadiusBasedOnAffectedCases = (
  comunidad: string,
  dataset: ResultEntry[]
) => {
  const maxAffected = calculateMaxAffected(dataset);

  const affectedRadiusScale = calculateAffectedRadiusScale(maxAffected);

  const entry = dataset.find((item) => item.name === comunidad);

  const adder = d3
    .scaleThreshold<number, number>()
    .domain([50000, 100000, 500000, 1000000, 5000000])
    .range([0.2, 0.4, 2, 4, 20]);

  return entry ? affectedRadiusScale(entry.value) + adder(maxAffected) : 0;
};
```

### Update chart when buttons are clicked

We now that we have to pass d.properties.NAME_1 as a parameter for assignColorToCommunity function because when we inspect spain.json, we can see that the property NAME_1 refers to the community name.

```typescript
const updateChart = (dataset: ResultEntry[]) => {
  svg
    .selectAll("circle")
    .data(latLongCommunities)
    .attr("class", "affected-marker")
    .attr("cx", (d) => aProjection([d.long, d.lat])[0])
    .attr("cy", (d) => aProjection([d.long, d.lat])[1])
    .transition()
    .duration(800)
    .attr("r", (d) => calculateRadiusBasedOnAffectedCases(d.name, dataset));
};
```

### Create the button

```typescript
document
  .getElementById("initial")
  .addEventListener("click", function handleInitialStats() {
    updateChart(initialStats);
  });

document
  .getElementById("final")
  .addEventListener("click", function handleFinalStats() {
    updateChart(finalStats);
  });
```
