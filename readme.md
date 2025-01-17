# 01 Hello

In this example we will setup react testing library and create a simple test over a component that
just display and _h1_

We will start from `00-boilerplate`.

# Steps

- `npm install` to install previous sample packages:

```bash
npm install
```

- Let's install [@testing-library/react](https://github.com/testing-library/@testing-library/react)

```bash
npm install @testing-library/react -D
```

- We will create a simple component.

### ./src/say-hello.tsx

```javascript
import * as React from 'react';

interface Props {
  person: string;
}

export const SayHello: React.FunctionComponent<Props> = props => {
  const { person } = props;
  return <h1>Hello {person}!</h1>;
};
```

- Let's add our first test, we want to instantiate _SayHello_ and check that we are getting an h1 that contains the name of the person that we are passing.

### ./src/say-hello.spec.tsx

```javascript
import * as React from 'react';
import { render } from '@testing-library/react';
import { SayHello } from './say-hello';

describe('SayHello component specs', () => {
  it('should display the person name', () => {
    // Arrange
    const person = 'John';

    // Act
    const { getByText } = render(<SayHello person={person} />);

    // Assert
    const element = getByText('Hello John!');
    expect(element).not.toBeNull();
    expect(element.tagName).toEqual('H1');
  });
});
```

- Another approach is to use `snapshot testing`:

### ./src/say-hello.spec.tsx

```diff
...

+ it('should display the person name using snapshot testing', () => {
+   // Arrange
+   const person = 'John';

+   // Act
+   const { asFragment } = render(<SayHello person={person} />);

+   // Assert
+   expect(asFragment()).toMatchSnapshot();
+ });

```

- It will add a file like:

### ./src/**snapshots**/say-hello.spec.tsx.snap

```
// Jest Snapshot v1, https://goo.gl/fbAQLP

exports[`SayHello component specs should display the person name using snapshot testing 1`] = `
<DocumentFragment>
  <h1>
    Hello John!
  </h1>
</DocumentFragment>
`;

```

- This kind of tests are useful when we want to make sure the UI does not change. The snapshot should be committed to be reviewed as part of the pull request.

- On the other hand, this could be a `bad idea` in complex scenarios due to it could be complicated review the whole snapshot and we could fall into a bad habit of updating snapshot tests blindly.

- A third approach is using [jest-dom](https://github.com/testing-library/jest-dom) from testing-library. It provides a set of custom jest matchers to create declarative and clear to read expects.

```bash
npm install @testing-library/jest-dom --save-dev
```

- Configure it:

### ./config/test/setup-after.ts

```javascript
import '@testing-library/jest-dom';
import '@testing-library/jest-dom/extend-expect';
```

- Update `jest` config:

### ./config/test/jest.json

```diff
{
  "rootDir": "../../",
  "preset": "ts-jest",
- "restoreMocks": true
+ "restoreMocks": true,
+ "setupFilesAfterEnv": ["<rootDir>/config/test/setup-after.ts"]
}

```

> We need to setup after jest environment execution.

- Now, we could write it like:

### ./src/say-hello.spec.tsx

```diff
...

+ it('should display the person name using jest-dom', () => {
+   // Arrange
+   const person = 'John';

+   // Act
+   const { getByText } = render(<SayHello person={person} />);

+   const element = getByText('Hello John!');

+   // Assert
+   expect(element).toBeInTheDocument();
+ });

```

# About Basefactor + Lemoncode

We are an innovating team of Javascript experts, passionate about turning your ideas into robust products.

[Basefactor, consultancy by Lemoncode](http://www.basefactor.com) provides consultancy and coaching services.

[Lemoncode](http://lemoncode.net/services/en/#en-home) provides training services.

For the LATAM/Spanish audience we are running an Online Front End Master degree, more info: http://lemoncode.net/master-frontend
