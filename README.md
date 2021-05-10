# Code-Engine-AngularWebList ☁ 
*IBM Cloud Code Engine* es una plataforma desarrollada por IBM con el objetivo de ayudarle a crear apps y trabajos modernos, centrados en orígenes y sin servidor. *Code Engine* abstrae la carga operativa derivada de la creación, el despliegue y la gestión de cargas de trabajo en *Kubernetes* para que los desarrolladores puedan centrarse en lo que para ellos es más importante: el código fuente. Esta guía está enfocada en el despliegue de una imagen  de la aplicación AngularWebList sobre *Code Engine*. 

## Índice  📰
1. [Pre-Requisitos](#Pre-Requisitos-pencil)
2. [Paso 1. Clonar repositorio AngularWebList](#Paso-1)
3. [Paso 2. Crear archivo Dockerfile](#Paso-2)
4. [Paso 3. Crear imagen de la aplicación en Docker](#Paso-3)
5. [Paso 4. Subir imagen a IBM Cloud Container Registry](#Paso-4)
6. [Paso 5. Crear acceso a registro en Code Engine](#Paso-5)
7. [Paso 6. Desplegar imagen dentro de una aplicación en Code Engine](#Paso-6)
8. [Paso 7. Prueba de funcionamiento](#Paso-7)

## Pre-requisitos :pencil:
* Tener instalado *Git* en su computador, para clonar el respositorio.
* Tener instalado *Docker Desktop* para verificar la creación de su imagen.
* Tener una cuenta actualizada en <a href="https://cloud.ibm.com/"> IBM Cloud </a>.
* Contar con un proyecto en *Code Engine* para hacer el despliegue de una imagen en una aplicación (importante con cuenta actualizada).
* Contar con la CLI de IBM Cloud en su computador.


## Paso 1.
### Clonar repositorio AngularWebList 📁
La aplicación utilizada en esta guía la puede encontrar en  <a href="https://github.com/emeloibmco/AngularWebList"> AngularWebList</a>. Para clonar el repositorio en su computador, realice los siguientes pasos:

1. En su computador cree una carpeta a la que pueda acceder con facilidad y asignele un nombre relacionado con la aplicación.
2. Abra una ventana de *Windows PowerShell* y dirigase con el comando *cd* hasta la carpeta que creó en el paso 1.
3. Una vez se encuentre dentro de la carpeta creada coloque el siguiente comando para clonar el repositorio:
```
git clone https://github.com/emeloibmco/AngularWebList.git
```
4. Verifique dentro de su carpeta que se encuentran descargados los archivos de la aplicación que se muestran en el repositorio.
<p align="center"><img width="700" src="https://github.com/emeloibmco/Code-Engine-AngularWebList-/blob/main/Imagenes/Clonar%20Repositorio.gif"></p>

## Paso 2.
### Crear archivo Dockerfile 📑
Es importante contar con un archivo conocido como *Dockerfile*, el cual contiene los comandos o instrucciones que permiten crear la imagen de una aplicación en *Docker*. Para agregar este archivo a su aplicación, realice lo siguiente:

1. Ingrese a la carpeta AngularWebList creada después de clonar el respositorio.
2. De click derecho y cree un arhivo de texto con el nombre *Dockerfile.txt*.
3. Dentro del archivo *Dockerfile.txt* agregue las siguientes líneas correspondientes a las instrucciones necesarias para crear la imagen de la aplicación AngularWebList:
```

FROM node:12-alpine AS builder

WORKDIR /app
COPY . .
RUN npm install && npm run build


FROM node:12-alpine

RUN npm install -g serve

COPY --from=builder app/dist/angular-web-app /app

EXPOSE 8080
ENTRYPOINT ["serve", "-l", "8080", "/app"]

```
4. Una vez ha agregado las respectivas instrucciones, es importante que elimine la extensión del archivo. Para ello, abra una ventana de *Windows PowerShell*, dirigase a la carpeta que contiene el archivo *Dockerfile.txt* y copie el siguiente comando:
```
copy Dockerfile.txt Dockerfile
```

5. Verifique en su carpeta que el archivo Dockerfile se encuentre sin ninguna extensión y elimine el archivo *Dockerfile.txt*.
<p align="center"><img width="700" src="https://github.com/emeloibmco/Code-Engine-AngularWebList-/blob/main/Imagenes/Crear%20Dockerfile.gif"></p>

**Nota**: 
Si desea consultar más información acerca de las instrucciones que debe contener el *Dockerfile* para *Code Engine*, puede consultar <a href="https://cloud.ibm.com/docs/codeengine?topic=codeengine-dockerfile"> Writing a Dockerfile for Code Engine </a>.

## Paso 3. 
### Crear imagen de la aplicación en Docker 📲
Para crear la imagen de la aplicación en *Docker* siga los pasos que se muestran a continuación:

1. En la ventaja de *Windows PowerShell* y asegurandose de que se encuentra dentro de la carpeta que contiene los archivos de la aplicación y el *Dockerfile*, coloque el siguiente comando para crear la imagen de su aplicación:
```
docker build -t <nombre_imagen:tag> .
```
> **Nota**: En la variable **nombre_imagen** asigne un nombre con el que pueda identificar la imagen. En la variable **tag** indique la etiqueta de su imagen, por ejemplo v1. Si no desea colocar ninguna etiqueta lo puede hacer (en este caso se tomaría la etiqueta por defecto *lastest*).

2. Una vez finalice el proceso, verifique en *Docker Desktop* que la imagen que acaba de crear aparece en la lista de imágenes (recuerde revisar teniendo en cuenta el nombre y la etiqueta que asignó al crear su imagen. En este caso fue **aplicacion-listas:v1**).
<p align="center"><img width="700" src="https://github.com/emeloibmco/Code-Engine-AngularWebList-/blob/main/Imagenes/Docker%20Desktop%20Image.PNG"></p>


## Paso 4. 
### Subir imagen a IBM Cloud Container Registry 📤
Una vez ha creado la imagen de su aplicación en *Docker*, se debe subir dicha imagen a *IBM Cloud Container Registry* para poder hacer el despliegue en *Code Engine*.
>**Nota**: La imagen también la puede subir a otro registro de imágenes como *Docker Hub*, pero para este caso practico utilizamos *IBM Cloud Container Registry*.

Para subir la imagen creada a *IBM Cloud Container Registry* realice lo siguiente:
1. En la ventana de *Windows PowerShell* y sin salir en ningún momento de la carpeta que contiene los archivos, inicie sesión en su cuenta de *IBM Cloud* con el siguiente comando:
```
ibmcloud login --sso
```

2. Seleccione la cuenta en donde se encuentra el proyecto de *Code Engine* (recuerde los [Pre-requisitos](#Pre-Requisitos-pencil)).
3. Una vez ha iniciado sesión, configure el grupo de recursos y la región que está utilizando para su proyecto de *Code Engine*. Para ello utilice el siguiente comando:
```
ibmcloud target -r <REGION> -g <GRUPO_RECURSOS>
```
>**Nota**: Reemplace \<REGION> y <GRUPO_RECURSOS> con su información.

4. Registre el daemon de Docker local en *IBM Cloud Container Registry* con el comando:
```
ibmcloud cr login
```

5. Cree un espacio de nombres (*namespace*) dentro de *IBM Cloud Container Registry* para su imagen. Para ello ejecute el siguiente comando:
```
ibmcloud cr namespace-add <namespace>
```
>**Nota**: Reemplace \<namespace> con un nombre fácil de recordar y que esté relacionado con la imagen de la aplicación. Un ejemplo puede ser *awlist-final-ns*.

6. Elija un repositorio y una etiqueta con la que pueda identificar su imagen. En este caso, debe colocar la información de la imagen que creó en *Docker* y el espacio de nombres (*namespace*). Coloque el siguiente comando:
```
docker tag <nombre_imagen:tag> us.icr.io/<namespace>/<nombre_imagen:tag>
```
>**Nota**: En el nombre de dominio **us.icr.io**, debe tener en cuenta colocar el dato correcto en base a la región en donde se encuentra su proyecto y grupo de recursos. Para mayor información puede consultar <a href="https://cloud.ibm.com/docs/Registry?topic=Registry-registry_overview#registry_regions"> regiones </a>.

7. Envíe la imagen a *IBM Cloud Container Registry* mediante el comando:
```
docker push us.icr.io/<namespace>/<nombre_imagen:tag>
```
8. Verifique en *IBM Cloud Container Registry* que aparece el espacio de nombres (*namespace*), el repositorio y la imagen. Tenga en cuenta los nombres que asignó en cada paso.
<p align="center"><img width="700" src="https://github.com/emeloibmco/Code-Engine-AngularWebList-/blob/main/Imagenes/CRImagen.PNG"></p>


## Paso 5. 
### Crear acceso a registro en Code Engine 🔐
Para agregar un acceso de registro a *Code Engine* debe realizar lo siguiente:

1. Cree una clave API. Para ello de click en la pestaña **Gestionar** y seleccione la opción **Acceso (IAM)**. Posteriormente, de click en **Claves API** y luego en el botón **Crear una clave API de IBM Cloud**. Asigne un nombre, una descripción y presione el botón **Crear**. 
>**Nota**: No olvide guardar la clave API que acaba de crear, ya que la usará en los pasos siguientes.

2. De click en la lista de recursos, elija *Code Engine* y seleccione el proyecto en el cual va a desplegar la imagen.

3. Dentro del proyecto dirijase a **Acceso de Registro**, de click en el botón **Crear** y coloque lo siguiente:
* En origen de registro seleccione la opción **Personalizado**. 
* Asigne un nombre a su registro.
* Como servidor de registro coloque el nombre de dominio teniendo en cuenta la región. En este caso es **us.icr.io**.
* En nombre de usuario por defecto debe quedar **iamapikey**.
* En contraseña coloque la clave API creada en el ítem 1.
* Finalice dando click en el botón **Crear**.
<p align="center"><img width="450" src="https://github.com/emeloibmco/Code-Engine-AngularWebList-/blob/main/Imagenes/Crear%20Acceso%20de%20Registro.gif"></p>

Si desea consultar más información acerca de los accesos a registro puede dar click en <a href="https://cloud.ibm.com/docs/codeengine?topic=codeengine-add-registry&locale=es#add-registry-access-ce-console"> Adición de acceso de registro a Code Engine </a>.


## Paso 6. 
### Desplegar imagen dentro de una aplicación en Code Engine 🚀
Para desplegar la imagen desde *IBM Cloud Container Registry* en *Code Engine*, realice lo siguiente:

1. Dentro del proyecto de *Code Engine*, seleccione la pestaña que dice **Aplicaciones** y posteriormente de click en el botón **Crear**.
2. Asigne un nombre para su aplicación.
3. En la sección **"Elija el código que desea ejecutar"** seleccione la opción **Imagen de contenedor**.
4. De click en el botón **Configurar imagen** y coloque lo siguiente:
* En servidor de registro coloque el nombre de dominio que indicó en la creación del acceso a registro. En este caso es **us.icr.io**.
* En acceso a registro seleccione el nombre del acceso que creó en el **Paso 5**.
* Seleccione el espacio de nombres (*namespace*) que asignó cuando subió la imagen de la aplicación a *IBM Cloud Container Registry*.
* Seleccione el repositorio (nombre de la imagen) que asignó en *IBM Cloud Container Registry*.
* Seleccione la etiqueta de su imagen.
* De click en el botón *Listo*.

5. En el puerto de escucha coloque **8080**, teniendo en cuenta la configuración establecida en el *Dockerfile* del **Paso 2**.
6. De click en **Crear** y espere unos minutos mientras que la imagen se despliega sobre la aplicación que acaba de crear.
<p align="center"><img width="700" src="https://github.com/emeloibmco/Code-Engine-AngularWebList-/blob/main/Imagenes/Crear-Aplicacion.gif"></p>

## Paso 7. 
### Prueba de funcionamiento 🏆
Una vez ha desplegado la imagen en la aplicación y cuando el estado de la misma sea **preparado**, dirigase a la parte inferior en el **Panel de prueba** y de click en **Enviar solicitud**.
<p align="center"><img width="700" src="https://github.com/emeloibmco/Code-Engine-AngularWebList-/blob/main/Imagenes/Enviar-Solicitud_1.gif"></p>

Si ha configurado todo correctamente debe observar la respuesta de la aplicación sobre un fondo de color verde. Posteriormente vaya a la parte superior y de click en **Abrir URL de la Aplicación** para ver su aplicación funcionando. En una nueva ventana debe salir la aplicación de forma similar a como se muestra a continuación:
<p align="center"><img width="600" src="https://github.com/emeloibmco/Code-Engine-AngularWebList-/blob/main/Imagenes/AngularWebList.PNG"></p>

## Referencias 🔎
Encuentre ejercicios inciales de *Code Engine* en <a href="https://cloud.ibm.com/docs/codeengine?topic=codeengine-getting-started"> Getting started with IBM Cloud Code Engine </a>.

## Autores ✒
* Diana Andrea Espitia Torres.
