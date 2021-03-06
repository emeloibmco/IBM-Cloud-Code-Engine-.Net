# IBM-Cloud-Code-Engine-.Net :globe_with_meridians: :computer:

La presente gu铆a est谩 enfocada en el despliegue de una aplicaci贸n web basica de ASP.NET Core en Code Engine de *IBM Cloud*.
<br />

## 脥ndice  馃摪
1. [Pre-Requisitos](#pre-requisitos-pencil)
2. [Crear y ejecutar una aplicaci贸n Dotnet localmente](#crear-y-ejecutar-una-aplicaci贸n-dotnet-localmente)
3. [Publicar la aplicaci贸n ASP.NET Core](#publicar-la-aplicaci贸n-aspnet-core)
4. [Crear el archivo Dockerfile](#crear-el-archivo-dockerfile)
5. [Desplegar la aplicaci贸n en Code Engine](#desplegar-la-aplicaci贸n-en-code-engine)
    * [Opci贸n 1 repositorio p煤blico: Comenzar con el c贸digo fuente](#opci贸n-1-repositorio-p煤blico-comenzar-con-el-c贸digo-fuente)
    * [Opci贸n 2 repositorio p煤blico: Ejecutar una imagen de contenedor](#opci贸n-2-repositorio-p煤blico-ejecutar-una-imagen-de-contenedor)
    * [Opci贸n 3 repositorio privado en github](#opci贸n-3-repositorio-privado-en-github)
    * [Opci贸n 4 Repositorio privado en azure](#opci贸n-4-repositorio-privado-en-azure)
6. [Referencias](#referencias-)
7. [Autores](#autores-black_nib)

## Pre Requisitos :pencil:
* Contar con una cuenta en <a href="https://cloud.ibm.com/"> IBM Cloud</a>.
* Tener instalada la CLI de IBM Cloud.
* Tener instalada la CLI de Docker.
* Tener instalado Git.
* Tener instalado .NET Core SDK.
<br />

## Crear y ejecutar una aplicaci贸n Dotnet localmente :envelope_with_arrow:

Antes de iniciar con la creaci贸n de la aplicaci贸n es necesario verificar que tenga instalado Dotnet adecuadamente, para esto abra la terminal de su computador y ejecute el siguiente comando:
```
dotnet --version
```
Luego de esto tenga en cuenta los siguientes pasos para crear y ejecutar correctamente la aplicaci贸n, en caso de tener una aplicaci贸n creada con anterioridad omita el primer paso.
1. En la terminal de su computador ingrese el siguiente comando:
```
dotnet new webApp -o <DIRECTORIO> --no-https
```
> Nota: ` Reemplace <DIRECTORIO> con el nombre del directorio o carpeta que ser谩 creado y en donde se guardaran los archivos necesarios para la creaci贸n de la aplicaci贸n`

<p align="center">
<img width="800" alt="img8" src=https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/74cf6f0280d64130967bc8477b3d517506c1c6d8/Imagenes/crear.png>
</p>

<br />

2. En la terminal ejecute el siguiente comando, este compilara e iniciara la aplicaci贸n y cada vez que se realicen cambios en el c贸digo base de la aplicacion la reconstruir谩 y reiniciara. 
   
```
dotnet watch run
```
   
luego de ejecutar este comando espere algunos minutos a que la aplicaci贸n muestre que est谩 escuchando en http: // localhost: 5000 y que el navegador se inicie                en esa direcci贸n. Para detener este proceso de validaci贸n y continuar con la publicaci贸n de la aplicaci贸n seleccione Ctrl+C para salir y cierre de ventana de la aplicaci贸n en el navegador.
   
<p align="center">
<img width="800" alt="img8" src=https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/a431cffbaafd5de19afbfce1149bcdcace111e57/Imagenes/ejecutar.png>
</p>
<br />

## Publicar la aplicaci贸n ASP.NET Core :calling:

Para publicar la aplicaci贸n y obtener una DLL aut贸noma utilice el siguiente comando:
```
dotnet publish -c Release 
```
este proceso debe otorgar un mensaje similar al que se muestra a continuaci贸n

<p align="center">
<img width="800" alt="img8" src=https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/6830ab3bec6daedeb3e7fd7a6c698f433dfaf04b/Imagenes/Publish.gif>
</p>

## Crear el archivo Dockerfile :bookmark_tabs:
Una vez la aplicaci贸n se haya creado y ejecutado es necesario generar una imagen, para esto necesitamos de un archivo que contenga las instrucciones paso a paso para desplegar la imagen en un contenedor y as铆 poder ejecutar la aplicaci贸n desde cualquier lugar, este archivo se llama Dockerfile. 

Para crear el archivo, en la terminal de su computador mu茅vase con el comando cd hasta la carpeta creada anteriormente ```<DIRECTORIO>```, una vez en la carpeta ejecute el siguiente comando
```
vi Dockerfile
```  
   
Este comando abrir谩 una nueva ventana de edici贸n aqu铆 puede crear su archivo Dockerfile, si se tiene un archivo predeterminado para una aplicaci贸n especifica ingrese la informaci贸n correspondiente, de lo contrario para el ejemplo de la aplicaci贸n creada en este repositorio ingrese la siguiente informaci贸n:

```
FROM mcr.microsoft.com/dotnet/aspnet:5.0
WORKDIR /app1
COPY ./bin/Release/net5.0/publish .
ENTRYPOINT ["dotnet", "myWebApp.dll"]
EXPOSE 8080
ENV ASPNETCORE_URLS=http://*:8080

```
      

<br />

## Desplegar la aplicaci贸n en Code Engine 

Antes de desplegar la aplicaci贸n en Code Engine es necesario crear un proyecto y un acceso al registro dentro de este proyecto, para esto tenga en cuenta los siguientes pasos:

1. Desde el men煤 de navegaci贸n o men煤 de hamburguesa seleccione la pesta帽a ```Code Engine```
2. Si no tiene un proyecto creado de click sobre el bot贸n ```Proyectos```. Esto lo llevara a una nueva ventana, aqu铆 de click sobre el bot贸n ```Crear```

   2.1 En esta ventana ingrese la informaci贸n necesaria para crear un nuevo proyecto:
      * ```Ubicaci贸n/location```: Seleccione la ubicaci贸n en la cual desea implementar el proyecto.
      * ```Nombre/Name```: Ingrese un nombre 煤nico para el proyecto.
      * ```Grupo de recursos/Resource group```: Seleccione el grupo de recursos sobre el cual desea desplegar el proyecto.
      * ```Etiquetas/tags```: Agregue etiquetas si lo considera necesario.
3. Una vez creado el proyecto de click sobre el nombre del proyecto, esto lo llevara a una nueva ventana. Aqu铆 de click sobre el bot贸n de ```Acceso al registro/Registry access```y de click sobre el bot贸n de ```Crear```.
4. Esto lo llevara a la ventana de configuraci贸n para la creaci贸n del acceso de registro, antes de completar la informaci贸n debe crear una clave API. Para ello de click en la pesta帽a Gestionar y seleccione la opci贸n Acceso (IAM). Posteriormente, de click en Claves API y luego en el bot贸n Crear una clave API de IBM Cloud. Asigne un nombre, una descripci贸n y presione el bot贸n Crear. (No olvide guardar la clave API que acaba de crear, ya que la usar谩 en los pasos siguientes.)
5. Una vez generada la clave API puede terminar la configuraci贸n del acceso de registro, para esto tenga en cuenta lo siguiente:
   * En origen de registro seleccione la opci贸n Personalizado.
   * Asigne un nombre a su registro.
   * Como servidor de registro coloque el nombre de dominio teniendo en cuenta la regi贸n. En este caso es us.icr.io.
   * En nombre de usuario por defecto debe quedar iamapikey.
   * En contrase帽a coloque la clave API creada en el 铆tem 1.
   * Finalice dando click en el bot贸n Crear.

<p align="center">
<img width="800" alt="img8" src=https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/a74588f9d4c39b12a0072fbfcf2fb5e219b71fa1/Imagenes/project.gif>
</p>

Una vez creado el proyecto y el acceso de registo se puede pasar a desplegar la aplicacion, para esto se tienen distintas opciones dependiendo de la configuracion del repositorio (privado o publico). A continuacion se explican cada una de estas.

### Opci贸n 1 repositorio p煤blico: Comenzar con el c贸digo fuente 

Para desplegar la aplicaci贸n en Code Engine mediante el c贸digo fuente es necesario tener el c贸digo en un repositorio de github, en este caso el c贸digo de la aplicaci贸n b谩sica generada ya se encuentra en este repositorio. Si se tiene una aplicaci贸n distinta se debe generar el repositorio.

para generar la aplicaci贸n a partir del c贸digo fuente tenga en cuenta los siguientes pasos:

1. Desde el men煤 de navegaci贸n o men煤 de hamburguesa seleccione la pesta帽a ```Code Engine```
2. De click sobre el bot贸n de ```Proyectos/Projects``` y seleccione el proyecto creado anteriormente
3. De click sobre el bot贸n de ```Aplicaciones/Appliactions```esto lo llevara a una nueva pesta帽a, aqu铆 de click sobre el bot贸n de ```Crear```.
4. Esto lo llevara a una pesta帽a de configuraci贸n, aqu铆 llene la informaci贸n de la siguiente manera:
   * ```Nombre/Name```: Ingrese un nombre para la aplicaci贸n.
   * ```Elija el c贸digo para ejecutar/Choose the code to run```: Seleccione c贸digo fuente o source code
   * ```URL del c贸digo fuente/Source code URL```: Ingrese la URL del repositorio, en este caso ingrese
```
https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net
```
   * De click sobre el bot贸n ```Especifique los detalles de la compilaci贸n/Specify build details```
      * Esto abrir谩 una nueva pesta帽a de configuraci贸n 
      * En la pesta帽a de ```Fuente/Source```deje los valores predeterminados y de click en siguiente.
      * En la pesta帽a de ```Estrategia/Strategy```seleccione Dockerfile y deje los valores predeterminados, luego de click en siguiente.
      * En la pesta帽a de ```Salida/output``` seleccione el acceso de registro creado anteriormente e ingrese un nombre para el espacio de trabajo, la imagen y la etiqueta los cuales se generar谩n autom谩ticamente al desplegar la aplicaci贸n, finalmente de click en el bot贸n ```Hecho/Done```.   
   * ```Puerto de escucha```: ingrese el puerto 8080.
   * De click en el boton de ```Crear```.
   * Esto lo llevara a una nueva pesta帽a en donde deber谩 esperar algunos minutos hasta que la aplicaci贸n se despliegue.

<p align="center">
<img width="800" alt="img8" src=https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/8edbcc0d5082c170fed5816112df9e7572738ecb/Imagenes/sourcecode.gif>
</p>

### Opci贸n 2 repositorio p煤blico: Ejecutar una imagen de contenedor

para desplegar la aplicacion ejecutando una imagen de contenedor debe tener instalada en si computador la CLI de *IBM Cloud*, si cumple con este requisito tenga en cuenta los siguientes pasos:
1. En la terminal de su computador incie sesion en *IBM Cloud*, para esto puede utilizar alguno de estos dos comandos dependiendo de su tipo de cuenta
```
ibmcloud login
```
```
ibmcloud login --sso
```
2. luego de esto seleccione la cuenta en donde se encuentra el proyecto de Code Engine creado anteriormente 
3. Una vez ha iniciado sesi贸n, configure el grupo de recursos y la regi贸n que est谩 utilizando para su proyecto de Code Engine. Para ello utilice el siguiente comando:
```
ibmcloud target -r <REGION> -g <GRUPO_RECURSOS>
```
> Nota: `Reemplace <REGION> y <GRUPO_RECURSOS> con su informaci贸n.`
4. Registre el daemon de Docker local en IBM Cloud Container Registry con el comando:
```
ibmcloud cr login
```
5. Cree un espacio de nombres (namespace) dentro de IBM Cloud Container Registry para su imagen. Para ello ejecute el siguiente comando:
```
ibmcloud cr namespace-add <namespace>
```
> Nota: `Reemplace <namespace> con un nombre f谩cil de recordar y que est茅 relacionado con la imagen de la aplicaci贸n.`
6. Obtenga informacion e identifique su Container Registry ejecutando el siguiente comando.
```
ibmcloud cr info
```
7. Cree y etiquete (-t) la imagen de Docker ejecutando el siguiente comando, reemplazando REGISTRY (en este caso es us.icr.io), NAMESPACE (es el espacio creado anteriormente), NOMBRE_IMAGEN y TAG (el nombre y el tag que tengra la imagen de la aplicacion en *IBM Cloud Container Registry*) con los valores apropiados.
```
docker build . -t <REGISTRY>/<NAMESPACE>/<NOMBRE_IMAGEN:TAG>
```
> Nota: `Tenga en cuenta que debe reemplazar: REGISTRY (en este caso es us.icr.io), NAMESPACE (es el espacio creado anteriormente), NOMBRE_IMAGEN y TAG (el nombre y el tag que tengra la imagen de la aplicacion en *IBM Cloud Container Registry*) con los valores apropiados.`
8. Env铆e la imagen de Docker a su Container Registry en IBM Cloud con el siguiente comando:
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

10. Una vez finalizado este proceso ingrese a <a href="https://cloud.ibm.com/"> IBM Cloud</a>. y verifique que en IBM Cloud Container Registry aparezca el espacio de nombres (namespace), el repositorio y la imagen. Tenga en cuenta los nombres que asign贸 en cada paso.

luego de esto ya puede desplegar la imagen dentro de una aplicaci贸n en Code Engine, para esto tenga en cuenta los sigueintes pasos:
1. Dentro del proyecto de Code Engine, seleccione la pesta帽a que dice Aplicaciones y posteriormente de click en el bot贸n ```Crear```.
2. Asigne un nombre para su aplicaci贸n.
3. En la secci贸n ```Elija el c贸digo que desea ejecuta/Choose the code to run``` seleccione la opci贸n ```Imagen de contenedor/Container image```.
4. De click en el bot贸n Configurar imagen y coloque lo siguiente:
   * En servidor de registro coloque el nombre de dominio que indic贸 en la creaci贸n del acceso a registro. En este caso es us.icr.io.
   * En acceso a registro seleccione el nombre del acceso que cre贸 anteriormente.
   * Seleccione el espacio de nombres (namespace) que asign贸 cuando subi贸 la imagen de la aplicaci贸n a IBM Cloud Container Registry.
   * Seleccione el repositorio (nombre de la imagen) que asign贸 en IBM Cloud Container Registry.
   * Seleccione la etiqueta de su imagen.
   * De click en el bot贸n Listo.
5. En el puerto de escucha coloque 8080
6. De click en Crear y espere unos minutos mientras que la imagen se despliega sobre la aplicaci贸n que acaba de crear.

<p align="center">
<img width="800" alt="img8" src=https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/8c1c32e61f42ec2816c2285844465d3e1fdc0719/Imagenes/ci.gif>
</p>


### Opci贸n 3 repositorio privado en github:
Con Code Engine, puede agregar acceso a un repositorio de c贸digo privado y luego hacer referencia a ese repositorio desde su compilaci贸n. Despu茅s de crear el acceso a su repositorio de c贸digo privado, puede extraer c贸digo del repositorio, compilarlo y desplegar una aplicaci贸n o trabajo con IBM Cloud 鈩? Code Engine. Siga los pasos mostrados a continuaci贸n:

#### Crear llave SSH y asociarla al repositorio de github

1.	Cree una ssh key para posteriormente asociara a su repositorio privado, para generarla acceda al *IBM Cloud Shell* y coloque el comando:
 
```
ssh-keygen -t rsa -C "user_id"
```

-	Al ejecutar el comando anterior, en la consola se pide que especifique la ubicaci贸n, en este caso oprima la tecla Enter para que se guarde en la ubicaci贸n sugerida. Posteriormente, cuando se pida la ```Passphrase```oprima la tecla Enter para dejarlo vacio, ya que no se aceptan ssh-keys con passphrase.

-	A continuaci贸n mu茅vase con el comando ```cd .ssh``` a la carpeta donde est谩n los archivos ```id_rsa.pub``` y ```id_rsa```. Estos archivos contienen las claves p煤blicas y privadas respectivamente.  

-	Visualice la clave p煤blica y la clave privada, aseg煤rese de guardarlas, ya que las necesitar谩 pr贸ximamente. Utilice el comando:
```
cat id_rsa.pub
```
Y para visualizar la llave privada:
```
cat id_rsa
```
2.	Asocie la clave ssh creada anteriormente a su repositorio de github, para esto complete lo siguiente:
Estando en su repostiorio de github, ingrese a ```settings``` > ```deploy keys```, de click en ```add deploy keys``` e  ingrese lo siguiente en los campos espec铆ficos:
* ```Title```: Especifique un titulo 煤nico para la llave.
* ```key```: Ingrese la clave p煤blica que guardo previamente.

Por 煤ltimo, de click en ```add key```.

<p align="center">
<img width="800" alt="img8" src=https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/main/Imagenes/github.gif>
</p>

#### Crear el code repository access

Ingrese a su proyecto en code engine y del men煤 de la izquierda, elija la opci贸n ```code repo access``` y de click en ```crear```. De la ventana desplegada complete lo siguiente:
* ```Name```: Especifique un nombre 煤nico para el acceso.
* ```Code repo server```: Ingrese github.com
* ```ssh private key ```: Ingrese la clave privada que guardo previamente.

Por 煤ltimo, de click en ```create```.

<p align="center">
<img width="800" alt="img8" src=https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/main/Imagenes/code.gif>
</p>



#### Compilar imagen de referencia
Luego de crear el acceso al repositorio privado de Git este se utiliza para la creaci贸n de la imagen, para esto tenga en cuenta los siguientes pasos:

1. En la ventana principal o dasboard de *Code Engine* seleccione el proyecto creado anteriormente.
2. Desde la venta del proyecto de click sobre el bot贸n de ```Image builds/Creaci贸n de im谩genes``` una vez aqu铆 de click sobre el bot贸n de ```Crear```.
3. Esto lo llevara a una pesta帽a de configuraci贸n, aqu铆 llene la informaci贸n de la siguiente manera:
   * ```Name```: Ingrese un nombre 煤nico para la construcci贸n de la imagen.
   * ```Code repo URL```: Ingrese la URL SSH de su repositorio privado.
   * ```Code repo access```: Seleccione el acceso de repositorio creado anteriormente.
   * ```Branch name```: Ingrese el nombre la rama del repositorio de origen en la cual se encuentra la infoermacion.
   * De click en ```Next```, esto lo llevara a la ventana de configuraci贸n de estrategia.
   * Deje la informaci贸n predeterminada y de click en ```Next```, esto lo llevara a la ventana de configuraci贸n de salida.
   * ```Registry server```: Seleccione el servidor en el cual esta desplegado su proyecto, en este caso es ```us.icr.io```.
   * ```Registry access```: Seleccione el acceso de registro creado anteriormente.
   * ```Namespace```: Seleccione un namespace existente o ingrese un nombre con el cual se crear谩 uno autom谩ticamente.
   * ```Repository (image name)```: Ingrese el nombre con el cual se creara la imagen.
   * ```Tag```: Ingrese la etiqueta de la versi贸n de la imagen si aplica.
   * de click en ```Done```
 
Una vez completados estos pasos ya tiene creada la imagen de la aplicaci贸n.

<p align="center">
<img width="800" alt="img8" src=https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/main/Imagenes/imagen.gif>
</p>


#### Despliegue de la aplicaci贸n 

Para desplegar la aplicaci贸n tenga en cuenta los siguientes pasos:

1. En la ventana principal o dasboard de *Code Engine* seleccione el proyecto creado anteriormente.
2. Desde la venta del proyecto de click sobre el bot贸n de ```Applications```, luego de esto de click sobre el bot贸n de ```Create```.
3. Esto lo llevara a una pesta帽a de configuraci贸n, aqu铆 llene la informaci贸n de la siguiente manera:
   * ```Name```: Ingrese un nombre 煤nico para su aplicaci贸n.
   * ```Choose the code to run```: Seleccione la opci贸n de ```Container image/Imagen de contenedor```.
   * ```Image reference```: Ingrese la referencia de la imagen creada anteriormente, esta la puede encontrar en la ventana de ```Output```en la pagina de informaci贸n de la imagen.
   * De click en el bot贸n de configure image, esto lo llevara a una pesta帽a de configuraci贸n, ingrese el registry access creado anteriormente y deje la informaci贸n predeterminada y de click en el boton ```Done```.
   * ```Listening port override```: Ingrese el puerto de escucha de su aplicaci贸n.


<p align="center">
<img width="800" alt="img8" src=https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/main/Imagenes/despliegue2.gif>
</p>

### Opci贸n 4 Repositorio privado en azure:
Igualmente *Code Engine* le permite cargar la aplicaci贸n desde un repositorio privado en Azure, los pasos son muy similares al paso anterior, donde se carg贸 un repositorio privado desde github y se muestran a continuaci贸n:

#### Cree una llave SSH y asociela al repositorio de azure :key:

1.	Cree una ssh key para posteriormente asociara a su repositorio privado, para generarla acceda al *IBM Cloud Shell* y coloque el comando:
 
```
ssh-keygen -t rsa -C "user_id"
```

-	Al ejecutar el comando anterior, en la consola se pide que especifique la ubicaci贸n, en este caso oprima la tecla Enter para que se guarde en la ubicaci贸n sugerida. Posteriormente, cuando se pida la ```Passphrase```oprima la tecla Enter para dejarlo vacio, ya que no se aceptan ssh-keys con passphrase.

-	A continuaci贸n mu茅vase con el comando ```cd .ssh``` a la carpeta donde est谩n los archivos ```id_rsa.pub``` y ```id_rsa```. Estos archivos contienen las claves p煤blicas y privadas respectivamente.  

-	Visualice la clave p煤blica y la clave privada, aseg煤rese de guardarlas, ya que las necesitar谩 pr贸ximamente. Utilice el comando:
```
cat id_rsa.pub
```
Y para visualizar la llave privada:
```
cat id_rsa
```
2.	Asocie la clave ssh creada anteriormente a su repositorio en azure, para esto complete lo siguiente:
Estando en su proyecto de azure,  ingrese a ```User settings``` > ```SSH public keys```, de click en ```+ New key``` e  ingrese lo siguiente en los campos espec铆ficos:
* ```Name```: Especifique un nombre 煤nico para la llave.
* ```Public Key Data```: Ingrese la clave p煤blica que guardo previamente.

Por 煤ltimo, de click en ```Add```.

<p align="center">
<img width="800" alt="img8" src=https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/main/Imagenes/sshazure.gif>
</p>

#### Crear el code repository access :door:

Ingrese a su proyecto en code engine y del men煤 de la izquierda, elija la opci贸n ```code repo access``` y de click en ```create```. De la ventana desplegada complete lo siguiente:
* ```Name```: Especifique un nombre 煤nico para el acceso.
* ```Code repo server```: Ingrese ssh.dev.azure.com
* ```ssh private key ```: Ingrese la clave privada que guardo previamente.

Por 煤ltimo, de click en ```create```.

<p align="center">
<img width="800" alt="img8" src=https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/main/Imagenes/codeazure.gif>
</p>

#### Compilar imagen de referencia 鈻讹笍
Luego de crear el acceso al repositorio privado de azure este se utiliza para la creaci贸n de la imagen, para esto tenga en cuenta los siguientes pasos:

Ingrese a su proyecto en code engine y del men煤 de la izquierda, elija la opci贸n ```Image builds``` y de click en ```create```. De la ventana desplegada complete lo siguiente:

   * ```Name```: Ingrese un nombre 煤nico para la construcci贸n de la imagen.
   * ```Code repo URL```: Ingrese la URL SSH de su repositorio privado.
   * ```Code repo access```: Seleccione el acceso de repositorio creado anteriormente.
   * ```Branch name```: Ingrese el nombre la rama del repositorio de origen en la cual se encuentra la aplicaci贸n. (En este caso master)
   
 De click en ```Next```, esto lo llevara a la ventana de configuraci贸n de estrategia, deje la informaci贸n predeterminada y de click en ```Next```, esto lo llevar谩 a la ventana de configuraci贸n de salida, complete lo siguiente:
 
   * ```Registry server```: Seleccione el servidor en el cual esta desplegado su proyecto, en este caso es ```us.icr.io```.
   * ```Registry access```: Seleccione el acceso de registro. Puede dejar por defecto Automatic.
   * ```Namespace```: Seleccione un namespace existente o ingrese un nombre con el cual se crear谩 uno autom谩ticamente.
   * ```Repository (image name)```: Ingrese el nombre con el cual se crear谩 la imagen.
   * ```Tag```: Opcionalmente ingrese la etiqueta de la versi贸n de la imagen.
  
  De click en ```Done```. Una vez el build se encuentre en estado "Ready", de click en "Submit build", en la ventana que se despliega deje todos los datos por defectos y de click nuevamente en "Submit build", finalmente espere a que su estado sea "Succeeded".
 

<p align="center">
<img width="800" alt="img8" src=https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/main/Imagenes/build.gif>
</p>


#### Despliegue de la aplicaci贸n 馃捇

Ingrese a su proyecto en code engine y del men煤 de la izquierda, elija la opci贸n ```Applications``` y de click en ```create```. De la ventana desplegada complete lo siguiente:

   * ```Name```: Ingrese un nombre 煤nico para su aplicaci贸n.
   * ```Choose the code to run```: Seleccione la opci贸n de ```Container image/Imagen de contenedor```.
   * ```Image reference```: Ingrese la referencia de la imagen creada anteriormente, esta la puede encontrar en la ventana de ```Output```en la pagina de informaci贸n de la imagen.
   * De click en el bot贸n de configure image, esto lo llevara a una pesta帽a de configuraci贸n, ingrese el registry access creado anteriormente y deje la informaci贸n predeterminada y de click en el boton ```Done```.
   * ```Listening port override```: Ingrese el puerto de escucha de su aplicaci贸n.

Finalmente de click en "Create", una vez su aplicaci贸n este lista, puede ingresar dando click en "Open application URL". 


<p align="center">
<img width="800" alt="img8" src=https://github.com/emeloibmco/IBM-Cloud-Code-Engine-.Net/blob/main/Imagenes/app.gif>
</p>



# Referencias 馃摉

* [Acerca de IBM Cloud Code Engine](https://cloud.ibm.com/docs/codeengine).

## Autores :black_nib:
Equipo IBM Cloud Tech Sales Colombia.
<br />

     
