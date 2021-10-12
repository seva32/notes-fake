Rollup es una herramienta Javascript orientada a unir todo el Javascript de nuestra aplicación (generalmente dividido en diferentes ficheros) en un único fichero .js (lo que se conoce como bundler), realizando por el camino ciertas transformaciones de código para mejorar algunos aspectos.

A diferencia de otros automatizadores como Parcel o Webpack, Rollup sigue más un enfoque de configuración manual, similar a Gulp, donde el desarrollador define exactamente lo que quiere conseguir. Esto puede ser algo más complejo para novatos, sin embargo, es mucho más potente y flexible para usuarios avanzados que quieren exactamente casos específicos que son más complicados de conseguir con otras herramientas con procesos más guiados.

Instalación de Rollup 
Para comenzar a utilizar Rollup, debemos crear una carpeta para nuestro proyecto, inicializarlo e instalar Rollup. Realicemos estos pasos desde una terminal para verlo detalladamente:

# Creamos la carpeta de nuestro proyecto (first-project)
$ mkdir first-project

# Accedemos a ella
$ cd first-project

# Inicializamos un proyecto con los datos por defecto
$ npm init -y

# Instalamos Rollup como dependencia de desarrollo
$ npm install -D rollup

# Comprobamos que está correctamente instalado
$ npx rollup --version
rollup v2.39.0
Listo, ya tenemos Rollup instalado en nuestro proyecto, listo para comenzar. Se trata de una herramienta pequeña (apenas unos ~3MB) y muy rápida, por lo que el proceso de instalación será practicamente instantáneo.

Como habrás observado, para utilizar Rollup necesitaremos tener instalado NodeJS, ya que utilizaremos el gestor de paquetes y dependencias NPM. Si no lo tienes instalado, te recomiendo seguir la guía de instalación de Node/NPM.

Preparación del código 
Una vez instalado, lo habitual suele ser crear una estructura de carpetas para nuestro proyecto y colocar el esqueleto de la web. Podemos hacerlo así:

# Comprobamos que estamos en la carpeta raíz del proyecto
$ pwd
/home/manz/workspace/first-project

# Creamos la carpeta src/, que a su vez tendrá dentro assets/ y components/
$ mkdir -p src/{assets,components}

# Creamos los ficheros index.css e index.js en src/
$ touch src/index.{css,js}

# Abrimos VSCode en la carpeta actual (raíz del proyecto)
$ code .
Observa que no hemos creado ningún fichero index.html. En Rollup, al igual que con Webpack (y a diferencia de Parcel), nuestro punto de entrada (entrypoint) no es un fichero index.html, sino un fichero index.js. Las aplicaciones web fuertemente basadas en Javascript, consideran que el punto de entrada es un fichero .js, mientras que el index.html lo consideran simplemente una plantilla externa que se puede copiar o generar.

Si el index.html es muy básico y pequeño, se suele autogenerar. Si va a ser algo más avanzado, se suele crear un fichero a modo de plantilla, y luego en la configuración del Rollup indicar que debe copiarse a la carpeta de destino.

Primeros pasos con Rollup 
Antes de comenzar a trabajar con Rollup debemos crear un fichero de configuración. Generalmente, se llama rollup.config.js y se coloca en la ruta raíz del proyecto. Dicho archivo de configuración debe tener al menos el siguiente contenido:

export default {
  input: "src/index.js",
  output: {
    file: "dist/app.js",
    format: "es"
  },
  plugins: []
}
Vamos a explicar un poco los parámetros que estamos utilizando input, output, file, format, etc. Existen algunos otros parámetros, pero primero nos enfocaremos en estos:

Parámetro	Descripción
input	Fichero de punto de entrada. Generalmente, suele ser src/index.js.
output	Un objeto con parámetros relacionados con el fichero generado de destino.
file	El fichero de destino a generar, que contendrá todo el javascript de la app.
dir	Alternativamente, se puede indicar una carpeta de destino, en lugar de un sólo fichero.
format	El formato Javascript a generar. Puede ser amd, cjs, es, iife, umd o system.
plugins	Array con los plugins de Rollup a utilizar. De momento, vacío. Lo veremos más adelante.
Con el mencionado ejemplo, le decimos a Rollup que queremos examinar el fichero src/index.js, e ir siguiendo los enlaces o referencias a otros archivos que se hagan en él. Rollup irá examinando todos los archivos e irá generando un Javascript de destino en el fichero dist/app.js (indicado en output / file).

Por último, indicamos a Rollup que tipo de formato de código Javascript queremos generar. Las opciónes más comunes suelen ser es (ESM, estándar de Javascript) o cjs (CommonJS, sistema de NodeJS), aunque otras opciones como iife o umd pueden ser útiles en algunas ocasiones. Otras opciones como system o amd son prácticamente legacy hoy en día. Tienes más información en CommonJS vs ES Modules.

Una vez hecho esto, podemos escribir en una terminal el comando npx rollup -c, con el que le decimos a rollup que busque un archivo de configuración y realice el proceso de generación, que recordemos que en nuestro caso es examinar el fichero src/index.js (y todos los archivos que se enlacen desde ahí), y generar un fichero dist/app.js final.

La salida de dicho comando debería ser algo parecido a esto:

$ npx rollup -c

src/index.js → dist/app.js...
(!) Generated an empty chunk
index
created dist/app.js in 8ms
El error (!) Generated an empty chunk aparece porque tenemos el fichero src/index.js sin contenido, y por lo tanto, está generando un dist/app.js de destino que también estará vacío. Esto ocurre porque Rollup por defecto no realiza ninguna tarea, la funcionalidad se suele dar en los plugins que activemos, que de momento no hay ninguno.

Generar index.html con Rollup 
Como hemos dicho, en aplicaciones web, lo habitual suele ser generar o utilizar un HTML como plantilla. Vamos a ver como realizar esto con Rollup y de paso vemos como añadir plugins, aunque profundizaremos en esto más adelante.

El primer paso para añadir plugins a Rollup, suele ser echar un vistazo a los plugins oficiales de Rollup. Si no encontramos lo que buscamos, podemos echar un vistazo a la búsqueda rollup plugin en NPM, donde también nos aparecerán plugins no oficiales.

Para crear la plantilla, nosotros vamos a utilizar el plugin oficial de Rollup llamado @rollup/plugin-html. Para instalarlo, escribimos lo siguiente desde una terminal:

$ npm install -D @rollup/plugin-html
Esto instalará el paquete @rollup/plugin-html en la carpeta node_modules y lo añadirá al package.json. Una vez instalado, editamos el fichero rollup.config.js para añadir y configurar el plugin que acabamos de instalar. En la primera linea importamos la función html del plugin, y ejecutamos dicha función en el array de plugins de Rollup:

import html from "@rollup/plugin-html";

export default {
  input: "src/index.js",
  output: {
    file: "dist/app.js",
    format: "es"
  },
  plugins: [
    html()
  ]
}
Entonces, si volvemos a ejecutar Rollup con npx rollup -c, observaremos que en la carpeta dist se nos autogenera un archivo index.html «de la nada», con una estructura básica de HTML y la etiqueta <script> que hace referencia al archivo de destino, en nuestro caso app.js:

<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Rollup Bundle</title>

  </head>
  <body>
    <script src="app.js" type="module"></script>
  </body>
</html>
Si nos agrada este index.html podemos dejarlo así. Pero si no nos gusta (o queremos personalizarlo más) podemos añadir un objeto de configuración a la función html() que tenemos en el array de plugins de Rollup. Veamos que personalizaciones podemos hacer:

Opción	Descripción
attributes	Indica atributos adicionales para las etiquetas <html>, <link> y <script>.
fileName	Indica el nombre del fichero generado. Por defecto, index.html.
meta	Indica una colección de objetos con las etiquetas <meta> a insertar.
publicPath	Indica un prefijo para añadir a los assets (ficheros) del HTML. Por defecto, sin prefijo.
title	Indica el título del documento HTML. Por defecto, Rollup Bundle.
template	Utiliza una función para personalizar el HTML de la plantilla. Ver más adelante.
Por defecto, si añadimos html() sin parámetro de opciones en el apartado plugins de Rollup, la configuración por defecto que toma sería similar a si hacemos lo siguiente:

  /* ... */
  plugins: [
    html({
      attributes: {
        html: {
          lang: "en"
        },
        link: null,
        script: null    /* Si usamos formato "es", se añade { type: "module" } */
      },
      fileName: "index.html",
      meta: [
        { charset: "utf-8" }
      ],
      publicPath: "",
      title: "Rollup Bundle"
    })
  ]
  /* ... */
Función template (Rollup html) 
Por defecto, la función template de las opciones de Rollup, aunque no se especifique explícitamente, tiene el contenido que veremos a continuación. Se trata de una función que toma por parámetro un objeto, que contiene varias propiedades que son las que se utilizan para crear el index.html y definen la estructura del mismo:

  /* ... */
  template: ({ attributes, bundle, files, publicPath, title }) => `
    <!DOCTYPE html>
    <html ${attributes}>
      <head>
        ${metas}
        <title>${title}</title>
        ${links}
      </head>
      <body>
        ${scripts}
      </body>
    </html>
  `;
  /* ... */
Nosotros podríamos modificar el contenido del  que devuelve esta función, para personalizarlo a nuestro gusto y definir como queremos la estructura del index.html.

Usar plantilla index.html con Rollup 
Si la modalidad anterior no te convence, siempre podremos ir a una estrategia más conservadora y en lugar de generar el index.html, crearlos nosotros mismos, y simplemente copiarlo a la carpeta de destino dist. En esta modalidad, lo único que debemos tener en cuenta es que tenemos que encargarnos personalmente de asegurarnos de que los nombres de los archivos enlazados sean correctos, porque recuerda que en algunos casos, los nombres definitivos suelen cambiar.

Si queremos realizar esta modalidad, en lugar del plugin @rollup/plugin-html que comentamos antes, instalaremos el plugin rollup-plugin-copy. Se trata de un plugin no oficial, pero bastante popular que hace copias de ficheros estáticos al destino que le indiquemos.

Para instalarlo, haremos lo siguiente desde una terminal:

$ npm install -D rollup-plugin-copy
Y modificaremos el fichero de configuración para hacerlo funcionar. Observa que en primer lugar, importaremos la función copy del plugin recién instalado, y luego lo utilizaremos en el  de plugins, pasándole un  de configuración por parámetro, donde indicaremos los ficheros o carpetas que vamos a copiar, y a donde:

import copy from "rollup-plugin-copy";

export default {
  input: "src/index.js",
  output: {
    file: "dist/app.js",
    format: "es"
  },
  plugins: [
    copy({
      targets: [
        { src: "src/index.html", dest: "dist" },
        { src: "src/assets/**/*", dest: "dist/assets" }
      ]
    })
  ]
}
En este ejemplo, copiamos el fichero src/index.html y lo guardamos en dist/, y además, también copiamos todos los archivos de la carpeta src/assets, y al indicar /**/ nos referimos a que también haga lo mismo con todas sus subcarpetas, y los deposite en dist/assets.

Recuerda que si utilizas esta modalidad, como desarrollador eres el responsable de mantener actualizados los nombres de los archivos en el fichero index.html, mientras que el plugin anterior lo hace todo automáticamente.

Ten en cuenta que Rollup solo construye los ficheros Javascript que utilizaremos en la web final, y carece de un servidor local propio, por si queremos ir viendo los cambios en desarrollo antes de subirlos al servidor final. En el siguiente artículo veremos como preparar Rollup como un servidor de desarrollo.
