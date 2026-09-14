TRABAJO PRACTICO 4

DESCRIPCION

Este trabajo práctico consiste en desarrollar una aplicación web utilizando Node.js, Express y EJS, cuyo objetivo es consultar mascotas disponibles para adopción y permitir agregar temporalmente nuevos registros mediante un formulario.

La aplicación utiliza datos iniciales almacenados en un archivo JSON y genera páginas HTML mediante el motor de plantillas EJS.

Los nuevos registros no se guardan nuevamente en el archivo JSON. Por este motivo, los datos agregados mediante el formulario permanecen disponibles mientras el servidor está funcionando.


INSTALACION

Para instalar y ejecutar el proyecto es necesario tener instalado Node.js.
Primero se debe clonar o descargar el repositorio y acceder a la carpeta del proyecto:

cd tp-04-mascotas-ejs

Luego se deben instalar las dependencias:

npm install

Las principales dependencias utilizadas son:

- express
- ejs
- express-ejs-layouts

También se puede comprobar la sintaxis de los archivos JavaScript mediante:

npm run check

El proyecto utiliza CommonJS y el archivo package.json debe contener el script:

"scripts": {
  "start": "node src/index.js",
  "check": "node --check src/index.js && node --check src/archivos.js"
}

La carpeta node_modules no debe incluirse en el repositorio.


EJECUCIÓN

Una vez instaladas las dependencias, se inicia el servidor mediante:

npm start

El servidor comienza a funcionar después de que se hayan cargado correctamente los datos iniciales desde el archivo JSON.
Una vez iniciado, se puede acceder a la aplicación desde el navegador utilizando la dirección local indicada por el servidor.
Para detener la aplicación se puede presionar:

Ctrl + C
Páginas y rutas

La aplicación cuenta con las siguientes rutas principales:

Método	Ruta	Función
GET	/	Muestra la página inicial
GET	/mascotas	Muestra el listado de mascotas
GET	/mascotas/nueva	Muestra el formulario para agregar una mascota
GET	/mascotas/:id	Muestra el detalle de una mascota
POST	/mascotas	Procesa el formulario y agrega una mascota en memoria

La ruta:

/mascotas/nueva

debe declararse antes de:

/mascotas/:id

para evitar que nueva sea interpretado como un identificador.

La ruta /mascotas recibe el arreglo de mascotas y lo envía a la vista mediante res.render.

La vista recorre el arreglo utilizando forEach y muestra una tarjeta para cada mascota.

Cada tarjeta contiene información como:

Nombre.
Especie.
Edad.
Estado.
Enlace hacia el detalle.

Si el arreglo está vacío, se muestra un mensaje alternativo indicando que no existen mascotas para mostrar.


Detalle:
La ruta /mascotas/:id busca una mascota utilizando su identificador.
Si la mascota existe, se muestran todos sus datos y su imagen.
Si el identificador no existe, la aplicación responde con código HTTP 404 y renderiza la vista:

no-encontrado.ejs

FORMULARIO

La ruta /mascotas/nueva muestra el formulario para crear un nuevo registro.

El formulario se envía mediante:

<form action="/mascotas" method="post">


ESTRUCTURA DE VISTAS

Las vistas se encuentran dentro de la carpeta:

views/

La estructura propuesta es:

views/
├── layouts/
│   └── main.ejs
├── partials/
│   ├── encabezado.ejs
│   └── pie.ejs
├── mascotas/
│   ├── lista.ejs
│   └── detalle.ejs
├── nueva.ejs
├── inicio.ejs
└── no-encontrado.ejs
Layout

El archivo:

views/layouts/main.ejs

funciona como estructura general de las páginas.
Contiene elementos que se comparten entre las diferentes vistas, como:

Declaración HTML.
Idioma español.
Codificación de caracteres.
Configuración del viewport.
Título dinámico.
Enlace al archivo CSS.
Encabezado.
Contenido principal.
Pie de página.

El contenido específico de cada página se incorpora mediante:

<%- body %>
Vistas

Las vistas representan el contenido particular de cada página.

Por ejemplo:

inicio.ejs

contiene el contenido de la página principal, mientras que:

mascotas/lista.ejs

se utiliza para mostrar el catálogo de mascotas.
Las vistas reciben información desde el servidor mediante res.render.

Por ejemplo:

res.render("mascotas/lista", {
    mascotas: mascotas
});

De esta manera, el arreglo mascotas queda disponible dentro de la plantilla EJS.

Los datos de las mascotas se muestran utilizando expresiones escapadas de EJS:

<%= mascota.nombre %>

Esto permite mostrar los valores de forma segura.


PARCIALES

Los parciales son fragmentos de HTML reutilizables.
En este proyecto se utilizan:

partials/encabezado.ejs
partials/pie.ejs

El encabezado contiene la navegación hacia:

Inicio.
Catálogo.
Formulario.

El pie de página se muestra en todas las páginas.
La diferencia entre estos elementos es:

Layout: define la estructura general compartida de las páginas.
Vista: contiene el contenido específico de una página.
Parcial: contiene un fragmento reutilizable, como el encabezado o el pie.
Recursos estáticos

Los recursos estáticos se encuentran dentro de la carpeta:

public/

La estructura es:

public/
├── css/
│   └── estilos.css
├── img/
│   └── mascota.svg
└── js/
    └── app.js

Express utiliza:
app.use(express.static(path.join(__dirname, "..", "public")));
para permitir el acceso a estos archivos desde el navegador.
La función express.static permite servir archivos estáticos como:

Hojas de estilos CSS.
Imágenes.
Archivos JavaScript.
Otros recursos públicos.

Por este motivo, las rutas utilizadas en las páginas no deben incluir /public.
Por ejemplo, el CSS se referencia como:

<link rel="stylesheet" href="/css/estilos.css">

La imagen como:

<img src="/img/mascota.svg">

Y el JavaScript como:

<script src="/js/app.js"></script>

El CSS contiene estilos para la tipografía, colores, navegación, contenedor, grilla adaptable, tarjetas, formulario, botones, mensajes de error y estados de foco.
El archivo app.js contiene al menos un mensaje de consola para comprobar que el recurso JavaScript está funcionando correctamente.


FORMULARIO

El formulario permite agregar temporalmente una nueva mascota.

Los campos requeridos son:

Nombre.
Especie.
Edad.
Estado.
Descripción.



El flujo exitoso es:

Formulario
    ↓
POST /mascotas
    ↓
Validación
    ↓
Agregar mascota al arreglo
    ↓
Redirección
    ↓
GET /mascotas
    ↓
Mostrar nueva tarjeta

La función express.urlencoded permite procesar los datos enviados por formularios HTML:
app.use(express.urlencoded({ extended: false }));


PERSISTENCIA DE DATOS

La aplicación utiliza dos formas diferentes de almacenamiento.

Datos iniciales
Las mascotas iniciales se encuentran en:

datos/mascotas.json

El archivo contiene al menos cinco registros propios de mascotas.
Cada registro posee propiedades como:

id
nombre
especie
edad
descripcion
estado

Los datos son leídos al iniciar el servidor.

Nuevos registros

Las mascotas agregadas mediante el formulario no se escriben en el archivo JSON.
Los nuevos registros se agregan únicamente al arreglo que se encuentra en memoria.
Esto significa que la información agregada permanece disponible mientras el servidor continúa ejecutándose.
Cuando el servidor se detiene y se vuelve a iniciar, el arreglo se vuelve a cargar desde mascotas.json.
Por lo tanto, las mascotas agregadas mediante el formulario desaparecen después de reiniciar el servidor.