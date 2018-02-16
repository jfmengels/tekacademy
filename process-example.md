### Example process of writing a function TDD-style

- Case study: a function that compares two simplified semantic versions ("1.2", "2.0", ...), that are already parsed and are of the form `{major: number, minor: numberumber}` where number is a number >= 0.

- Step 1
```js
function compareSemanticVersions(ref1, ref2) {}

// Fails!
test('should return 1 if major version of ref2 is higher than major version of ref1', t => {
  const res = compareSemanticVersions(
    {major: 1, minor: 1},
    {major: 2, minor: 1}
  );
  t.is(res, 1);
  const res2 = compareSemanticVersions(
    {major: 1, minor: 10000},
    {major: 2, minor: 1}
  );
  t.is(res2, 1);
});
```

- Step 2
```js
function compareSemanticVersions(ref1, ref2) {
  return 1;
}

// (Same test)
// Passes!
test('should return 1 if major version of ref2 is higher than major version of ref1', t => {
  const res = compareSemanticVersions(
    {major: 1, minor: 1},
    {major: 2, minor: 1}
  );
  t.is(res, 1);
  const res2 = compareSemanticVersions(
    {major: 1, minor: 10000},
    {major: 2, minor: 1}
  );
  t.is(res2, 1);
});
```

- Step 3
```js
function compareSemanticVersions(ref1, ref2) {
  return 1;
}

// (New test)
// Fails!
test('should return -1 if major version of ref2 is lower than major version of ref1', t => {
  const res = compareSemanticVersions(
    {major: 2, minor: 1},
    {major: 1, minor: 1}
  );
  t.is(res, -1);
  const res2 = compareSemanticVersions(
    {major: 2, minor: 1},
    {major: 1, minor: 10000}
  );
  t.is(res2, -1);
});
```

- Step 4
```js
function compareSemanticVersions(ref1, ref2) {
  if (ref2.major > ref1.major) { return 1; }
  return -1;
}

// (Same test)
// Passes!
test('should return -1 if major version of ref2 is lower than major version of ref1', t => {
  const res = compareSemanticVersions(
    {major: 2, minor: 1}
    {major: 1, minor: 1},
  );
  t.is(res, -1);
  const res2 = compareSemanticVersions(
    {major: 2, minor: 1}
    {major: 1, minor: 10000},
  );
  t.is(res2, -1);
});
```

- Step 5
```js
function compareSemanticVersions(ref1, ref2) {
  if (ref2.major > ref1.major) { return 1; }
  return -1;
}

// (New test)
// Fails!
test('should return 1 if major versions are equal but minor version of ref2 is higher than minor version of ref1', t => {
  const res = compareSemanticVersions(
    {major: 1, minor: 1},
    {major: 1, minor: 2}
  );
  t.is(res, 1);
  const res2 = compareSemanticVersions(
    {major: 1, minor: 100},
    {major: 1, minor: 2}
  );
  t.is(res2, 1);
});
```

- Step 6
```js
function compareSemanticVersions(ref1, ref2) {
  if (ref2.major > ref1.major) { return 1; }
  if (ref2.major < ref1.major) { return -1; }
  return 1;
}

// (Same test)
// Passes!
test('should return 1 if major versions are equal but minor version of ref2 is higher than minor version of ref1', t => {
  const res = compareSemanticVersions(
    {major: 1, minor: 1},
    {major: 1, minor: 2}
  );
  t.is(res, 1);
  const res2 = compareSemanticVersions(
    {major: 1, minor: 100},
    {major: 1, minor: 2}
  );
  t.is(res2, 1);
});
```

- Step 7
```js
function compareSemanticVersions(ref1, ref2) {
  if (ref2.major > ref1.major) { return 1; }
  if (ref2.major < ref1.major) { return -1; }
  return 1;
}

// (New test)
// Fails!
test('should return -1 if major versions are equal but minor version of ref2 is lower than minor version of ref1', t => {
  const res = compareSemanticVersions(
    {major: 1, minor: 2},
    {major: 1, minor: 1}
  );
  t.is(res, -1);
  const res2 = compareSemanticVersions(
    {major: 1, minor: 2},
    {major: 1, minor: 100}
  );
  t.is(res2, -1);
});
```

- Step 8
```js
function compareSemanticVersions(ref1, ref2) {
  if (ref2.major > ref1.major) { return 1; }
  if (ref2.major < ref1.major) { return -1; }
  if (ref2.minor > ref1.minor) { return 1; }
  return -1;
}

// (Same test)
// Passes!
test('should return -1 if major versions are equal but minor version of ref2 is lower than minor version of ref1', t => {
  const res = compareSemanticVersions(
    {major: 1, minor: 2},
    {major: 1, minor: 1}
  );
  t.is(res, -1);
  const res2 = compareSemanticVersions(
    {major: 1, minor: 2},
    {major: 1, minor: 100}
  );
  t.is(res2, -1);
});
```

- Step 9
```js
function compareSemanticVersions(ref1, ref2) {
  if (ref2.major > ref1.major) { return 1; }
  if (ref2.major < ref1.major) { return -1; }
  if (ref2.minor > ref1.minor) { return 1; }
  return -1;
}

// (New test)
// Fails!
test('should return 0 if all versions are equal', t => {
  const res = compareSemanticVersions(
    {major: 1, minor: 1},
    {major: 1, minor: 1}
  );
  t.is(res, 0);
  const res2 = compareSemanticVersions(
    {major: 100, minor: 10000},
    {major: 100, minor: 10000}
  );
  t.is(res2, 0);
});
```

- Step 10
```js
function compareSemanticVersions(ref1, ref2) {
  if (ref2.major > ref1.major) { return 1; }
  if (ref2.major < ref1.major) { return -1; }
  if (ref2.minor > ref1.minor) { return 1; }
  if (ref2.minor < ref1.minor) { return -1; }
  return 0;
}

// (Same test)
// Passes!
test('should return 0 if all versions are equal', t => {
  const res = compareSemanticVersions(
    {major: 1, minor: 1},
    {major: 1, minor: 1}
  );
  t.is(res, 0);
  const res2 = compareSemanticVersions(
    {major: 100, minor: 10000},
    {major: 100, minor: 10000}
  );
  t.is(res2, 0);
});
```

- Step 15
```js
// Refactoring
function compare(a, b) {
  if (a < b) {
    return 1;
  }
  return a > b ? -1 : 0;
}

function compareSemanticVersions(ref1, ref2) {
  return (
    compare(ref1.major, ref2.major) ||
    compare(ref1.minor, ref2.minor)
  );
}

// Passes all previous tests!
```

- Without TDD, Adding tests to get 100% code coverage.
```js
test('should compare semantic versions correctly', t => {
  const version1 = {major: 1, minor: 1};
  const version2 = {major: 2, minor: 1};
  t.is(compareSemanticVersions(version1, version1), 0);
  t.is(compareSemanticVersions(version1, version2), 1);
  t.is(compareSemanticVersions(version2, version1), -1);
});
```

Warning: When refactoring, new bugs may be inserted for cases that were not thought of before (division by 0, checking the property of something), but the tests make sure that all existing cases are still working properly. It's better to handle more cases than needed, in case a future refactoring adds "breakable" parts.

- Result: Lots of tests for a pretty small function, but there are a lot of branches here and pretty much every test is necessary to make sure nothing is broken.
