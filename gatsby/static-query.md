# Static Query (https://www.gatsbyjs.org/docs/static-query/)

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




Las páginas y las templates no tiene que utilizar este enfoque, los componentes si.

En los componentes no se pueden realizar querys a pelo, solo por medio de staticquery



Comentario obtenido en el chat de gatsby:
So the StaticQuery approach is what you want to do! Page and template queries (e.g. files in src/pages or src/templates) are special in that they don't have to use a StaticQuery.