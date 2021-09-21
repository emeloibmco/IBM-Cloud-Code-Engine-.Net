# IBM-Cloud-Code-Engine-.Net

La presente guía está enfocada en el despliegue de una aplicación web basica de ASP.NET Core en Code Engine de *IBM Cloud*.
<br />

## Índice  📰
1. [Pre-Requisitos](#pre-requisitos-pencil)
2. [Crear y ejecutar una aplicación Dotnet localmente](#crear-y-ejecutar-una-aplicación-dotnet-localmente)
3. [Publicar la aplicación ASP.NET Core](#publicar-la-aplicación-aspnet-core)
4. [Crear el archivo Dockerfile](#crear-el-archivo-dockerfile)
5. [Desplegar la aplicación en Code Engine]
    * [Opción1: Comenzar con el código fuente]
    * [Opción 2: Ejecutar una imagen de contenedor]
9. [Autores](#autores-black_nib)
<br />

## Pre Requisitos: pencil:
* Contar con una cuenta en <a href="https://cloud.ibm.com/"> IBM Cloud</a>.
* Tener instalada la CLI de IBM Cloud.
* Tener instalada la CLI de Docker.
* Tener instalado Git.
* Tener instalado .NET Core SDK.
<br />

## Crear y ejecutar una aplicación Dotnet localmente

Antes de iniciar con la creación de la aplicación es necesario verificar que tenga instalado Dotnet adecuadamente, para esto abra la terminal de su computador y ejecute el siguiente comando:
```
dotnet --version
```
Luego de esto tenga en cuenta los siguientes pasos para crear y ejecutar correctamente la aplicación, en caso de tener una aplicación creada con anterioridad omita el primer paso.
1. En la terminal de su computador ingrese el siguiente comando:
```
dotnet new webApp -o <DIRECTORIO> --no-https
```
> Nota: ` Reemplace <DIRECTORIO> con el nombre del directorio o carpeta que será creado y en donde se guardaran los archivos necesarios para la creación de la aplicación`


<p align="center"><img width="700" src="https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/ddf97cc26befd9268fa5fe40add910fca560f362/Images/crear.png"></p

<br />

2. En la terminal ejecute el siguiente comando, este compilara e iniciara la aplicación y cada vez que se realicen cambios en el código base de esta la reconstruirá y reiniciara. Tenga en cuenta que puede detener la aplicación en cualquier momento seleccionando Ctrl+C.
   
```
dotnet watch run
```
   
luego de ejecutar este comando espere algunos minutos a que la aplicación muestre que está escuchando en http: // localhost: 5000 y que el navegador se inicie                en esa dirección. Para detener este proceso de validación y continuar con la publicación de la aplicación seleccione Ctrl+C para salir y cierre de ventana de la aplicación en el navegador.
   
<p align="center"><img width="700" src="https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/ddf97cc26befd9268fa5fe40add910fca560f362/Images/ejecutar.png"></p

<br />

## Publicar la aplicación ASP.NET Core

Para publicar la aplicación y obtener una DLL autónoma utilice el siguiente comando:
```
dotnet publish -c Release 
```
este proceso debe otorgar un mensaje similar al que se muestra a continuación 

   <p align="center"><img width="700" src="https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/448011f74cb4f441dcc50053264b50608d80c2e3/Images/Publish.gif"></p
   
<br />
      
## Crear el archivo Dockerfile

Una vez la aplicación se haya creado y ejecutado, es necesario generar una imagen, para esto necesitamos de un archivo que contenga las instrucciones paso a paso para desplegar la imagen en un contenedor y así poder ejecutar la aplicación desde cualquier lugar, este archivo se llama Dockerfile. Para crear el archivo, en la terminal de su computador muévase con el comando cd hasta la carpeta creada anteriormente <DIRECTORIO>, una vez en la carpeta ejecute el siguiente comando
   
```
vi Dockerfile
```
   
Este comando abrirá una nueva ventana de edición aquí puede crear su archivo Dockerfile, si se tiene un archivo predeterminado para una aplicación especifica ingrese la información correspondiente, de lo contrario para el ejemplo de la aplicación creada en este repositorio ingrese la siguiente información:

```
FROM mcr.microsoft.com/dotnet/aspnet:5.0
WORKDIR /app1
COPY ./bin/Release/net5.0/publish .
ENTRYPOINT ["dotnet", "myWebApp.dll"]
EXPOSE 8080
ENV ASPNETCORE_URLS=http://*:8080

```
      

<br />

## Desplegar la aplicación en Code Engine
### Opción1: Comenzar con el código fuente

Para desplegar la aplicacion en Code Engine mediante el codigo fuente es necesario tener el codigo en un repositorio de github, en este caso el codigo de la aplicacion basica generada ya se encuentra en este repositorio. Si se tiene una aplicacion distinta se debe generar el repositorio.

para generar la aplicacion a partir del codigo fuente tenga en cuenta los siguientes pasos:
      
1. Desde el menú de navegación o menú de hamburguesa seleccione la pestaña ```Code Engine```
2. Si no tiene un proyecto creado de clik sobre el boton ```Proyectos```. Esto lo llevara a una nueva ventana, aqui de click sobre el boton ```Crear```
   2.1 en esta ventana ingrese la informacion necesaria para crear un nuevo proyecto:
      * ```Ubicacion/location``: Seleccione la ubicación en la cual desea implementar el proyecto.
      * ```Nombre/Name```: Ingrese un nombre unico para el proyecto.
      * ```Grupo de recursos/Resource group```: Seleccione el grupo de recursos sobre el cual desea desplegar el proyecto.
      * ```Etiquetas/tags```: Agregue etiquetas si lo considera necesario.
      
     
