# GraphQL

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
