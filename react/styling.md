# Styling

## Estilos globales

Para utilizar estilos globales  se debe de utilizar el tag `global` en un fichero css acompañado entre paréntesis  del nombre de la clase que se quiere hacer global. Ejemplo

```css
:global(.no-margin-bottom) {
  background-color: red;
  margin-bottom: 0px !important;
}
```

La clase `.no-margin-bottom` debe de estar definida previamente

```css
.no-margin-bottom {
  background-color: blue;
}
```

Teniendo más relevancía si colisionan estilos el estilo definido en la clase que no tiene la etiqueta `global`