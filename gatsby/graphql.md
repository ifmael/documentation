# GraphQL 

## GraphiQL

GraphiQL es una interfaz gráfica para realizar consultas a graphQL. Para acceder a ella desde cuando se esta ejecutando el servidor de gatsby es con la dirección http:\\localhost:8000/___graphql. Para lanzar una consulta se realiza por medio de `Ctrl + Enter` y para autocompletado `Ctrl + space`.

Los datos en gatsby pueden venir de muchas formas: API's, database, CMS, ficheros locales, etc ...  Los plugins de datos proporcionan una manera de obtener los datos de las fuentes. A partir de las fuentes desde graphiQL podremos consultar la información relativa a ella.

La manera habitual de proceder es ir probando consultas en graphiQL y una vez que se haya encontrado la query deseada, copiarla en la página React para empezar a construir la IU. Así un ejemplo sería:

```javascript
import React from "react"
import { graphql } from "gatsby"
import Layout from "../components/layout"

export default ({ data }) => {
  console.log(data)
  return (
    <Layout>
      <div>Hello world</div>
    </Layout>
  )
}

export const query = graphql`
  query {
    allFile {
      edges {
        node {
          relativePath
          prettySize
          extension
          birthTime(fromNow: true)
        }
      }
    }
  }
`
```

Con el `console.log(data)` se podrán visualizar los ficheros que se tenga configurados con el plugin de ficheros locales, pudiendo ser mostrados estos datos dentro del renderizado de la vista.


## Tranformer plugins

Por lo normal el formato de los datos que se obtienen de los plugins de datos no es el deseado, por ejemplo, el plugin del sistema de ficheros permite saber información sobre los ficheros pero no la información dentro del fichero. Para poder realizar este tipo de cosas, gatsby soporta plugings los cuales obtienen información en bruto y la transforman en algo más usable. Un ejemplo de esto pueden ser los ficheros markdown, los cuales son bueno para escribir pero no para construir una web, por lo que será necesario transformar el texto del fichero markdown en HTML. Un ejemplo de una query para obtener todos los ficheros markdown e información relativa a ellos, sería:

```javascript
{
  allMarkdownRemark {
    totalCount
    edges {
      node {
        id
        frontmatter {
          title
          date(formatString: "DD MMMM, YYYY")
        }
        excerpt
        timeToRead
        html
      }
    }
  }
}
```


## Crear nuevos páginas y slug(algo paracido a path)

### Crear slug

Normalmente los datos proporcionan un slug para el contenido. Por ejemplo cuando se trabaja con CMS no es necesario proporcionar path directamente como con markdown.

Para crear páginas a partir de markdown, es necesario utilizar las APIs `onCreateNode` y `createPages`.  Para implementar la API, se exporta el nombre de la la funcióna implementar desde `gatsby-node.js`.

```javascript
exports.onCreateNode = ({ node }) => {
  console.log(node.internal.type)
}
```

Esta función será llamada por gatsby cuando un nuevo nodo es creado o actualizado.

Ahora se modifica la función para añadir path a los ficheros makrdown y crear así sus páginas.

Primero se filta por los nodos que sean markdown

```javascript
exports.onCreateNode = ({ node }) => {
  if (node.internal.type === 'markdownRemark') {
    console.log(node.internal.type);
  }
}
```

Para crear el path por cada fichero por cada markdown, se va a utiliar el nombre de estos ficheros como path. Para poder realizar esto es necesario obtener el nodo padre puesto que el nodo Files, contiene los datos necesarios sobre el fichero en el sistema. Cuando se arranca el servidor, se debería de ver en la terminal el path de los ficheros markdown.

```javascript
exports.onCreateNode = ({ node, getNode }) => {
  if (node.internal.type === `MarkdownRemark`) {
    const fileNode = getNode(node.parent)
    console.log(`\n`, fileNode.relativePath)
  }
}
```

El plugin `gatsby-source-filesystem` puede facilitar lo anteriormente  hecho.

```javascript
const { createFilePath} = require(`gatsby-source-filesystem`)

export.onCreate = ({node, getNode}) => {
  if (node.internal.type  === 'MarkdownRemark' ){
    console.log( createFilePath( { node, getNode, basePath: `pages` }))
  }
}
```

La función `createFilePath` se encarga de encontrar el nodo padre para crear el path.

Ahora ya se puede crear un nuevo slug  en los nodos `MarkdownRemark`. De esta manera cualquier información que se añada a los nodos esta disponible para despues consultar por medio de GraphQL.

Para obtener el slug, se utiliza la función `createNodeField`. Esta función permite crear un campo adicional al nodo creado. Solo el creador original del nodo puede modificar directamente el nodo.

```javascript
const { createFilePath } = require(`gatsby-source-filesystem`)

exports.onCreateNode = ({ node, getNode, actions}) => {
  if (node.internal.type === 'MarkdownRemark') {
    const { createNodeField } = actions
    const slug = createFilePath({ node, getNode, basePath: `markdown` })
    createNodeField({
      node,
      name: `slug`,
      value: slug
    })
  }
}
```

Ahora cuando se realiza una consulta a GraphQL se puede obtener el nombre del slug creado

```json
{
  allMarkdownRemark {
    edges {
      node {
        fields {
          slug
        }
      }
    }
  }
}
```

### Crear páginas

Para crear páginas programativamente se tiene que :

* Consultar los datos con GrapQL
* Mapear los resultados en páginas.

Para realizar el primer paseo se tiene que añadir en el fichero `gatsby-node.js` lo siguiente para poder crear las págins. Se utiliza la función `graphql` para consultar los slug que se han creado con anterioridad.

```javascript
exports.createPages = ({ graphql, actions}) => {
  return graphql(`
  {
    allMarkdownRemark {
      edges {
        node{
          fields{
            slug
          }
        }
      }
    }
  }
  `).then( result => {
    console.log(JSON.stringify(result, null, 4))
  });
}
```

Con esto se añade uuna implementación para la API `createPages` la cúal es llamada por gatsby para que los plugins puedn añadir páginas.

Se necesita proporcionar un template componente al slug para crear la página.

```javascript
const path = require('path');
const { createFilePath } = require(`gatsby-source-filesystem`)

exports.onCreateNode = ({ node, getNode, actions}) => {
  if (node.internal.type === 'MarkdownRemark') {
    const { createNodeField } = actions
    const slug = createFilePath({ node, getNode, basePath: `markdown` })
    createNodeField({
      node,
      name: `slug`,
      value: slug
    })
  }
}

exports.createPages = ({ graphql, actions }) => {
  const { createPage } = actions
  return graphql(`
    {
      allMarkdownRemark {
        edges {
          node {
            fields {
              slug
            }
          }
        }
      }
    }
  `).then(result => {
    result.data.allMarkdownRemark.edges.forEach(({ node }) => {
      createPage({
        path: node.fields.slug,
        component: path.resolve(`./src/components/layout.js`),
        context: {
          slug: node.fields.slug,
        },
      }) 
    })
  })
}
```

El siguiente paso es recoger los datos de nuestros ficheros markdown. Para ello se tiene que modificar el template.

```javascript

```

## SiteMetaData

El lugar para guardar información común en la página como puede ser el título, items del menu, etc... es el objeto `siteMetaData` dentro del fichero `gatsby-config.js`. Así por ejemplo antes de especificar cualquier configuración de plugin u otra cosa se puede definir este objeto:

```javascript
module.exports = {
  siteMetadata: {
    title: `Title from siteMetadata`,
  },
  plugins: [
    `gatsby-plugin-emotion`,
    {
      resolve: `gatsby-plugin-typography`,
      options: {
        pathToConfigModule: `src/utils/typography`,
      },
    },
  ],
}
```

Ahora para poder hacer referencia a la cadena referida por title dentro de un componente, se tiene que importar `graphql` y utilizar el objeto `data.site.siteMetadata` y añadir la propiedad que se quiera, en este caso quedaría así `data.site.siteMetadata.title`. Pero para poder utilizar este objeto se tiene que realiar una query con `graphql`. Un ejemplo sería:

```javascript
import React from "react"
import { graphql } from "gatsby"

export default ({ data }) => (
  <h1>{data.site.siteMetadata.title}</h1> 
)

export const query = graphql`
  query {
    site {
       siteMetadata {
         title
        }
      }
  }`;
```



## StaticQuery

Es una nueva API que en permite en los componentes que no son para una página, como puede ser un `Layout`, obtener datos por medio de consultas a  graphQL. Para ello se añade `<StaticQuery />`  al componente que se desee, y se añade la referencía estática, por ejemplo, `data.site.siteMetadata.title`.

```javascript
import React from "react"
import { StaticQuery, Link, graphql } from "gatsby"

export default ({ children }) => (
  <StaticQuery
    query={graphql`
      query {
        site {
          siteMetadata {
            title
          }
        }
      `}
    render={data => (
      <h1>{data.site.siteMetadata.title}</h1>
      {children}
    )}
  />
```

Esto es un ejemplo simple a las query types, como se formatean y donde se pueden usar. Solo se pueden usar páginas, no en componentes.

