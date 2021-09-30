# IBM-Cloud-Code-Engine-.Net :globe_with_meridians: :computer:

La presente guía está enfocada en el despliegue de una aplicación web basica de ASP.NET Core en Code Engine de *IBM Cloud*.
<br />

## Índice  📰
1. [Pre-Requisitos](#pre-requisitos-pencil)
2. [Crear y ejecutar una aplicación Dotnet localmente](#crear-y-ejecutar-una-aplicación-dotnet-localmente)
3. [Publicar la aplicación ASP.NET Core](#publicar-la-aplicación-aspnet-core)
4. [Crear el archivo Dockerfile](#crear-el-archivo-dockerfile)
5. [Desplegar la aplicación en Code Engine](#desplegar-la-aplicación-en-code-engine)
    * [Opción 1 repositorio público: Comenzar con el código fuente](#opción-1-repositorio-público-comenzar-con-el-código-fuente)
    * [Opción 2 repositorio público: Ejecutar una imagen de contenedor](#opción-2-repositorio-público-ejecutar-una-imagen-de-contenedor)
    * [Opción 3 repositorio privado:](#opción-3-repositorio-privado)
6. [Referencias](#referencias-)
7. [Autores](#autores-black_nib)

## Pre Requisitos :pencil:
* Contar con una cuenta en <a href="https://cloud.ibm.com/"> IBM Cloud</a>.
* Tener instalada la CLI de IBM Cloud.
* Tener instalada la CLI de Docker.
* Tener instalado Git.
* Tener instalado .NET Core SDK.
<br />

## Crear y ejecutar una aplicación Dotnet localmente :envelope_with_arrow:

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

## Publicar la aplicación ASP.NET Core :calling:

Para publicar la aplicación y obtener una DLL autónoma utilice el siguiente comando:
```
dotnet publish -c Release 
```
este proceso debe otorgar un mensaje similar al que se muestra a continuación

<p align="center">
<img width="800" alt="img8" src=https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/6830ab3bec6daedeb3e7fd7a6c698f433dfaf04b/Imagenes/Publish.gif>
</p>

## Crear el archivo Dockerfile :bookmark_tabs:
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

Una vez creado el proyecto y el acceso de registo se puede pasar a desplegar la aplicacion, para esto se tienen distintas opciones dependiendo de la configuracion del repositorio (privado o publico). A continuacion se explican cada una de estas.

### Opción 1 repositorio público: Comenzar con el código fuente 

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

### Opción 2 repositorio público: Ejecutar una imagen de contenedor

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

<p align="center">
<img width="800" alt="img8" src=https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/8c1c32e61f42ec2816c2285844465d3e1fdc0719/Imagenes/ci.gif>
</p>


### Opción 3 repositorio privado:
Con Code Engine, puede agregar acceso a un repositorio de código privado y luego hacer referencia a ese repositorio desde su compilación. Después de crear el acceso a su repositorio de código privado, puede extraer código del repositorio, compilarlo y desplegar una aplicación o trabajo con IBM Cloud ™ Code Engine. Siga los pasos mostrados a continuación:

#### Crear llave SSH y asociarla al repositorio de github

1.	Cree una ssh key para posteriormente asociara a su repositorio privado, para generarla acceda al *IBM Cloud Shell* y coloque el comando:
 
```
ssh-keygen -t rsa -C "user_id"
```

-	Al ejecutar el comando anterior, en la consola se pide que especifique la ubicación, en este caso oprima la tecla Enter para que se guarde en la ubicación sugerida. Posteriormente, cuando se pida la ```Passphrase```oprima la tecla Enter para dejarlo vacio, ya que no se aceptan ssh-keys con passphrase.

-	A continuación muévase con el comando ```cd .ssh``` a la carpeta donde están los archivos ```id_rsa.pub``` y ```id_rsa```. Estos archivos contienen las claves públicas y privadas respectivamente.  

-	Visualice la clave pública y la clave privada, asegúrese de guardarlas, ya que las necesitará próximamente. Utilice el comando:
```
cat id_rsa.pub
```
Y para visualizar la llave privada:
```
cat id_rsa
```
2.	Asocie la clave ssh creada anteriormente a su repositorio de github, para esto complete lo siguiente:
Estando en su repostiorio de github, ingrese a ```settings``` > ```deploy keys```, de click en ```add deploy keys``` e  ingrese lo siguiente en los campos específicos:
* ```Title```: Especifique un titulo único para la llave.
* ```key```: Ingrese la clave pública que guardo previamente.

Por último, de click en ```add key```.

<p align="center">
<img width="800" alt="img8" src=https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/74cf6f0280d64130967bc8477b3d517506c1c6d8/Imagenes/github.gif>
</p>

#### Crear el code repository access

Ingrese a su proyecto en code engine y del menú de la izquierda, elija la opción ```code repo access``` y de click en ```crear```. De la ventana desplegada complete lo siguiente:
* ```Name```: Especifique un nombre único para el acceso.
* ```Code repo server```: Ingrese github.com
* ```ssh private key ```: Ingrese la clave privada que guardo previamente.

Por último, de click en ```create```.

<p align="center">
<img width="800" alt="img8" src=https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/74cf6f0280d64130967bc8477b3d517506c1c6d8/Imagenes/code.gif>
</p>



#### Compilar imagen de referencia
Luego de crear el acceso al repositorio privado de Git este se utiliza para la creación de la imagen, para esto tenga en cuenta los siguientes pasos:

1. En la ventana principal o dasboard de *Code Engine* seleccione el proyecto creado anteriormente.
2. Desde la venta del proyecto de click sobre el botón de ```Image builds/Creación de imágenes``` una vez aquí de click sobre el botón de ```Crear```.
3. Esto lo llevara a una pestaña de configuración, aquí llene la información de la siguiente manera:
   * ```Name```: Ingrese un nombre único para la construcción de la imagen.
   * ```Code repo URL```: Ingrese la URL SSH de su repositorio privado.
   * ```Code repo access```: Seleccione el acceso de repositorio creado anteriormente.
   * ```Branch name```: Ingrese el nombre la rama del repositorio de origen en la cual se encuentra la infoermacion.
   * De click en ```Next```, esto lo llevara a la ventana de configuración de estrategia.
   * Deje la información predeterminada y de click en ```Next```, esto lo llevara a la ventana de configuración de salida.
   * ```Registry server```: Seleccione el servidor en el cual esta desplegado su proyecto, en este caso es ```us.icr.io```.
   * ```Registry access```: Seleccione el acceso de registro creado anteriormente.
   * ```Namespace```: Seleccione un namespace existente o ingrese un nombre con el cual se creará uno automáticamente.
   * ```Repository (image name)```: Ingrese el nombre con el cual se creara la imagen.
   * ```Tag```: Ingrese la etiqueta de la versión de la imagen si aplica.
   * de click en ```Done```
 
Una vez completados estos pasos ya tiene creada la imagen de la aplicación.

#### Despliegue de la aplicación 

Para desplegar la aplicación tenga en cuenta los siguientes pasos:

1. En la ventana principal o dasboard de *Code Engine* seleccione el proyecto creado anteriormente.
2. Desde la venta del proyecto de click sobre el botón de ```Image builds/Creación de imágenes```, seleccione la imagen que acabo de crear anteriormente.
3. En la ventana de la imagen de click sobre el botón ```Submit build```, deje la información predeterminada y de click de nuevo sobre ```Submit build```.
4. Espere unos minutos a que se termine el proceso de despliegue de la aplicación.
5. Para acceder a la aplicación regrese a la ventana del proyecto y de click sobre el botón ```Applications```, allí podrá encontrar la aplicación que se acabo de crear.



 

# Referencias 📖

* [Acerca de IBM Cloud Code Engine](https://cloud.ibm.com/docs/codeengine).

## Autores :black_nib:
Equipo IBM Cloud Tech Sales Colombia.
<br />

     
