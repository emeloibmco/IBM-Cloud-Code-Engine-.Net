# IBM-Cloud-Code-Engine-.Net

La presente guía está enfocada en el despliegue de una aplicación web basica de ASP.NET Core en Code Engine de *IBM Cloud*.
<br />

## Índice  📰
1. [Pre-Requisitos]
2. [Crear y ejecutar una aplicación Dotnet localmente]
3. [Publicar la aplicación ASP.NET Core]
4. [Crear el archivo Dockerfile]
5. [Desplegar la aplicacion en Code Engine]
    * [Opcion1: Comenzar con el código fuente]
    * [Opcion 2:Ejecutar una imagen de contenedor]
9. [Autores](#autores-black_nib)
<br />

## Pre Requisitos :pencil:
* Contar con una cuenta en <a href="https://cloud.ibm.com/"> IBM Cloud</a>.
* Tener instalada la CLI de IBM Cloud.
* Tener instalada la CLI de Docker.
* Tener instalado Git.
* Tener instalado .NET Core SDK.

## Crear y ejecutar una aplicacion Dotnet localmente

Antes de iniciar con la creacion de la aplicacion es necesario verificar que tenga instalado Dotnet adecuadamente, para esto abra la terminal de su computador y ejecute el sigueinte comando:
```
dotnet --version
```
Luego de esto tenga en ceunta los sigueintes pasos para crear y ejecutar correctamente la aplicacion, en caso de tener una aplicacion creada con anterioridad omita el primer paso.
1. en la terminal de su computador ingrese el siguiente comando:
```
dotnet new webApp -o <DIRECTORIO> --no-https
```
> Nota: ` Reemplace <DIRECTORIO> con el nombre del directorio o carpeta que sera creado y en donde se guardaran los achivos necesarios para la creacion de la aplicacion`


<p align="center"><img width="700" src="https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/ddf97cc26befd9268fa5fe40add910fca560f362/Images/crear.png"></p


2. En la terminal ejecute el sigueinte comando, este compilara e iniciara la aplicacion y cada vez que se realicen cambios en el codigo base de esta la reconstruira y reniniciara. Tenga en cuenta que puede detener la aplicacion en cualquier momento seleccioanndo Ctrl+C.
```
dotnet watch run
```
  luego de ejecutar este comando espere algunos minutos a que la aplicación muestre que está escuchando en http: // localhost: 5000 y que el navegador se inicie en esa dirección. para detener este proceso de validacion y continuar con la publicacion de la aplicacion seleccione Ctrl+C para salir y cierre de ventana de la aplicacion en el navegador.
   
   <p align="center"><img width="700" src="https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/ddf97cc26befd9268fa5fe40add910fca560f362/Images/ejecutar.png"></p

## Publicar la aplicación ASP.NET Core

Para publicar la aplicacion y obtener una DLL autonoma utilice el siguiente comando:
```
dotnet publish -c Release 
```
este proceso debe otorgar un mensaje similar al que se muestra a continuacion 

   <p align="center"><img width="700" src="https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/448011f74cb4f441dcc50053264b50608d80c2e3/Images/Publish.gif"></p
   
   
## Crear el archivo Dockerfile

Una vez la aplicacion se haya creado y ejecutado, es necesario generar una imagen, para esto necesitamos de un archivo que contenga las instrucciones paso a paso para desplegar la imagen en un contenedor y asi poder ejecutar la aplicacion desde cualquier lugar, este archivo se llama Dockerfile. Para crear el archivo tenga en cuenta los siguientes pasos:

1. En la terminal de su computador muevase con el comando cd hasta la carpeta creada anteriormente <DIRECTORIO>, una vez en la carpeta ejecute el siguiente comando
   
```
vi Dockerfile
```
   
Este comando abrira una nueva ventana de edicion aqui puede crear su archivo Dockerfile, si se tiene un archivo predeterminado para una aplicacion especifica ingrese la informacion correspondiente, de lo contrario para el ejemplo de la aplicacion creada en este repositorio ingrese la siguiente inforamcion:

```
FROM mcr.microsoft.com/dotnet/aspnet:5.0
WORKDIR /app1
COPY ./bin/Release/net5.0/publish .
ENTRYPOINT ["dotnet", "myWebApp.dll"]
EXPOSE 8080
ENV ASPNETCORE_URLS=http://*:8080


