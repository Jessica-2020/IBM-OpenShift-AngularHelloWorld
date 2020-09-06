# Guía de despliegue de una APP con Appsody junto con OpenShift

En esta guía encontrará la creación y el despliegue de una aplicación de manera nativa haciendo uso de Appsody y un cluster de OpenShift el cual se encuentra alojado en IBM Cloud.

## Prerequisitos

- Instalar la CLI de Appsody.
- Instalar Docker.
- Instalar la CLI de OpenShift.
- Instalar la CLI de IBM Cloud.

## 1. Creación del Proyecto

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
 La URL que vamos a utilizar es la docker-registry. Se verá así docker-registry-default.*.containers.appdomain.cloud.* Y la configuramos de la siguiente manera:
 ```
 export IMAGE_REGISTRY=docker-registry-default.openshift-43-ea9753cca330b7f05a99ad5b2c8b5da1-0000.us-east.containers.appdomain.cloud
 ```

