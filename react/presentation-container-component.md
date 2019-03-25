# Componentes de presentacion y componente contenedores

Los componentes de presentación tiene las siguientes características:

* Lo importante es como se muestrán las cosas.
* Pueden contener a otros componentes de presentación o a componentes contenedores, y normalmente tiene algo de lenguaje de marcado o estilos propios.
* Permiten la contención de `this.props.children`
* No tienen depencias en el resto de la aplicación como pueden ser Redux.
* No especifica como los datos son cargados o se cambian.
* Recibe datos y callback por medio de props.
* Son escritos como componentes funcionales al menos que se necesite estado,  hook del ciclo de vida
* Por ejemplo puede ser : Page, SideBar, Story, UserInfo,List.

```javascript
//defining the component as a React Component
class Image extends Component {
   render() {
      return <img src={this.props.image} />;
   }
}
export default Image

//defining the component as a constant
const Image = props => (
   <img src={props.image} />
)
export default Image
```


Los componenentes contenedores:

* Se preocupan de como funcionan las cosas
* Pueden contener a otros componentes de presentación o a componentes contenedores,y normalmente no tienen algo de lenguaje de marcado y nunca estilos propios.
* Proporcionan información y comportamiento para los componentes de presentación o otros componentes contenedores.
* Llaman a las acciones de Redux.
* Suelen tener estado ya que tienden a servir como fuente de datos.
* Ejemplos: UserPage, FollowerSideBar,StoryContainer, FollowedUserList

Un ejemplo:

```javascript
class Collage extends Component {
   constructor(props) {
      super(props);
      
      this.state = {
         images: []
      };
   }

   componentDidMount() {
      fetch('/api/current_user/image_list')
         .then(response => response.json())
         .then(images => this.setState({images}));
   }

   render() {
      return (
         <div className="image-list">
            {this.state.images.map(image => {
               <div className="image">
                  <img src={book.image_url} />
               </div>
            })}
         </div>
      )
   }
}
```

Los beneficios de este enfoque son:

* Separacion de responsabilidades. Así se entiende mejor la app y su IU.
* Mejora la reusabilidad. Se puede utilizar componentes de presentación con diferentes fuentes de datos.
* Los componentes de presentación son esencialmente la paleta de la app.
* Fuerza a extraer compontes de layout, como son SideBar, Page, ContextMenu, y usar this.props.children  en lugar de duplicar el mismo marcado en otros componentes.


Es recomendable empezar a contruir la app con componentes de presentación inicialmente. Te darás cuenta de que se estan pasando demasiados elementos por los componentes intermedios. Cuando te des cuenta que algunos componentes no usan props que reciben, si no que simplemente las reenvian teniendo que reconectar de nuevo con todos los componentes intermediarios cuando los hijos necesitan mas datos, es buen momento para crear los componentes contendores. De esta manera se puede obtener los datos y las props a los componentes hojas sin sobrecargar los componentes intermedios del árbol.

Es un proceso de continua refactorización del código, asi que es probable que se falle en los primeros intentos. A medida que se tenga más experiencia con este patrón,  el desarrollo será mas intuitivo cuando llegue el tiempo de extraer algunos contenedores.


https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0

#######################################

Tanto los componentes de presentación como los componentes contenedores conforman el árbol de componentes de React. Es necesario que unos componentes tengan la responsabilidad de hacer las cosas, y otros componentes de dibujar la información actual. Donde los componentes de presentación no manejan estados los componentes contenedores lo hacen. Donde los componentes de presentación normalmente son hijos en la jerarquía de componentes de la app,  los componentes contenedores son normalmente los padres de los componentes de representación.


## Que son los componentes contedores?
* Son  principalmente aquellos que se preocupan de como se hacen las cosas.
* Rara vez contienen lenguaje HTML, a parte de un wrapper como `<div>`.
* Contiene casi siempre estado
* Su responsabilidad es proporcionar datos y comportamiento a sus hijos.

Por ejemplo:

```javascript
class Collage extends Component {
   constructor(props) {
      super(props);
      
      this.state = {
         images: []
      };
   }

   componentDidMount() {
      fetch('/api/current_user/image_list')
         .then(response => response.json())
         .then(images => this.setState({images}));
   }

   render() {
      return (
         <div className="image-list">
            {this.state.images.map(image => {
               <div className="image">
                  <img src={book.image_url} />
               </div>
            })}
         </div>
      )
   }
}
```

## Que son los componentes de presentación ?

* Su principal preocupación es como mostrar los datos.
* Probablemente solo contenta un método render o una pequeña lógica.
* No conocen como los datos son obtenidos o alterados.
* No suelen contener estado.

Ejemplo:

```javascript
//defining the component as a React Component
class Image extends Component {
   render() {
      return <img src={this.props.image} />;
   }
}
export default Image

//defining the component as a constant
const Image = props => (
   <img src={props.image} />
)
export default Image
```

Con los componentes de presentación se obtiene la opción de definirlos como compoentes react o como funciones. Definiendolos como funciones ayuda a eliminar dependencias y líneas de código.`




https://medium.com/@yassimortensen/container-vs-presentational-components-in-react-8eea956e1cea