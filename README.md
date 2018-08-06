# react-section-demo
[LIVE-DEMO](https://charliephung.github.io/react-section-demo/)

---
### component/Loaded.jsx
```javascript
import React from "react";
const Loaded = props => {
  return (
    <div>
      <h1>I'm loaded 1</h1>
      <h2>Check your network tab</h2>
      <button {...props}>Click me</button>
    </div>
  );
};
export default Loaded;
```
### component/Loaded2.jsx
```javascript
import React from "react";
const Loaded2 = props => {
  return (
    <div>
      <h1>I'm loaded 2</h1>
      <h2>Check your network tab</h2>
      <button {...props}>Click me</button>
    </div>
  );
};
export default Loaded2;
```

### component/Loaded3.jsx
```javascript
import React from "react";
const Loaded2 = props => {
  return (
    <div>
      <h1>I'm loaded 3</h1>
      <h2>Check your network tab</h2>
      <button {...props}>Click me</button>
    </div>
  );
};
export default Loaded2;
```


### App.jsx
```javascript
import React, { Component } from "react";
import Section from "./section/Section";
import "./App.scss";

const navBar = [
  {
    name: "header",
    content: "Header"
  },
  {
    name: "body",
    content: "Body"
  },
  {
    name: "footer",
    content: "Footer"
  }
];

class App extends Component {
  constructor(props) {
    super(props);
    this.section = React.createRef();
    this.state = {
      inViewFlag: "header",
      loadComponent: false
    };
  }

  onClick = (flag, top = 0) => {
    this.section.scrollToFlag(flag, top);
  };
  onLoadComponent = () => {
    this.setState({ loadComponent: true });
  };

  componentDidMount() {
    console.log(this.section);
    // this.section.getFlagsOffSet()
    // this.section.getFlagsPosition()
    // this.section.flagsNode
    // this.section.flagsRef
    // this.section.inViewFlag
    // this.section.scrollToFlag()
    window.addEventListener("scroll", this.onScroll);
  }
  componentWillUnmount() {
    window.removeEventListener("scroll", this.onScroll);
  }
  onScroll = () => {
    const inViewFlag = this.section.inViewFlag({
      // Set offset top equal to nabar height
      offSetTop: 61,
      // Ignore flags which are not in navbar
      ignore: "lazyComponent1 lazyComponent2 lazyComponent3"
    });
    // Setstate if new section is in view
    if (inViewFlag !== this.state.inViewFlag) {
      this.setState({ inViewFlag });
    }
  };

  render() {
    const { inViewFlag, loadComponent } = this.state;
    return (
      <div className="App">
        {/* Navbar */}
        <nav className="nav">
          <ul className=" nav__ul">
            {navBar.map(ele => {
              return (
                <li
                  key={ele.name}
                  onClick={() => this.onClick(ele.name, 60)}
                  className={`nav__item  ${
                    ele.name === inViewFlag ? "active" : ""
                  }`}
                >
                  {ele.content}
                </li>
              );
            })}
          </ul>
        </nav>
        {/* Get section props */}
        <Section onRef={ref => (this.section = ref)}>
          <br />
          <br />
          <br />
          {/* Header */}
          <Section.Flag className="header" flagName="header">
            <h1>Header</h1>
          </Section.Flag>
          <Section.Flag
            lazyLoad={{
              // Loaded delay time
              initProps: {
                className: "before__load"
              },
              delay: 2000,
              loading: () => <h1>I will load after 2s</h1>,
              loaded: () => import("./component/Loaded"),
              loadedProps: { onClick: () => alert("Hi i'm lazy 1") }
            }}
            flagName="lazyComponent1"
            className="after__load"
          />
          {/* Header 2 */}
          <Section.Flag className="body" flagName="body">
            <h1>Body</h1>
            <button onClick={() => this.onLoadComponent()}>
              Click me to load component
            </button>
            {loadComponent && (
              <Section.Flag
                lazyLoad={{
                  // Loaded delay time
                  initProps: {
                    // Hide loading state if you want :)
                    className: "hide"
                  },
                  delay: 0,
                  loading: () => <h1>I'm hidden</h1>,
                  loaded: () => import("./component/Loaded2"),
                  loadedProps: { onClick: () => alert("Hi i'm lazy 2") }
                }}
                flagName="lazyComponent3"
                className="after__load"
              />
            )}
          </Section.Flag>
          <Section.Flag
            lazyLoad={{
              // Initprops
              initProps: {
                className: "before__load"
              },
              // Component will be loaded when appear in to viewport
              loadOnView: true,
              //Component will be loaded when scroll down 300px from it
              fromBottom: 300,
              loading: () => <h1>Scroll down 300px from me to load</h1>,
              loaded: () => import("./component/Loaded3"),
              loadedProps: {
                onClick: () => alert("Hi i'm lazy 2")
              }
            }}
            flagName="lazyComponent2"
            className="after__load"
          />
          <Section.Flag className="footer" flagName="footer">
            <h1>Footer</h1>
            <h2>Check out below</h2>
            <button onClick={() => this.onClick("header", 60)}>
              Back To Header
            </button>
          </Section.Flag>
        </Section>
      </div>
    );
  }
}

export default App;
```

### Scss file
> h1, h2, h3, h4 {
>    margin: 0;
>}
>.nav {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 60px;
    background-color: #eee;
    &__ul {
        display: flex;
        list-style: none;
        align-items: baseline;
    }
    &__item {
        padding: 0 1rem;
        cursor: pointer;
        &:hover {
            background-color: #fff
        }
    }
}

>.active {
    background-color: #fff
}

>.hide {
    opacity: 0;
}

>.App {
    width: 1000px;
    margin: auto;
}

>body {
    font-size: 20px;
}

>.header {
    height: 40vh;
    padding: 2rem;
    background-color: lightgreen;
}

>.body {
    padding: 2rem;
    height: 50vh;
    background-color: lightgray
}

>.footer {
    height: 95vh;
    padding: 2rem;
    background-color: lightyellow;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    align-items: flex-start;
}

>.before__load {
    padding: 2rem;
    height: 30vh;
    background-color: lightpink;
}

>.after__load {
    padding: 2rem;
    height: 30vh;
    background-color: lightsalmon;
    animation: move-in-left 2s;
}

>@keyframes move-in-left {
    0% {
        transform: translateX(200%);
        opacity: 0;
    }
    90% {
        transform: translateX(-2%);
        opacity: .9;
    }
    100% {
        transform: translateX(0);
        opacity: 1;
    }
}
