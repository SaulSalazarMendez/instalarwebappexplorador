# Ejemplo de como instalar una web app en el explorador

Primero que nada esto sirve para que las aplicaciones funcionen en modo offline.
Se tinen que tomar en cuenta que una vez que esta cacheada la app la UNICA manera de 
actualizarlo es mediante el cambio de version del serviceworker

## Crear el service worker

Para empezar hay que crear un service worker el cual es una archivo que se mantiene
en ejecución y dando los archivos que se cachean en el explorador.

archiv: worker.js
```javascript
///version del service worker
const version = '1';
//nombre del cache que almacenara los archivos
const grabador = "grabador";
//lista de archivos que se guardaran
const assets = [
  "/",
  "/index.html",
];
// Instalacion de los archivos en el explorador
self.addEventListener("install", installEvent => {
  installEvent.waitUntil(
    caches.open(grabador).then(cache => {
      cache.addAll(assets)
      console.log('add files');
    })
  )
})
// Se regresan los archivos guardados al explorador
self.addEventListener("fetch", fetchEvent => {
  fetchEvent.respondWith(
    caches.match(fetchEvent.request).then(res => {
      return res || fetch(fetchEvent.request);
    })
  )
})
```

**Nota:** HASTA que cambie la versión del service worker se actualizaran los archivos.

## Invocar el service worker

Para instalar el service worker hay que llamarlo desde nuestro archivo principal.

main.js
```javascript
function instalaWorker(){
    navigator.serviceWorker
    .register("./worker.js") // se registra el archivo  de worker.js
    .then(res => console.log("app instalada"))
    .catch(err => console.log("Falló la instalación", err));
}

//validamos que el api de serviceworker este ativa en el explorador
if ("serviceWorker" in navigator) {
    //se instala despues de cargar la página
    window.addEventListener("load", function() {    
        instalaWorker();
    })
} 
```