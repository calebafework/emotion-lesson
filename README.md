# CSS in JS (Emotion and Styled-Components)

![Emotion.js Logo](https://miro.medium.com/max/3840/1*hPN6aCm0RmEdHwksS8kaxA.png)

## Why use Emotion?

[Emotion](https://emotion.sh/) is a Javascript library that helps keep the concerns of styling and element architecture separate and makes components more readable, overall. Furthermore, when you have components that rely on JavaScript for their style, Emotion gives control of those states back to CSS instead of using a multitude of conditional class names.

## CSS in JS

Local Inline Styles are useful for specificity, but are limited in capabilities (it's only a subset of CSS). They don’t have media queries, keyframes, pseudo-selectors, etc.

For example:

```javascript

    // Vanilla JS
    let element = document.createElement('div');
    element.setAttribute('style', 'color: red; padding: 20px;')

    // React
    <Component
        style={{
            color: "red",
            padding: "20px",
            }}
    />

```

With Emotion, JavaScript is used to style our components with CSS template literals. When the components are parsed, CSS is generated and attached to the DOM. The CSS used in styled is much more capable than inline styles, allowing for nesting, mixins, and other advanced usage (but not as powerful as a CSS pre-processor).

See sites that use CSS in JS:
[Oscar Health](https://www.hioscar.com/)

```javascript
// this comment tells babel to convert jsx to calls to a function called jsx instead of React.createElement
/** @jsxImportSource @emotion/core */
import { css } from '@emotion/core';

const color = 'white';

render(
  <div
    css={css`
      padding: 32px;
      background-color: hotpink;
      font-size: 24px;
      border-radius: 4px;
      &:hover {
        color: ${color};
      }
    `}
  >
    Hover to change color.
  </div>
);
```

## Code-Along

### Installation

First we're going to want to install the `styled-components` dependency.

```
npm i @emotion/styled @emotion/core
npm i emotion-theming react-spring
```

### The Basic Setup

If you take a look at our boilerplate, we will have a `Components` directory, where we will store all of our styled components, as well as an App.js and index.js.

Let's implement a basic button in App.js to familarize ourselves with how Emotion works!

- Step 1 Configure the JSX Babel Plugin to use Emotion's `jsx` function instead of `React.createElement`

```javascript
// Set the jsx pragma at the top of any js file that uses the css prop
/** @jsxImportSource @emotion/core */

import { css } from '@emotion/core';
```

You may also configure your `.babelrc` if you are using something like `Next.js`. Your .babelrc would look something like so:

```json
{
  "presets": [
    [
      "next-babel",
      {
        "preset-react": {
          "runtime": "automatic",
          "importSource": "@emotion/core"
        }
      }
    ]
  ],
  "plugins": ["babel-plugin-emotion"]
}
```

- Step 2 Create your first CSS in JS component

```javascript
function App() {
  return (
    <div>
      <button
        css={css`
          padding: 32px;
          background-color: hotpink;
          font-size: 24px;
          border-radius: 4px;
          cursor: pointer;
        `}
      >
        Pink Button
      </button>
    </div>
  );
}
```

- Step 3 Add a hover pseudo class, and pass in dynamic styles.

```javascript
function App() {
  const color = 'white';
  return (
    <div>
      <button
        css={css`
          padding: 32px;
          background-color: hotpink;
          font-size: 24px;
          border-radius: 4px;
          cursor: pointer;
          &:hover {
            color: ${color};
          }
        `}
      >
        Hover to change color
      </button>
    </div>
  );
}
```

### Styled Components

`styled` is a way to create React components that have styles attached to them. It’s available from @emotion/styled. styled was heavily inspired by styled-components and glamorous

#### Styling elements and components

`styled` is very similar to css except you call it with an html tag or React component and then call that with a template literal for string styles or a regular function call for object styles.

```javascript
// App.js

import styled from '@emotion/styled';

const Container = styled.div`
  width: 100vw;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: space-around;
  flex-direction: column;
`;

function App() {
  return (
    <Container>
      <button
        css={css`
          padding: 32px;
          background-color: hotpink;
          font-size: 24px;
          border-radius: 4px;
          cursor: pointer;
        `}
      >
        Hover to change color
      </button>
    </Container>
  );
}
```

#### Styled Buttons

Let's isolate our buttons into their own seperate components where we can export them and use them all over our project.

```javascript
// /components/Buttons.js
import styled from '@emotion/styled';

export const Button = styled.button`
  padding: 12px 24px;
  font-size: 1rem;
  border-radius: 2px;
  min-width: 100px;
  cursor: pointer;
  font-family: 'Menlo', monospace;
`;
```

In our `App.js`, we will import the Button and render it in our App.

```javascript
// App.js

import { Button } from './components';

function App() {
  return (
    <Container>
      <Button>Press me</Button>
    </Container>
  );
}

export default App;
```

### Props with Styled Components

We can pass props to our styles much like regular React components in order to create a wider variety of styles. Take for instance this implementation of using a large button.

```javascript
<Button large>Press me</Button>
```

Within our CSS, we can pass styles to effect our padding and font-size with ternary operators:

```javascript
export const Button = styled.button`
  padding: ${(props) => (props.large ? `16px 25px` : `12px 24px`)};
  font-size: ${(props) => (props.large ? `1.5rem` : `1rem`)};
`;
```

### Inheritance with Styled-Components

We can refactor our `Buttons.js` to dry our code when creating more button variations. We will create a base `Button` component that all specific buttons will inherit the styled properties of.

```javascript
// Buttons.js
...

// The Base Button Component
export const Button = styled.button`
    padding: 12px 24px;
    font-size: 1rem;
    border-radius: 2px;
    min-width: 100px;
    cursor: pointer;
    font-family: "Menlo", monospace;
`
// PrimaryButton inherits Button
export const PrimaryButton = styled(Button)`
  background-color: red;
  border: none;
  color: white;
`;
// SecondaryButton inherits Button
export const SecondaryButton = styled(Button)`
  background: none;
  border: 1px solid black;
  color: white;
  color: black;
  transition: background-color 0.2s linear, color 0.2s linear;

  &:hover {
    background-color: red;
    color: white;
  }
`;

```

### Global Styles with Emotion

You can also define Global styles with Emotion. We will also be installing the polished dependency to utilize some additional functionality for CSS in JS type of projects

- `import { Global, css } from '@emotion/core'`

- create a Global component in your index.js

```javascript
// index.js
...
import { Global, css } from '@emotion/core';
import App from './App';

ReactDOM.render(
  <React.StrictMode>
    <Global
      styles={css`
        html {
          font-size: 16px;
          box-sizing: border-box;
        }

        *,
        *:before,
        *:after {
          box-sizing: inherit;
        }

        body {
          margin: 0;
          font-family: 'Menlo', monospace;
        }

        main {
          width: 90%;
          margin: 0 auto;
        }
      `}
    />
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);
```

### Building a Modal with Nested Styled Components

![Modal](https://github.com/kleimaj/styled-components/raw/master/markdown/images/modal.png)

In the second part of this lesson we will be building a modal in order to call our users to action. This is a great way of leading them to a login / signup page. For this example I used an open source illustration from [undraw.io](https://undraw.co/) as well as an icon from [Font Awesome](https://fontawesome.com/)

#### 1. Create your Modals.js in /components

```
touch components/Modals.js
```

#### 2. Create your ModalWrapper component to wrap all sub-components.

First, create your parent component that will house all other components of the modal.

```javascript
// Modals.js

import React from 'react';
import styled from '@emotion/styled';

const ModalWrapper = styled.div`
  width: 800px;
  height: 550px;
  box-shadow: 0 5px 16px rgba(0, 0, 0, 0.2);
  background-color: #ffffff;
  color: black;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  position: relative;
  border-radius: 2px;
  font-family: 'Menlo', monospace;
`;
```

#### 3. Create Your Header and Text Component for the Modal.

We will then create the `SignUpHeader` and `SignUpText`.

```javascript
// Modals.js

const SignUpHeader = styled.h3`
  font-size: 2rem;
  margin-bottom: 0;
`;

const SignUpText = styled.p`
  font-size: 1rem;
  max-width: 70%;
  text-align: center;
`;
```

#### 4. Create a stateless functional component which will act as our modal.

```javascript
// Modal.js

// import the PrimaryButton
import { PrimaryButton } from './Buttons';

export const SignUpModal = () => {
  return (
    <ModalWrapper>
      <SignUpHeader>Sign Up!</SignUpHeader>
      <SignUpText>Sign up today to get access to cool things!</SignUpText>
      <PrimaryButton>Submit</PrimaryButton>
    </ModalWrapper>
  );
};
```

#### 5. Import our assets into `Modals.js` and render them

```javascript
// Modals.js

import SignUp from '../assets/signup.svg';
import CloseIcon from '../assets/close-icon.svg';

// Button to wrap our CloseIcon component
const CloseModalButton = styled.button`
  cursor: pointer;
  background: none;
  border: none;
  position: absolute;
  right: 40px;
  top: 40px;
  width: 24px;
  height: 24px;
  padding: 0;
`;

export const SignUpModal = () => {
  return (
    <ModalWrapper>
      <img src={SignUp} alt='Sign up for an account' aria-hidden='true' />
      <SignUpHeader>Sign Up!</SignUpHeader>
      <SignUpText>Sign up today to get access to cool things!</SignUpText>
      <PrimaryButton>Submit</PrimaryButton>
      <CloseModalButton aria-label='Close modal'>
        <img src={CloseIcon} alt='Close modal' />
      </CloseModalButton>
    </ModalWrapper>
  );
};
```

#### 7. Let's place our modal into App.js

```javascript
// App.js
import React from 'react';
import { PrimaryButton } from './components/Buttons';
import { SignUpModal } from './components/Modals';

function App() {
  return (
    <Container>
      <h1>My Styled Components</h1>
      <PrimaryButton>Click Me</PrimaryButton>
      <SignUpModal />
    </Container>
  );
}

export default App;
```

### Animation and functionality with react spring

#### 1. Add state to our App component

In `App.js`, we want to import `useState` to create a toggle state for our modal.

- `import React, { useState } from 'react`;

- initialize `showModal` and `setShowModal` with a default value of `false`

- add an `onClick` listener to our `PrimaryButton`

- pass the useState properties to the `SignUpModal`

```javascript
// App.js

import React, { useState } from 'react';
import { PrimaryButton } from './components/Buttons';
import { SignUpModal } from './components/Modals';

function App() {
  // useState hook
  const [showModal, setShowModal] = useState(false);
  return (
    <div
      style={{
        width: '100vw',
        height: '100vh',
        display: 'flex',
        alignItems: 'center',
        justifyContent: 'space-around',
        flexDirection: 'column',
      }}
    >
      <h1>My Styled Components</h1>

      <PrimaryButton onClick={() => setShowModal(!showModal)}>
        Click Me
      </PrimaryButton>

      <SignUpModal showModal={showModal} setShowModal={setShowModal} />
    </div>
  );
}

export default App;
```

#### 2. Add animation to our modal

- First we will `npm install react-spring`

- Next, `import { useSpring, animated } from 'react-spring'` in our `Modals.js`

- Define the animation style in our `SignUpModal` component

- wrap the entire `SignUpModal` in an `<animated.div style={animated}>`

- Finally remember to add the `setShowModal` callback to the `CloseModalButton`

```javascript
// Modals.js

import { useSpring, animated } from 'react-spring';

...

export const SignUpModal = (props) => {
    const animation = useSpring({
        opacity: props.showModal ? 1 : 0,
        transform: props.showModal ? `translateY(0)` : `translateY(-200%)`
    });
    return (
        <animated.div style={animation}>
            <ModalWrapper>
                <img
                    src={Illustrations.SignUp}
                    alt="Sign up for an account"
                    aria-hidden="true"
                />
                <SignUpHeader>Sign Up!</SignUpHeader>
                <SignUpText>Sign up today to get access to cool things!</SignUpText>
                <PrimaryButton>Submit</PrimaryButton>
                <CloseModalButton
                    aria-label="Close modal"
                    onClick={() => props.setShowModal(false)}
                >
                    <CloseIcon/>
                </CloseModalButton>
            </ModalWrapper>
        </animated.div>
    )
}

```

<strong>Alternatively, you can restyle the `ModalWrapper` as an animated div</strong>

```javascript
// Modals.js

const ModalWrapper = styled(animated.div)`
...
`
// apply animation like so
...
<ModalWrapper style={animation}>

```

### Theming with CSS-in-JS

It's very easy to maintain themes across a react project with Emotion. First we must install the proper dependencies to utilize the `ThemeProvider` component.

```bash
npm install emotion-theming
```

> Next, we can import the `ThemeProvider` into our `App.js`

```javascript
// App.js
import { ThemeProvider } from 'emotion-theming';
```

Next, go ahead and copy the following themes we will use for this exercise. They are javascript objects that include a `lightTheme` and `darkTheme`

```javascript
const themeLight = {
  text: '#000',
  background: '#fff',
  modalBg: '#fff',
  buttonText: '#000',
  buttonTextHover: '#fff',
  buttonBorder: '#000',
  buttonBg: 'rgba(0, 0, 0, 0)',
  buttonBgHover: 'rgba(0, 0, 0, 1)',
};

const themeDark = {
  text: '#fff',
  background: '#121212',
  modalBg: '#202023',
  buttonText: '#fff',
  buttonTextHover: '#000',
  buttonBorder: '#fff',
  buttonBg: 'rgba(255, 255, 255, 0)',
  buttonBgHover: 'rgba(255, 255, 255, 1)',
};
```

Once these are in our `App.js`, go ahead and wrap the entire App with our `<ThemeProvider>`

```javascript
// App.js
return (
  // Wrapped in ThemeProvider
  <ThemeProvider>
    <Container>...</Container>
  </ThemeProvider>
);
```

Next we will alter our `Container`, `Button`, and `Modal` styles to receive props from `theme`:

```javascript
// App.js
const Container = styled.div`
  background: ${(props) => props.theme.background};
  ...
  `;
```

```javascript
// Modal.js
const Modal = styled.div`
  background-color: ${(props) => props.theme.modalBg};
  color: ${(props) => props.theme.text};
  ...
  `;
```

```javascript
// Button.js
export const SecondaryButton = styled(Button)`
  border: 1px solid ${(props) => props.theme.buttonBorder};
  color: ${(props) => props.theme.text};
  ...
  `;
```

Finally, we want to add another `useState` variable to `App.js` in order to invoke the light or dark theme. We also want a button to toggle the state on or off.

```javascript
function App() {

  const getLocalStorage = () => {
    const val = window.localStorage.getItem('isDark');
    return val !== null ? val === 'true' : false;
  }

  const [showModal, setShowModal] = useState(false);
  const [isDark, setIsDark] = useState(getLocalStorage);


  useEffect(() => {
    localStorage.setItem('isDark', isDark);
  }, [isDark])

  return (
    <ThemeProvider theme={isDark ? themeDark : themeLight}>
        <PrimaryButton onClick={() => setIsDark(!isDark)}>
          Change to {isDark ? 'light' : 'dark'} mode
        </PrimaryButton>
        ...
  )
}
```

We should then be able to see the theme toggle from dark to light mode as we click on the toggle theme button.

### In conclusion

Styled-Components removes the need for additional css files and style leaking. It keeps all of your components isolated and style independent and makes updating a particular component much easier.

#### Additional Resources

If you're interested in other CSS in JS libraries, check out [Emotion](https://emotion.sh/docs/introduction) which is very similar to styled-components. Another library for writing styles in JS is [Polished](https://polished.js.org/) and allows for a variety of mixins to dry up your code.
