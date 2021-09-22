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
    * [Opción 2: Ejecutar una imagen de contenedor](#opción-2-ejecutar-una-imagen-de-contenedor)
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

<p align="center">
<img width="800" alt="img8" src=https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/74cf6f0280d64130967bc8477b3d517506c1c6d8/Imagenes/crear.png>
</p>

<br />

2. En la terminal ejecute el siguiente comando, este compilara e iniciara la aplicación y cada vez que se realicen cambios en el código base de la aplicacion la reconstruirá y reiniciara. 
   
```
dotnet watch run
```
   
luego de ejecutar este comando espere algunos minutos a que la aplicación muestre que está escuchando en http: // localhost: 5000 y que el navegador se inicie                en esa dirección. Para detener este proceso de validación y continuar con la publicación de la aplicación seleccione Ctrl+C para salir y cierre de ventana de la aplicación en el navegador.
   
<p align="center">
<img width="800" alt="img8" src=https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/a431cffbaafd5de19afbfce1149bcdcace111e57/Imagenes/ejecutar.png>
</p>
<br />

## Publicar la aplicación ASP.NET Core

Para publicar la aplicación y obtener una DLL autónoma utilice el siguiente comando:
```
dotnet publish -c Release 
```
este proceso debe otorgar un mensaje similar al que se muestra a continuación

<p align="center">
<img width="800" alt="img8" src=https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/6830ab3bec6daedeb3e7fd7a6c698f433dfaf04b/Imagenes/Publish.gif>
</p>

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

<p align="center">
<img width="800" alt="img8" src=https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/a74588f9d4c39b12a0072fbfcf2fb5e219b71fa1/Imagenes/project.gif>
</p>

### Opción1: Comenzar con el código fuente

Para desplegar la aplicación en Code Engine mediante el código fuente es necesario tener el código en un repositorio de github, en este caso el código de la aplicación básica generada ya se encuentra en este repositorio. Si se tiene una aplicación distinta se debe generar el repositorio.

para generar la aplicación a partir del código fuente tenga en cuenta los siguientes pasos:

1. Desde el menú de navegación o menú de hamburguesa seleccione la pestaña ```Code Engine```
2. De click sobre el botón de ```Proyectos/Projects``` y seleccione el proyecto creado anteriormente
3. De click sobre el botón de ```Aplicaciones/Appliactions```esto lo llevara a una nueva pestaña, aquí de click sobre el botón de ```Crear```.
4. Esto lo llevara a una pestaña de configuración, aquí llene la información de la siguiente manera:
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

<p align="center">
<img width="800" alt="img8" src=https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/8edbcc0d5082c170fed5816112df9e7572738ecb/Imagenes/sourcecode.gif>
</p>

### Opción 2: Ejecutar una imagen de contenedor

para desplegar la aplicacion ejecutando una imagen de contenedor debe tener instalada en si computador la CLI de *IBM Cloud*, si cumple con este requisito tenga en cuenta los siguientes pasos:
1. En la terminal de su computador incie sesion en *IBM Cloud*, para esto puede utilizar alguno de estos dos comandos dependiendo de su tipo de cuenta
```
ibmcloud login
```
```
ibmcloud login --sso
```
2. luego de esto seleccione la cuenta en donde se encuentra el proyecto de Code Engine creado anteriormente 
3. Una vez ha iniciado sesión, configure el grupo de recursos y la región que está utilizando para su proyecto de Code Engine. Para ello utilice el siguiente comando:
```
ibmcloud target -r <REGION> -g <GRUPO_RECURSOS>
```
> Nota: `Reemplace <REGION> y <GRUPO_RECURSOS> con su información.`
4. Registre el daemon de Docker local en IBM Cloud Container Registry con el comando:
```
ibmcloud cr login
```
5. Cree un espacio de nombres (namespace) dentro de IBM Cloud Container Registry para su imagen. Para ello ejecute el siguiente comando:
```
ibmcloud cr namespace-add <namespace>
```
> Nota: `Reemplace <namespace> con un nombre fácil de recordar y que esté relacionado con la imagen de la aplicación.`
6. Obtenga informacion e identifique su Container Registry ejecutando el siguiente comando.
```
ibmcloud cr info
```
7. Cree y etiquete (-t) la imagen de Docker ejecutando el siguiente comando, reemplazando REGISTRY (en este caso es us.icr.io), NAMESPACE (es el espacio creado anteriormente), NOMBRE_IMAGEN y TAG (el nombre y el tag que tengra la imagen de la aplicacion en *IBM Cloud Container Registry*) con los valores apropiados.
```
docker build . -t <REGISTRY>/<NAMESPACE>/<NOMBRE_IMAGEN:TAG>
```
> Nota: `Tenga en cuenta que debe reemplazar: REGISTRY (en este caso es us.icr.io), NAMESPACE (es el espacio creado anteriormente), NOMBRE_IMAGEN y TAG (el nombre y el tag que tengra la imagen de la aplicacion en *IBM Cloud Container Registry*) con los valores apropiados.`
8. Envíe la imagen de Docker a su Container Registry en IBM Cloud con el siguiente comando:
```
docker push <REGISTRY>/<NAMESPACE>/<NOMBRE_IMAGEN:TAG>
```
9. Luego de esto verifique que la imagen se haya enviado adecuadamente con el comando:
```
ibmcloud cr image-list
```
<p align="center">
<img width="800" alt="img8" src=https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/341beab81e6c3b73fa4288430e643707d83a749f/Imagenes/ci.png>
</p>

10. Una vez finalizado este proceso ingrese a <a href="https://cloud.ibm.com/"> IBM Cloud</a>. y verifique que en IBM Cloud Container Registry aparezca el espacio de nombres (namespace), el repositorio y la imagen. Tenga en cuenta los nombres que asignó en cada paso.

luego de esto ya puede desplegar la imagen dentro de una aplicación en Code Engine, para esto tenga en cuenta los sigueintes pasos:
1. Dentro del proyecto de Code Engine, seleccione la pestaña que dice Aplicaciones y posteriormente de click en el botón ```Crear```.
2. Asigne un nombre para su aplicación.
3. En la sección ```Elija el código que desea ejecuta/Choose the code to run``` seleccione la opción ```Imagen de contenedor/Container image```.
4. De click en el botón Configurar imagen y coloque lo siguiente:
   * En servidor de registro coloque el nombre de dominio que indicó en la creación del acceso a registro. En este caso es us.icr.io.
   * En acceso a registro seleccione el nombre del acceso que creó anteriormente.
   * Seleccione el espacio de nombres (namespace) que asignó cuando subió la imagen de la aplicación a IBM Cloud Container Registry.
   * Seleccione el repositorio (nombre de la imagen) que asignó en IBM Cloud Container Registry.
   * Seleccione la etiqueta de su imagen.
   * De click en el botón Listo.
5. En el puerto de escucha coloque 8080
6. De click en Crear y espere unos minutos mientras que la imagen se despliega sobre la aplicación que acaba de crear.
     
