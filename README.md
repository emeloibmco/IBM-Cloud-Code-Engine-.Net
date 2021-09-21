# IBM-Cloud-Code-Engine-.Net

La presente guía está enfocada en el despliegue de una aplicación web basica de ASP.NET Core en Code Engine de *IBM Cloud*.
<br />

## Índice  📰
1. [Pre-Requisitos](#pre-requisitos-pencil)
2. [Crear y ejecutar una aplicación Dotnet localmente](#crear-y-ejecutar-una-aplicación-dotnet-localmente)
3. [Publicar la aplicación ASP.NET Core](#publicar-la-aplicación-aspnet-core)
4. [Crear el archivo Dockerfile](#crear-el-archivo-dockerfile)
5. [Desplegar la aplicación en Code Engine](#desplegar-la-aplicación-en-code-engine)
    * [Opción1: Comenzar con el código fuente](#opción1-comenzar-con-el-código-fuente)
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



<br />

2. En la terminal ejecute el siguiente comando, este compilara e iniciara la aplicación y cada vez que se realicen cambios en el código base de la aplicacion la reconstruirá y reiniciara. 
   
```
dotnet watch run
```
   
luego de ejecutar este comando espere algunos minutos a que la aplicación muestre que está escuchando en http: // localhost: 5000 y que el navegador se inicie                en esa dirección. Para detener este proceso de validación y continuar con la publicación de la aplicación seleccione Ctrl+C para salir y cierre de ventana de la aplicación en el navegador.
   

<br />

## Publicar la aplicación ASP.NET Core

Para publicar la aplicación y obtener una DLL autónoma utilice el siguiente comando:
```
dotnet publish -c Release 
```
este proceso debe otorgar un mensaje similar al que se muestra a continuación

## Crear el archivo Dockerfile
Una vez la aplicación se haya creado y ejecutado es necesario generar una imagen, para esto necesitamos de un archivo que contenga las instrucciones paso a paso para desplegar la imagen en un contenedor y así poder ejecutar la aplicación desde cualquier lugar, este archivo se llama Dockerfile. 

Para crear el archivo, en la terminal de su computador muévase con el comando cd hasta la carpeta creada anteriormente ```<DIRECTORIO>```, una vez en la carpeta ejecute el siguiente comando
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

Antes de desplegar la aplicación en Code Engine es necesario crear un proyecto y un acceso al registro dentro de este proyecto, para esto tenga en cuenta los siguientes pasos:

1. Desde el menú de navegación o menú de hamburguesa seleccione la pestaña ```Code Engine```
2. Si no tiene un proyecto creado de click sobre el botón ```Proyectos```. Esto lo llevara a una nueva ventana, aquí de click sobre el botón ```Crear```

   2.1 En esta ventana ingrese la información necesaria para crear un nuevo proyecto:
      * ```Ubicación/location```: Seleccione la ubicación en la cual desea implementar el proyecto.
      * ```Nombre/Name```: Ingrese un nombre único para el proyecto.
      * ```Grupo de recursos/Resource group```: Seleccione el grupo de recursos sobre el cual desea desplegar el proyecto.
      * ```Etiquetas/tags```: Agregue etiquetas si lo considera necesario.
3. Una vez creado el proyecto de click sobre el nombre del proyecto, esto lo llevara a una nueva ventana. Aquí de click sobre el botón de ```Acceso al registro/Registry access```y de click sobre el botón de ```Crear```.
4. Esto lo llevara a la ventana de configuración para la creación del acceso de registro, antes de completar la información debe crear una clave API. Para ello de click en la pestaña Gestionar y seleccione la opción Acceso (IAM). Posteriormente, de click en Claves API y luego en el botón Crear una clave API de IBM Cloud. Asigne un nombre, una descripción y presione el botón Crear. (No olvide guardar la clave API que acaba de crear, ya que la usará en los pasos siguientes.)
5. Una vez generada la clave API puede terminar la configuración del acceso de registro, para esto tenga en cuenta lo siguiente:
   * En origen de registro seleccione la opción Personalizado.
   * Asigne un nombre a su registro.
   * Como servidor de registro coloque el nombre de dominio teniendo en cuenta la región. En este caso es us.icr.io.
   * En nombre de usuario por defecto debe quedar iamapikey.
   * En contraseña coloque la clave API creada en el ítem 1.
   * Finalice dando click en el botón Crear.


### Opción1: Comenzar con el código fuente

Para desplegar la aplicación en Code Engine mediante el código fuente es necesario tener el código en un repositorio de github, en este caso el código de la aplicación básica generada ya se encuentra en este repositorio. Si se tiene una aplicación distinta se debe generar el repositorio.

para generar la aplicación a partir del código fuente tenga en cuenta los siguientes pasos:

1. Desde el menú de navegación o menú de hamburguesa seleccione la pestaña ```Code Engine```

3. De click sobre el botón de ```Proyectos/Projects``` y seleccione el proyecto creado anteriormente
4. De click sobre el botón de ```Aplicaciones/Appliactions```esto lo llevara a una nueva pestaña, aquí de click sobre el botón de ```Crear```.
5. Esto lo llevara a una pestaña de configuración, aquí llene la información de la siguiente manera:
   * ```Nombre/Name```: Ingrese un nombre para la aplicación.
   * ```Elija el código para ejecutar/Choose the code to run```: Seleccione código fuente o source code
   * ```URL del código fuente/Source code URL```: Ingrese la URL del repositorio, en este caso ingrese
```
https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net
```
   * De click sobre el botón ```Especifique los detalles de la compilación/Specify build details```
      * Esto abrirá una nueva pestaña de configuración 
      * En la pestaña de ```Fuente/Source```deje los valores predeterminados y de click en siguiente.
      * En la pestaña de ```Estrategia/Strategy```seleccione Dockerfile y deje los valores predeterminados, luego de click en siguiente.
      * En la pestaña de ```Salida/output``` seleccione el acceso de registro creado anteriormente e ingrese un nombre para el espacio de trabajo, la imagen y la etiqueta los cuales se generarán automáticamente al desplegar la aplicación, finalmente de click en el botón ```Hecho/Done```.   
   * ```Puerto de escucha```: ingrese el puerto 8080.
   * De click en el boton de ```Crear```.
   * Esto lo llevara a una nueva pestaña en donde deberá esperar algunos minutos hasta que la aplicación se despliegue.


      

     
