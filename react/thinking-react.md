# Pensando en react

Para decidir que es un componente se puede seguir la misma técnica que para crear una función o un nuevo objeto, el principio de responsabilidad unica. Si el componente sigue creciendo, se debe desacoplar en más componentes.

Para hacer la IU interactiva, react proporciona una manera fácil por medio del state.
Para construir la app correctamente, es necesario pensar el conjunto mínimo de información que la aplicación necesita. La clave es no repetirse (DRY, don't repeat yourself). Para ello es necesario averiguar la representación mínima del estado que necesita una aplicación y calcular el resto de lo que se necesita bajo demanda.
Se debe de pensar en todas las piezas de datos de la aplicación. Después ir a través de cada una de ellas para averiguar cuál de ellas es un estado. Para ello simplemente hay que hacerse 3 preguntas sobre la pieza de datos.

1. ¿Es pasada desde el padre por medio de `props`?. Si es así no es un estado.
2. ¿Permanece inalterable a lo largo del tiempo?. Si es así, no es un estado.
3. ¿Se puede calcular en base a estados o props que sean del dominio del componente?. Si es así, no es un estado.

## ¿Dónde debe de vivir un componente?

Una vez decidido que debe de eser un estado se tiene que pensar sobre que componente se tiene que crear.

React trata con flujos de datos unidireccionales a través de la jerarquía de componentes. Para entender que componente debe de poseer el estado se puede seguir los siguiente pasos para cada pieza de estado:

* Identificar cada componente que renderiza algo basado en ese estado.
* Buscar un propietario común (un simple componente superior a todos los componentes que necesita el estado en la jerarquía)
* El propietario común más alto en la jerarquía debe de ser el propietario.
* Si no se encuentra un componente en el que tenga sentido establecer el estado, se crea un nuevo componente para establecer el estado y se añade en algún lugar superior dentro de la escala jerarquíca que componentes que utilicen el estado.

## Flujo inverso de datos

Hasta ahora, todo lo que se ha comentado sigue el flujo de datos hacía abajo en la jerarquía. Ahora se va a estudiar como hacer el flujo de datos de manera inversa, los componentes en lo más profundo de la jerarquía necesitan actualizar el estado que esta en una nivel superior en ella.

React hace este flujo de datos explicito para que sea fácil el entendimiento de como funciona la aplicación, pero requiere algo más de trabajo que el tradicional two-data-binding.

Ya que los componentes solo deben de actualizar su propio estado, el componente pasará un callback al componente que este por debajo en la escala jerarquica, que será lanzado en este componente cuando el estado deba de ser cambiado.


