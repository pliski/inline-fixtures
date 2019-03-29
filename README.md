# inline-fixtures

[![CircleCI](https://circleci.com/gh/ofrobots/inline-fixtures.svg?style=svg)](https://circleci.com/gh/ofrobots/inline-fixtures)

Sometimes tests need fixture directories. It would be nice if the fixtures were inline, next to the test source code. [Observe](https://github.com/nodejs/node/blob/d3fb7e1b3658a6f00e1c631aa551f2ea0ab81f5e/test/parallel/test-require-dot.js):

```js
const fixtures = require('../common/fixtures');

const a = require(fixtures.path('module-require', 'relative', 'dot.js'));
const b = require(fixtures.path('module-require', 'relative', 'dot-slash.js'));
//...
```

At this point it is not clear what the fixture files themselves actually do, or how the rest of the test file relates to them. Tests and fixtures should be together! The fixture is part of the test.

`inline-fixtures` dynamically creates a temporary directory and populates it with the fixture
layout you provide, and then calls the passed in function. This has the
additional benefit that fixture mutation cannot leak from one test to
another.

Example:

```js
import {withFixtures} from 'inline-fixtures';

describe('tests for fs.readFileSync', () => {
  it('should have a test cases', async () => {
    const FIXTURES = {
      'README.md': 'Hello Mars.',
      anotherDir: {
        'dot.js': '42;',
      },
    };
    await withFixtures(FIXTURES, async (fixturesDir) => {
      // A temporary `fixturesDir` exists at this point with `README.md`
      // and `anotherDir`. The latter contains `index.js`.
      const readmePath = path.join(fixturesDir, 'README.md');
      const contents = fs.readFileSync(readmePath, 'utf8');
      assert.strictEqual(contents, FIXTURES['README.md']);     
    });
  });
});
```
