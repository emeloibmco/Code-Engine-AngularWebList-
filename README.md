# Code-Engine-AngularWebList ☁ 
IBM Cloud Code Engine es una plataforma desarrollada por IBM con el objetivo de ayudarle a crear apps y trabajos modernos, centrados en orígenes y sin servidor. Code Engine abstrae la carga operativa derivada de la creación, el despliegue y la gestión de cargas de trabajo en Kubernetes para que los desarrolladores puedan centrarse en lo que para ellos es más importante: el código fuente. Esta guía está enfocada en el despliegue de una imagen de contendor de la aplicación AngularWebList sobre Code Engine.

## Índice  📰
1. [Pre-Requisitos](#Pre-Requisitos-pencil)
2. [Paso 1. Clonar repositorio AngularWebList](#Paso-1)
3. [Paso 2. Crear archivo Dockerfile](#Paso-2)
4. [Paso 3. Crear imagen de la aplicación en Docker](#Paso-3)
5. [Paso 4. Subir imagen a Container Registry](#Paso-4)
6. [Paso 5. Crear acceso a registro en Code Engine](#Paso-5)
7. [Paso 6. Desplegar imagen dentro de una aplicación en Code Engine](#Paso-6)
8. [Paso 7. Prueba de funcionamiento](#Paso-7)

## Pre-requisitos :pencil:
* Tener una cuenta actualizada en IBM Cloud.
* Contar con un proyecto en Code Engine para hacer el despliegue de una imagen en una aplicación (importante con cuenta actualizada).

## Paso 1.
### Clonar repositorio AngularWebList 📁
La aplicación utilizada en esta guía la puede encontrar en  <a href="https://github.com/emeloibmco/AngularWebList"> AngularWebList</a>. Para clonar el repositorio en su computador, realice los siguientes pasos:

1. En su computador cree una carpeta a la que pueda acceder con facilidad y asignele un nombre relacionado con la aplicación.
2. Abra una ventana de *Windows PowerShell* y dirigase con el comando *cd* hasta la carpeta que creó en el paso 1.
3. Una vez se encuentre dentro de la carpeta creada digite los siguientes comandos para clonar el repositorio:
```
git clone https://github.com/emeloibmco/AngularWebList.git
```
4. Verifique dentro de su carpeta que se encuentran descargados los archivos de la aplicación que se muestran en el repositorio.
<p align="center"><img width="600" src="https://github.com/emeloibmco/Code-Engine-AngularWebList-/blob/main/Imagenes/Clonar%20Repositorio.gif"></p>

## Paso 2.
### Crear archivo Dockerfile 📑
Es importante contar con un archivo conocido como *Dockerfile*, el cual, contiene los comandos o instrucciones que permiten crear la imagen de una aplicación en Docker. Para agregar este archivo a su aplicación, realice los siguientes pasos:

1. Ingrese a la carpeta AngularWebList creada después de clonar el respositorio.
2. De click derecho y cree una arhivo txt con el nombre *Dockerfile.txt*.
3. Dentro del archivo *Dockerfile.txt* agregue las siguientes líneas, correspondientes a las instrucciones necesarias para crear la imagen de la aplicación AngularWebList
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
4. Una vez ha agregado las respectivas instrucciones, es importante que elimine la extensión del archivo. Para ello, abra una ventana de *Windows PowerShell*, dirigase a la  la carpeta que contiene el archivo *Dockerfile.txt* y copie los siguientes comandos:
```
copy Dockerfile.txt Docerfile
```

5. Por último verifique en su carpeta que el archivo Dockerfile se encuentre sin ninguna extensión.
