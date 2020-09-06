# Guía de despliegue de una APP con Appsody junto con OpenShift

En esta guía encontrará la creación y el despliegue de una aplicación de manera nativa haciendo uso de Appsody y un cluster de OpenShift el cual se encuentra alojado en IBM Cloud.

## Prerequisitos

- Instalar la CLI de Appsody.
- Instalar Docker.
- Instalar la CLI de OpenShift.
- Instalar la CLI de IBM Cloud.

## Creación del Proyecto

En esta sección encontrará los comandos necesarios para la implementación de OpenShift en un proyecto. El primer paso de esta guía es crear un nuevo proyecto, para esto, ejecute el siguiente comando desde una terminal:

```
oc new-project ap
```
Compruebe mediante el siguiente comando que el contexto actual es el espacio de proyecto de su equipo.
```
oc project -q
```
El siguiente paso es acceder al registro interno de Docker, esto debido a que se necesita un lugar para enviar las imágenes de Docker recién creadas por Appsody. OpenShift viene con un registro interno de Docker que puede usar, a través del siguiente comando, pero este registro no está habilitado para el acceso público sino que está disponible para el registro.
```
oc get route --all-namespaces | grep registry
```
Resultado que obtiene es similar al siguiente:
```
oc get route --all-namespaces | grep registry
default    registry-console    registry-console-default.cp4apps-workshop-prop-5290c8c8e5797924dc1ad5d1b85b37c0-0001.us-east.containers.appdomain.cloud
```
A continuación, se acceder al registro interno, para esto mediante el siguiente comando se crea la ruta y se expone.
```
oc create route reencrypt docker-registry --service=docker-registry -n default
```
Una vez esto, ya se puede obtener la URL del registro de Docker.
```
oc get route --all-namespaces | grep registry
```

Resultado que obtiene es similar al siguiente:
```
oc get route --all-namespaces | grep registry
 default    docker-registry    docker-registry-default.cp4apps-workshop-prop-5290c8c8e5797924dc1ad5d1b85b37c0-0001.us-    east.containers.appdomain.cloud
 default    registry-console    registry-console-default.cp4apps-workshop-prop-5290c8c8e5797924dc1ad5d1b85b37c0-0001.us-    east.containers.appdomain.cloud
 ```
 La URL que vamos a utilizar es la correspondiente al docker-registry. La vamos a configurar de la siguiente manera:
 ```
 export IMAGE_REGISTRY=docker-registry-default.openshift-43-ea9753cca330b7f05a99ad5b2c8b5da1-0000.us-east.containers.appdomain.cloud
 ```
Ahora, lo que haremos es guardar las credenciales de Docker para que la conexión sea directa, se logre subir la imagén a Docker Hub y desplegarlo de manera correcta en OpenShift.
```
oc whoami -t| docker login -u $(oc whoami) --password-stdin $IMAGE_REGISTRY
```

## Creación proyecto de Appsody
En esta sección vamos a crear un nuevo directorio para el proyecto y a su vez ejecutarlo. 

Creamos un nuevo directorio para el proyecto y elija una pila de desarrollo. Para ver todas las pilas disponibles, ejecute:
```
mkdir appsody
appsody list
```
Ejecútelo *appsody init <stack>* para descargar la plantilla del proyecto. Para este caso usamos *nodejs-express* para crear un proyecto Appsody completamente funcional:
```
appsody init nodejs-express
```
Finalmente, mediante el siguiente comando iniciamos el contenedor de desarrollo.
```
appsody run
```
