# Functional vs class component

La manera más simple para definir un componente React es con una función. 

```javascript
function Welcome(props){
  return <h1> Hello, {props.name} </h1>
}
```

Es solo una función que acepta un parámetros `props`y devuelve un elemento React. Pero también se puede escribir con una sintaxis de clase ES6.

```javascript
class Welcome extends React.Component {
  render(){
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

Ambas versiones son equivalentes y se obtiene la misma salida de ambas. ¿Cúando se debe utilizar una sobre la otra?

## Diferencias

### Sintaxis

Un componente funcional es solo una función plana en Javascript que acepta como argumento `props`  y devuelve un elemento React. La clase sin embargo requiere extender de React.Component y crear una función `render` la cuál devuelve un elemento React.

La opción de la clase requiere de más codificación pero también aporta beneficios.

Mirando el código transpilado por Babel se ven mejor las diferencias.

```javascript
//Código transpilado para el componente funcional
 function Welcome(props) {
   return _react2.default.createElement(
     'h1',
     null,
     'Hello, ',
     props.name
   )
 }
 ```

```javascript
var welcome = function(_React$component) {
  _inherits(Welcome, _React$Component);

  function Welcome() {
    _classCallCheck(this, Welcome);
    return _possibleConstructorRetun(this, (Welcome.__proto__ || Object.getProtoype(Welcome)).aplu(this, arguments));

    _createClass(Welcome, [{
       ...
    }])
  }
}
```

Debido a que el componente funcional es solo una función Javascript, no se puede utilizar `setState()` en el componente. Es debigo a esto por lo que también son llamados components funcionales sin estado, por lo que siempre que se vea un componente funcional  se puede estar seguro que ese componente no tiene su propio estado.

Si se precisa de un estado es necesario crear un componente de clase o pasar un estado a traves del componente padre por medio de `props`

### Ciclo de vida

Otra característica que no está disponible cuando se utilizan components funcionales  son los enganches al ciclo de vida de un componente. La razón es la misma por la que  carecen también de estado y es que todo esto proviene de React.Component

## ¿Por qué utilizar componentes funcionales?

* Los componentes funcionales son más fáciles de leer y de testear porque son funciones javascript sin estado o sin enganches al ciclo de vida.
* Necesitan de menos código.
* Te ayudan a utilizar buenas prácticas. Es más fácil separar los contenedores y los componentes de presentación porque se necesita pensar más sobre el estado del componente si no se tiene acceso a `setState()` en el componente.
* El rendimiento según el equipo de React es mayor.
