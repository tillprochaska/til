# React

## TypeScript & CSS Custom Properties

In order to set custom properties module augmentation or a type assertion can be used. Augmentations are the preferred way, but type assertions work well if a custom property is only used in a single place:

```tsx
import { CSSProperties } from 'react';

const brandColor = 'pink';

const style: CSSProperties = {
  ['--accent-color' as string]: brandColor,
};

return <div style={style}>...</div>
```

* https://github.com/frenic/csstype#what-should-i-do-when-i-get-type-errors
* https://github.com/DefinitelyTyped/DefinitelyTyped/blob/2af5cda9fd14a9588946f83e8f6955d8aeabf6c6/types/react/index.d.ts#L1572-L1579

## Class vs. Pure vs. Functional Components

React bietet verschiedene Möglichkeiten, Komponenten zu erstellen. Der erste Unterschied zwischen *Class Components*, *Pure Components* und *Functional Components* besteht darin, dass die letzten beiden genannten keinen eigenen, internen Zustand haben, also *stateless* sind. Konkret bedeutet das, dass die gerenderte Komponente ausschließlich von den übergebenen *Props* abhängt.

```js
// Pure Component
class HelloWorld extends React.PureComponent {

  render() {
    return <p>Hello { this.props.name }</p>;
  }

}

// Functional Component
const HelloWorld = props => {
  return <p>Hello { props.name }</p>;
};
```

Class Components können hingegen einen eigenen Zustand haben. Wie die Komponente gerendert wird hängt also nicht nur von den übergebenen Props ab, sondern auch vom internen Zustand der Komponente.

```js
// Stateful Class Component
class Counter extends React.Component {

  state = {
    count: 0,
  };
  
  increment = () => {
    this.setState(prevState => ({
      count: prevState.count + 1,
    }));
  }
  
  render(props) {
    return (
      <button onClick={ this.increment }>
        { this.state.count }
      </button>
    );
  }
  
}
```

Zwischen Pure Components und Functional Components gibt es – über den offensichtlichen, syntaktischen hinausgehende – Unterschiede.

Pure Components werden nur neu gerendert, falls sich die Props verändert wurden. (Damit sind sie im Prinzip Syntactic Sugar für »normale« Class Components, die die `shouldComponentUpdate()`-Methode manuell implementieren.)

Functional Components hingegen werden immer neu gerendert, damit bieten sie keine Vorteile gegenüber »normalen« Class Compoennts. Mittlerweile wurde jedoch die `memo()`-API ergänzt, die es nach dem Vorbild von Pure Components bzw. `shouldComponentUpdate()` auch Functional Components nur bei Bedarf neu zu rendern.

* [React Docs: `PureComponent`](https://reactjs.org/docs/react-api.html#reactpurecomponent)
* [Dan Abramov auf Twitter](https://twitter.com/dan_abramov/status/755343749983657986)
* [`React.memo()` RFC](https://github.com/reactjs/rfcs/blob/gaearon-patch-1/text/0000-memo.md)
