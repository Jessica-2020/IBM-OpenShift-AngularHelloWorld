# Guía de despliegue de un HelloWorld con Appsody y OpenShift
---
En esta guía encontrará la creación y el despliegue de una aplicación de manera nativa haciendo uso de Appsody y un cluster de OpenShift, el cual se encuentra alojado en IBM Cloud.

## Prerequisitos

- Instalar Docker.
- Instalar la CLI de Appsody.
- Instalar la CLI de IBM Cloud.
- Instalar la CLI de OpenShift.


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
oc create route reencrypt docker-registry --service=docker-registry -n default port 3079
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

## 2. Creación proyecto de Appsody
En esta sección vamos a crear un nuevo directorio para el proyecto y a su vez ejecutarlo. 

Creamos un nuevo directorio para el proyecto y elegimos una pila de desarrollo. Para ver todas las pilas disponibles, se ejecuta el segundo comando a continuación:
```
mkdir appsody
appsody list
```
Al ejecutar *appsody init <stack>* se descarga la plantilla del proyecto. En este caso usamos *nodejs-express* para crear un proyecto Appsody completamente funcional:
 
```
appsody init nodejs-express
```
Finalmente, mediante el siguiente comando iniciamos el contenedor de desarrollo.
 
```
appsody run
```
---
 
## 3. Despligue de la Aplicacion a Openshift con Appsody

Para realizar el despliegue sobre el cluster de OpenShift es necesario correr el siguiente comando:
 
```
appsody deploy --tag <UserDocker>/<App-Name>:latest --push --namespace <Project-Name>
```
ejemplo:
```
appsody deploy --tag smith2008/demoappsody:latest --push --namespace ap3
```
## 4. Verificar el Despligue de la Aplicacion en el cluster de Openshift 


_Para verificar el despliegue siga los siguintes pasos:_


_1. Ingrese a IBM cloud desde el siguiente link:_
```
https://cloud.ibm.com/login
```
_2. Realice el login con sus credenciales de ingreso._

---
![Captura de pantalla de 2020-03-26 17-25-55](https://user-images.githubusercontent.com/60987042/77702638-f8482580-6f86-11ea-9a83-9714df69ec38.png)
---

_3. Dirijase al resource list._
---
<img width="696" alt="7" src="https://user-images.githubusercontent.com/60987042/76996077-da434b00-691e-11ea-92be-558da48f7d97.PNG">
---


_4. Dirigirse a la sección de clusters._

---
<p align="center">
<img width="668" alt="clus" src="https://user-images.githubusercontent.com/60987042/83417688-13506f00-a3e8-11ea-8d06-69558164a8a0.PNG">
</p>

_5. Ingresar al cluster que lleva por nombre openshift-4.3_

_6. Ingrese a la sección de openshift web console._

<p align="center">
<img width="949" alt="4 3" src="https://user-images.githubusercontent.com/60987042/83417830-40048680-a3e8-11ea-968b-2cda9035accf.PNG">
</p>

_7. En la parte superior busque el proyecto en cual se realizó el despligue, e ingrese a el._

<p align="center">
<img width="960" alt="ap3" src="https://user-images.githubusercontent.com/60987042/92391019-923cfe00-f0e1-11ea-96ca-c0a5bd5f652e.PNG">
</p>

_8. vaya a la sección de la url en la parte superor del circulo que simboliza el pod corriendo y de clic._

<p align="center">
<img width="960" alt="url-ap3" src="https://user-images.githubusercontent.com/60987042/92391029-9537ee80-f0e1-11ea-8c28-b11e3faec377.PNG">
</p>

_9. En el navegador se va a abrir una nueva pestaña con la app corriendo._

<p align="center">
<img width="960" alt="apps" src="https://user-images.githubusercontent.com/60987042/92391035-979a4880-f0e1-11ea-87a2-327701723218.PNG">
</p>

### Referencias de Instalación.
1. <a href="https://docs.docker.com/get-docker/"> Docker Installation.
2. <a href="https://appsody.dev/docs/installing/installing-appsody"> CLI de Appsody.
3. <a href="https://cloud.ibm.com/docs/cli"> CLI de IBM Cloud.
4. <a href="https://cloud.ibm.com/docs/openshift?topic=openshift-openshift-cli&locale=es"> CLI de OpenShift.

