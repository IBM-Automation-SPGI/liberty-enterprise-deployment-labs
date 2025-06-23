# Laboratorio 1050: Actualización sin migración con Liberty

![bandera](./images/media/image1.jpeg)

**Última actualización:** marzo de 2023

**Duración:** 45 minutos

¿Necesitas ayuda? Contacta con **Kevin Postreich, Yi Tang.**

## Introducción al laboratorio

El objetivo de este laboratorio es proporcionar orientación práctica y experiencia práctica siguiendo las mejores prácticas para actualizaciones de versión a versión (fixpack a fixpack) de WebSphere Liberty.

Debido al **modelo de características** de Liberty y su **arquitectura de migración cero** , Liberty sigue un modelo de entrega continua de flujo único.

Solo hay una versión de Liberty, por lo que no es necesario actualizarla. Además, cada nueva versión reinicia el período de soporte de 5 años, por lo que no se necesitan extensiones.

Simplemente instale los nuevos fixpacks para obtener las últimas mejoras de rendimiento, funciones y correcciones de errores. A continuación, utilice su proceso de compilación actual para generar un nuevo paquete de Liberty Server que contenga los binarios de Liberty actualizados, su aplicación y configuración existentes, y luego impleméntelo en una ubicación de instalación dual. Esto elimina la mayor dificultad para gestionar la deuda técnica de sus aplicaciones, que consiste en mantenerse al día con las actualizaciones de software.

En el laboratorio, seguirás estas prácticas recomendadas para actualizar la versión de Liberty en el Colectivo.

- Actualización mediante proceso de compilación

- Despliegues Azul/Verde

    - Ubicaciones de instalación dual

    - Inicio de ondulación (detener lo antiguo/iniciar lo nuevo, JVM por JVM)

    - Elimina instancias antiguas cuando te convenga

A continuación se muestra una ilustración del resultado de un proceso de actualización.

**Nota:** Los nombres de servidor, rutas y puertos utilizados en el laboratorio son diferentes a los ilustrados.

![](./images/media/image3.png)

Siguiendo esta metodología, comprenderá cómo puede aplicar sus propios procesos de construcción o automatización para lograr una agilidad y flexibilidad significativas al administrar los colectivos Liberty con procesos automatizados repetibles que reducen significativamente el riesgo para su negocio.

Después de completar el laboratorio, debería poder apreciar lo sencillo que es actualizar Liberty aprovechando su arquitectura de migración cero y las prácticas recomendadas que se describen en el laboratorio.

**Este laboratorio contiene las siguientes actividades prácticas:**

- Cree un nuevo paquete de servidor Liberty que incluya un paquete de correcciones actualizado de Liberty

- Implemente el nuevo paquete Liberty Server en una ubicación de instalación dual.

- Agregar los servidores como nuevos miembros colectivos al Colectivo

- Utilice el Centro de administración de Liberty para **iniciar** los nuevos servidores Liberty.

- Pruebe la aplicación con el paquete de correcciones Liberty actualizado

**El entorno de laboratorio consta de dos máquinas virtuales host:**

- servidor0.gym.lan

- servidor1.gym.lan

Utilizará los mismos scripts de shell de Linux “ **Build and Deploy** ” que utilizó en los laboratorios anteriores para crear un nuevo paquete de servidor Liberty (utilizando Liberty 22.0.0.12) e implementar el paquete de servidor en ambas máquinas virtuales host y unirse al colectivo.

![](./images/media/image4.png)

## **Accediendo al entorno**

Si realiza este laboratorio como parte de un taller impartido por un instructor (virtual o presencial), ya se le ha proporcionado un entorno. El instructor le proporcionará los detalles para acceder al laboratorio.

De lo contrario, deberá reservar un entorno para el laboratorio. Puede obtenerlo aquí. Siga las instrucciones en pantalla para la opción " **Reservar ahora** ".

KLP: ENLACE A DETERMINAR PARA LA RESERVA DE ENV

El entorno de laboratorio contiene dos (2) máquinas virtuales Linux.

![](./images/media/image5.png)

Se configura un servicio publicado para proporcionar acceso a la VM **server0** a través de la interfaz noVNC para el entorno de laboratorio.

1. Acceda al entorno de laboratorio desde su navegador web.

    a. Una vez aprovisionado el entorno, haga clic con el botón derecho en el enlace **"Servicio publicado"** . A continuación, seleccione " **Abrir enlace en una nueva pestaña** " en el menú contextual.

    ![](./images/media/image6.png)

    b. Haga clic en el enlace **"vnc.html"** para abrir el entorno de laboratorio a través de la interfaz **noVNC** .

    ![](./images/media/image7.png)

    c. Haga clic en el botón **Conectar**

    ![](./images/media/image8.png)

    d. Ingrese la contraseña: **passw0rd** . Luego, haga clic en el botón **"Enviar credenciales"** para acceder al entorno de laboratorio.

    **Nota:** Ese es un cero numérico en passw0rd

    ![](./images/media/image9.png)

2. Inicie sesión en la máquina virtual **server0** utilizando las credenciales a continuación:

    - ID de usuario: **techzone**
    - Contraseña: **IBMDem0s!**

## **Consejos para trabajar en el entorno de laboratorio**

1. Puede cambiar el tamaño del área visible utilizando las opciones **de configuración de noVNC** para cambiar el tamaño del escritorio virtual para que se ajuste a su pantalla.

    a. Desde la máquina virtual del entorno, haga clic en el **icono de giro** en el panel de control noNC para abrir el menú.

    ![ajustar a la ventana](./images/media/z-twisty.png)

    b. Para aumentar el área visible, haga clic en `Settings > Scaling Mode` y configure el valor en `Remote Resizing`

    ![ajustar a la ventana](./images/media/z-remote-resize.png)

2. Puede copiar/pegar texto de la guía de laboratorio en el entorno de laboratorio utilizando el portapapeles en el visor noVNC.

    a. Copie el texto de la guía de laboratorio que desea pegar en el entorno de laboratorio.

    b. Haga clic en el icono **del Portapapeles** y **pegue** el texto en el portapapeles de noVNC.

    ![ajustar a la ventana](./images/media/paste.png)

    c. Pegue el texto en la máquina virtual, como en una ventana de terminal, una ventana del navegador, etc.

    d. Haga clic en el icono **del portapapeles** nuevamente para cerrarlo.

    > **NOTA:** A veces, pegar en una ventana de Terminal en la VM no funciona de manera consistente.

    > En este caso, puedes intentarlo nuevamente, o abrir otra ventana de terminal e intentarlo nuevamente, o pegar el texto en un **editor de texto** en la máquina virtual y luego pegarlo en la ventana de terminal en la máquina virtual.

3. Como alternativa a la opción "Copiar y pegar" de noVNC, puede considerar abrir la guía de laboratorio en un navegador web dentro de la máquina virtual. Con este método, puede copiar y pegar fácilmente texto de la guía de laboratorio sin tener que usar el portapapeles de noVNC.

<br>

## **Parte 1: Clonar el repositorio de GitHub para este taller**

Este laboratorio requiere artefactos almacenados en un repositorio de GitHub. Ejecute el siguiente comando para clonar el repositorio en la máquina virtual local utilizada para el laboratorio.

1. Clone el repositorio de GitHub que contiene los artefactos de laboratorio necesarios para el laboratorio si aún no lo ha hecho en un laboratorio anterior de esta serie.

    a. Abra una nueva ventana de terminal en la máquina virtual “ **server0.gym.lan** ”.

    ![](./images/media/image13.png)

    b. Clonar el repositorio de GitHub necesario para el laboratorio.

    ```
    git clone https://github.com/IBMTechSales/liberty_admin_pot.git
    ```

    c. Navegue al directorio lab-scripts en el repositorio clonado.

    ```
    cd ~/liberty_admin_pot/lab-scripts
    ```

    d. Agregue los permisos de "ejecución" a los directorios de scripts de laboratorio y a los scripts de shell.

    ```
    chmod -R 755 ./
    ```

## **Parte 2: Asegurarse de que el Colectivo Libertad esté desplegado**

El enrutamiento dinámico de Liberty requiere un Colectivo Liberty y que los servidores de aplicaciones que alojan las aplicaciones utilizadas en los laboratorios sean miembros del Colectivo Liberty. El módulo de aprendizaje para crear el Colectivo Liberty es "Lab_1020".

En esta sección, se asegurará de que haya un colectivo administrativo de Liberty disponible y de que los servidores de aplicaciones estén implementados en el colectivo.

<table>
<tbody>
<tr class="odd">
<td><img src="./images/media/image2.png" style="width:2.170833in;height:0.73125in" alt="señal de precaución"></td>
<td>
<p><strong>IMPORTANTE: ¡Por favor leer!</strong></p>
<p>Si has completado el <strong>Laboratorio 1020</strong> o <strong>el Laboratorio 1030</strong> de esta serie, ya has creado el controlador colectivo Liberty.</p>
<p>En otras palabras, puede omitir <strong>la Parte 2</strong> del laboratorio y continuar con <strong>la Parte 3</strong> si ya ha completado el Laboratorio 1020 o el Laboratorio 1030 de esta serie.</p>
<p>El ULR del Centro de Administración es: <a href="https://server0.gym.lan:9491/adminCenter">https://server0.gym.lan:9491/adminCenter</a></p>
<p>Las credenciales del Centro de administración son: <strong>admin</strong> / <strong>admin</strong></p>
</td>
</tr>
</tbody>
</table>

### 2.1: Si no ha completado el laboratorio 1020 o el laboratorio 1030, los siguientes pasos proporcionan una “ruta rápida” para crear el colectivo Liberty requerido para este laboratorio, “Laboratorio 1050”.

1. Ejecute el siguiente comando para garantizar que se cree un colectivo Liberty:

    ```
    /home/techzone/liberty_admin_pot/lab-scripts/deployCollective.sh
    ```

    El script deployCollectve.sh hará lo siguiente:

    - Si el controlador ya existe, el script se ABORTARÁ, ya que ya existe un colectivo
    - Si el Controlador NO existe, se creará
    - Construir y producir un paquete de Liberty Server para implementarlo en servidores de aplicaciones Liberty
    - Cree dos servidores Liberty, “appServer1” y “appServer2”, implemente el paquete del servidor y una los servidores al Liberty Collective
    - Configurar el enrutamiento dinámico en el controlador colectivo

2. Una vez completado el script, acceda al **Centro de administración** . Ingrese las credenciales de inicio de sesión: **admin** / **admin**

    ```
    https://server0.gym.lan:9491/adminCenter
    ```

    **Nota:** Si ve la “Advertencia: Posible riesgo de seguridad más adelante”, haga clic en **Avanzado...-&gt;Aceptar riesgo y continuar** para continuar.

    Se muestra la página del Centro de administración del colectivo Liberty.

    ![Interfaz gráfica de usuario, descripción de la aplicación generada automáticamente](./images/media/image18.png)

3. Haga clic en el icono **Explorar**

    ![Descripción del icono generada automáticamente](./images/media/image19.png)

    Se muestra la lista de recursos colectivos y puedes ver que tienes tres servidores.

    ![](./images/media/new-image001.png)

4. Haga clic en la lista de Servidores para ver los tres servidores, appServer1, appServer2 y CollectiveController

    ![](./images/media/new-image002.png)

    **¡EVITAR PROBLEMAS!**

    Si ejecutó el comando “ **deployCollectve.sh** ” y terminó con la siguiente información, como se ilustra en la captura de pantalla a continuación, significa que el script detectó que el controlador colectivo ya se ha creado.

    Por lo tanto, los scripts existían y mostraban la URL del Centro de administración.

    Vaya a la URL del Centro de administración y verifique que el colectivo contenga los dos miembros del colectivo, “appServer1” y “appServer2”, como se ilustra arriba.

    **¡Solución de problemas!**

    Si la aplicación del Centro de administración no puede ejecutarse o los dos servidores de aplicaciones no son parte del colectivo, se requiere una limpieza manual del colectivo y debe comunicarse con el instructor del laboratorio.

    ![](./images/media/new-image003.png)

## **Parte 3: Asegúrese de que los servidores miembros del colectivo Liberty estén implementados**

En este punto, deberías tener dos miembros del colectivo Liberty que ahora deben crearse en el colectivo y nombrarse de la siguiente manera:

- servidor de aplicaciones1
- Servidor de aplicaciones2

1. Acceda al Centro de administración de Liberty desde el navegador

    a. Ingrese las credenciales de inicio de sesión como: admin / admin

    ```
    https://server0.gym.lan:9491/adminCenter
    ```

2. Vaya a la vista Servidores en el Centro de administración

    a. Desde el Centro de administración, vaya a la página **Explorador** y haga clic en el ícono **SERVIDORES** para acceder a su página de detalles.

    ![](./images/media/new-image005.png)

    ![](./images/media/new-image006.png)

En este punto, **appServer1** y **appServer2** deberían mostrarse en el colectivo.

No hay problema si no están en estado "En ejecución". Los iniciarás más adelante en los laboratorios.

## **Parte 4: Producir un nuevo «paquete de servidor» utilizando Liberty 22.0.0.12**

En esta sección del laboratorio, utilizará el mismo script automatizado “ **mavenBuild** ” que se usó en laboratorios anteriores para producir un NUEVO paquete de servidor Liberty, con solo una diferencia notable: el paquete del servidor incluirá Liberty 22.0.0.12 en lugar de 22.0.0.8.

Producir la salida de la compilación en forma de un archivo zip del paquete del servidor Liberty proporciona la flexibilidad de implementar y actualizar su versión de Liberty y sus aplicaciones como un paquete inmutable, de manera similar a como se implementan las imágenes de contenedores en las plataformas de contenedores de Kubernetes.

En esta sección del laboratorio, utilizará el script de shell proporcionado que automatiza las tareas para producir un paquete de servidor para su implementación en el colectivo.

### **Utilice el script Maven Build para producir un paquete de servidor con Liberty 22.0.0.12**

1. Ejecute el siguiente comando para compilar las aplicaciones y producir un paquete de servidor, que utilizará el kernel WebSphere Liberty, versión 22.0.0.12

    ```
    ~/liberty_admin_pot/lab-scripts/mavenBuild.sh -v 22.0.0.12
    ```

    Tenga en cuenta que se creó un nuevo paquete de servidor Liberty con el nombre: “ **22.0.0.12-pbwServerX.zip”**

    ![](./images/media/image14.png)

    La salida del script “ **mavenBuild** ” es un paquete de Liberty Server.

    El paquete del servidor está en el siguiente directorio de trabajo.

    > **/inicio/techzone/laboratorio/servidoresempaquetados**

2. Usando el visor de archivos en el escritorio de la máquina virtual, verifique que se haya producido el paquete del servidor.

    a. Haga doble clic con el ratón en la carpeta " **Inicio"** de la máquina virtual de escritorio.

    ![](./images/media/image15.png)

    b. Desde el explorador de archivos, navegue al directorio **techzone/lab-work/packagedServers** .

    **SUGERENCIA:** el paquete del servidor se nombra según la versión de Liberty en el paquete y el nombre del servidor de marcador de posición; “ **22.0.0.12-pbwServerX.zip** .

    ![](./images/media/image16.png)

**¡Felicitaciones!** Ha utilizado Maven y ha generado con éxito un nuevo paquete de servidor Liberty que contiene sus aplicaciones y WebSphere Liberty 22.0.0.12.

En las siguientes secciones del laboratorio, continuará con la mejor práctica de usar la automatización para implementar el paquete del servidor en dos hosts (VM) y unir los servidores implementados a Liberty Collective.

## **Parte 5: Implementar el nuevo paquete de servidor en el Colectivo**

En esta sección del laboratorio, implementará nuevos servidores Liberty como miembros colectivos del colectivo, utilizando el paquete de servidor que produjo en la sección anterior del laboratorio.

Ese paquete de servidor que usted creó incluye los binarios de Liberty para 22.0.0.12 y las MISMAS aplicaciones de muestra que utilizan la MISMA configuración de servidor predeterminada que la implementación anterior en Liberty 22.0.0.8.

En este laboratorio, utilizará el mismo script “ **addMember.sh** ” utilizado en laboratorios anteriores para implementar los paquetes del servidor en los nodos, implementar el paquete del servidor, crear los miembros del colectivo y unir los miembros al colectivo.

### **Inicie el Centro de administración de Liberty en el navegador web**

1. Si el Centro de Administración Liberty aún no está abierto en el navegador web, ábralo ahora utilizando la siguiente URL:

    ```
    https://server0.gym.lan:9491/adminCenter/
    ```

2. Inicie sesión en el **Centro de administración** con las credenciales: **admin** / **admin** .

    ![](./images/media/image17.png)

    Se muestra la interfaz de usuario del “ **Centro de administración”** de Liberty Collective.

    ![](./images/media/image18.png)

3. Haga clic en el ícono **Explorar** para mostrar los servidores, las aplicaciones y el Colectivo.

    ![Descripción del icono generada automáticamente](./images/media/image19.png)

4. Haga clic en la vista **Servidores** para mostrar los servidores en el Colectivo.

    > **Nota:** Ya deberías ver dos servidores implementados en el colectivo.

    > Los servidores ejecutan **Liberty 22.0.0.8** . El servidor debería estar en estado " **En ejecución** ".

5. **Si los servidores appServer1 o appServer2 NO están en ejecución, continúe e **inícielos** ahora.**

    Estos servidores se implementaron en los laboratorios anteriores.

    ![](./images/media/image20.png)

### **5.1 Implementar el miembro colectivo en la máquina virtual del host local, server0.gym.lan**

Utilice el script de automatización para implementar el servidor Liberty desde el paquete de servidor que creó anteriormente y unir al miembro al colectivo.

**El siguiente script realiza lo siguiente:**

> - Implementar el paquete del servidor para Liberty versión 22.0.0.12
> - Aplicar anulaciones para los puertos HTTP y HTTPS con “9081 / 9441”
> - Cambie el nombre del servidor predeterminado a “appServer1”
> - Unir el servidor al colectivo

1. Desde una ventana de terminal en la VM, ejecute el siguiente comando para crear un miembro colectivo Liberty local en la VM **server0.gym.lan** usando el paquete de servidor 22.0.0.12.

    ```
    ~/liberty_admin_pot/lab-scripts/addMember.sh -n appServer1 -v 22.0.0.12 -p 9081:9441 -h server0.gym.lan
    ```

    ![](./images/media/image22.png)

    Cuando se completa el script, se crea la aplicación del servidor **appServer1** con Liberty 22.0.0.12 y se agrega al colectivo.

    El script **addMember.sh** creó un servidor Liberty local llamado **appServer1** en el siguiente directorio en la VM **server0.gym.lan** :

    > **/inicio/techzone/laboratorio/liberty-staging/22.0.0.12-appServer1/wlp/usr/servers**

2. Regrese a la página del Servidor del Centro de administración del colectivo Liberty, podrá ver que el servidor **appServer1** en el directorio “ **22.0.0.12-appServer1** ” se agregó al colectivo como un nuevo servidor.

    El servidor está en estado “ **Detenido** ”.

    > ![](./images/media/image23.png)

### **5.2 Implementar un miembro colectivo en la máquina virtual del host remoto, server1.gym.lan**

Ahora, ejecute el script de nuevo, con parámetros ligeramente diferentes, para implementar Liberty en la máquina virtual remota, **server1.gym.lan** . A continuación, una el miembro remoto al colectivo.

Unir miembros remotos a un colectivo requiere un par de pasos adicionales que el script realiza por usted en el entorno de laboratorio y que se identifican a continuación.

1. Ejecute el siguiente comando para crear un miembro colectivo Liberty remoto en la máquina virtual **server1.gym.lan** , especificando un nombre de servidor y puertos diferentes.

    ```
    ~/liberty_admin_pot/lab-scripts/addMember.sh -n appServer2 -v 22.0.0.12 -p 9082:9442 -h server1.gym.lan
    ```

    ![](./images/media/image24.png)

    - Cuando se completa el script, se crea la **aplicación** del servidor Server2 con Liberty 22.0.0.12 y se agrega al colectivo.

    - El script **addMember.sh** creó un servidor Liberty local llamado **appServer2** en el siguiente directorio en la VM **server1.gym.lan** :

        > **/opt/IBM/liberty-staging/22.0.0.12-appServer2/wlp/usr/servers**

    - El servidor utiliza **9082** y **9442** como sus puertos HTTP/HTTPS, tal como se define en los parámetros de entrada del script.

2. Regrese a la vista “ **servidores** ” del Centro de administración colectivo Liberty y podrá ver que el nuevo miembro, **appServer2** , se ha agregado a la lista de servidores y se encuentra en estado **Detenido** .

    ![](./images/media/image25.png)

### **5.3 Ripple Inicia los nuevos servidores 22.0.0.12**

Ahora que los nuevos servidores Liberty con la versión 22.0.0.12 de Liberty se han implementado en el colectivo, todo lo que necesita hacer es iniciar los servidores.

Al iniciar los servidores Ripple se permitirá que los nuevos servidores de aplicaciones acepten solicitudes entrantes sin provocar una interrupción de la aplicación.

El inicio de la ondulación se realizará manualmente en el Centro de Administración en este laboratorio. Sin embargo, al igual que con otros scripts automatizados que ha utilizado, este proceso también se puede automatizar fácilmente.

**Pasos para iniciar la propagación del nuevo servidor:**

- Detener 22.0.0.8-appServer1
- Iniciar 22.0.0.12-appServer1
- Detener 22.0.0.8-appServer2
- Iniciar 22.0.0.12-appServer2

  <br>


1. Antes de iniciar los servidores Liberty, debe asegurarse de que la base de datos db2 utilizada por la aplicación **PlantsByWebSphere** esté en ejecución.

    ```
    docker start db2_demo_data
    ```

2. **Detenga** el miembro colectivo “ **22.0.0.8-appServer1”** desde el Centro de administración de Liberty.

    a. En la página de detalles **del servidor** , haga clic en el icono del menú desplegable " **22.0.0.8-appServer1"** y seleccione **"Detener"** para detener el servidor.

    ![](./images/media/image26.png)

    **Nota:** Si se le solicitan credenciales, ingrese el nombre de usuario y la contraseña del Centro de administración como: **admin / admin** .

    b. Haga clic **en Detener** para confirmar la detención del servidor **22.0.0.8-appServer1.**

    ![](./images/media/image27.png)

    **La aplicación** del servidor Server1 se detendrá y podrás ver que ahora está en estado **Detenido** .

    ![](./images/media/image28.png).

3. **Inicie** el miembro colectivo “ **22.0.0.12-appServer1** ” desde el Centro de administración de Liberty.

    a. En la página de detalles **del servidor** , haga clic en el ícono del menú desplegable " **22.0.0.12-appServer1"** y seleccione **"Iniciar"** para iniciar el servidor.

    ![](./images/media/image29.png)

    b. Haga clic **en Iniciar** para confirmar la detención del servidor **22.0.0.12-appServer1** .

    ![](./images/media/image30.png)

    Se iniciará el servidor **22.0.0.12-appServer1** y podrás ver que ahora está en estado **de ejecución** .

    ![](./images/media/image31.png).

### **Punto de control del estado actual**

En este punto, debería ver los siguientes estados del servidor:

**En el servidor VM server0.gym.lan:**

- 22.0.0.8-appServer1 - **Detenido**

- 22.0.0.12-appServer1 - **En ejecución**

![](./images/media/image32.png)

**En el servidor VM server1.gym.lan:**

- 22.0.0.8-appServer2 - **En ejecución**

- 22.0.0.12-appServer2 - **Detenido**

![](./images/media/image33.png)

A continuación, inicie el servidor **22.00.12-appServer2** en la VM **server1.gym.lan** siguiendo los mismos pasos indicados anteriormente.

1. **Detenga** el miembro colectivo **22.0.0.8-appServer02** desde el Centro de administración de Liberty.

2. **Inicie** el miembro colectivo **22.0.0.12-appServer02** desde el Centro de administración de Liberty.

El estado final debe reflejar que los servidores 22.0.0.12 están EN EJECUCIÓN y los servidores 22.0.0.8 están DETENIDOS.

![](./images/media/image34.png)

**¡Felicitaciones!** Acaba de completar la actualización de Liberty 22.0.0.8 a 22.0.0.12 utilizando la arquitectura de migración cero de WebSphere Liberty y las prácticas comunes para implementaciones que utilizan paquetes de servidor inmutables para implementaciones flexibles.

La actividad final de este laboratorio es demostrar que las aplicaciones continúan ejecutándose como están después de la actualización.

## **Parte 6: Probar las aplicaciones después de la actualización de Liberty**

Has iniciado con éxito los nuevos servidores 22.0.0.12 en el colectivo.

En esta sección, probará las aplicaciones PlantsByWebSphere y WhereAmI y se asegurará de que se ejecuten correctamente después de la actualización a Liberty 22.0.0.12.

### **Parte 6: Pruebe las aplicaciones utilizadas en los laboratorios de esta serie de talleres**

En esta sección, probará las dos aplicaciones que están implementadas en el colectivo.

### **6.1 Pruebe la aplicación PlantsByWebSphere:**

1. Para acceder a la aplicación **PlantsByWebSphere** en appServer1

    a. Abra una nueva pestaña en el navegador Firefox y pruebe PlantsByWebSphere en **appServer1** , que está en **server0.gym.lan**

    ```
    https://server0.gym.lan:9441/PlantsByWebSphere
    ```

    **Nota:** Si ve la “Advertencia: Posible riesgo de seguridad más adelante”, haga clic en Avanzado...-&gt;Aceptar riesgo y continuar para continuar.

    ![](./images/media/image35.png)

    b. En la aplicación, haga clic en el enlace “ **Ayuda** ”, ubicado en la esquina superior derecha de la página.

    ![](./images/media/image36.png)

    c. En la página " **Ayuda** ", verá el nombre del servidor y la versión de Liberty que se ejecuta en el servidor que gestionó esta solicitud específica.

    ![](./images/media/image37.png)

    d. En la aplicación, haga clic en el enlace “ **Inicio** ” para regresar a la página de inicio de PlantsByWebSphere.

    ![](./images/media/image38.png)

2. OPCIONAL: Repita los pasos para acceder a la aplicación **PlantsByWebSphere** en appServer2 en el host server1.gym.lan

    ```
    https://server1.gym.lan:9442/PlantsByWebSphere
    ```

    **Nota:** Si ve la “Advertencia: Posible riesgo de seguridad más adelante”, haga clic en Avanzado...-&gt;Aceptar riesgo y continuar para continuar.

    ![](./images/media/image39.png)

### **6.2. Pruebe la aplicación WhereAmI:**

1. Para acceder a la aplicación **WhereAmI** en appServer1

    a. Abra una nueva pestaña en el navegador Firefox y pruebe WhereAmI en **appServer1** , que está en **server0.gym.lan**

    ```
    https://server0.gym.lan:9441/WhereAmI
    ```

    b. Tenga en cuenta que la aplicación se ejecuta en la versión 22.0.0.12 de Liberty.

    ![](./images/media/image40.png)

2. OPCIONAL: Repita los pasos para acceder a la aplicación **WhereAmI** en appServer2 en el host server1.gym.lan

    ```
    https://server1.gym.lan:9442/WhereAmI
    ```

    ![](./images/media/image41.png)

### **6.3 Probar el enrutamiento dinámico después de la actualización de Liberty**

<table>
<tbody>
<tr class="odd">
<td><img src="./images/media/image21.png" style="width:1.9875in;height:0.6875in" alt="información de inicio de sesión"></td>
<td>
<p><strong>¡IMPORTANTE!</strong></p>
<p>Esta sección es opcional y solo se puede realizar si ha completado el Laboratorio “ <strong>1030 - Enrutamiento dinámico</strong> ” en esta serie PoT, o si ejecutó el script <strong>deployCollective.sh</strong> en la “Parte 2” de este laboratorio, Laboratorio 1050.</p>
</td>
</tr>
</tbody>
</table>

En esta parte del laboratorio, demostrará que las capacidades de enrutamiento dinámico de Liberty ahora dirigen automáticamente las solicitudes entrantes del servidor HTTP a los nuevos servidores Liberty 22.0.0.12, después de la actualización.

### **Pruebe el enrutamiento dinámico después de la actualización de Liberty**

1. Para acceder a la aplicación **WhereAmI** a través del servidor HTTP de IBM y el complemento, abra una nueva ventana del navegador e ingrese la URL de la aplicación como:

    ```
    https://server0.gym.lan:8443/WhereAmI
    ```

    La salida muestra la aplicación ejecutándose en **appServer1** en la solicitud inicial.

    > **Nota:** es posible que la solicitud se dirija a **appServer2** en lugar de a appServer1.

    Es importante tener en cuenta que la versión de Liberty es **22.0.0.12** , lo que valida que la capacidad de enrutamiento dinámico ha detectado automáticamente los nuevos servidores Liberty y está dirigiendo las solicitudes entrantes a los nuevos servidores Liberty 22.0.0.12 después de la actualización.

    ![](./images/media/image45.png)

2. Actualice la página de la aplicación haciendo clic en el ícono **de actualización** del navegador en la página.

    Puede ver la salida que muestra que la función de enrutamiento dinámico de Liberty dirige el tráfico de solicitud al servidor **appServer2** .

    ![](./images/media/image46.png)

3. Actualice el navegador nuevamente unas cuantas veces más y vea que las solicitudes se enrutan a **appServer1** y **appServer2** según corresponda.

    Se ha completado la prueba de equilibrio de carga round robin.

## **Resumen**

**¡Felicidades!**

**Has completado con éxito el laboratorio “Actualizaciones de migración cero con Liberty”**

En el laboratorio, siguió estas prácticas recomendadas para actualizar la versión de Liberty en el Colectivo.

- Actualización mediante proceso de compilación
- Despliegues Azul/Verde
    - Ubicaciones de instalación dual
    - Inicio de ondulación (detener lo antiguo/iniciar lo nuevo, JVM por JVM)
    - Elimina instancias antiguas cuando te convenga

![](./images/media/image47.png)

Siguiendo esta metodología, usted adquirió una comprensión de cómo podría aplicar sus propios procesos de construcción o automatización para lograr una agilidad y flexibilidad significativas al administrar los colectivos Liberty con procesos automatizados repetibles que reducen significativamente el riesgo para su negocio.

Ha adquirido una idea de la arquitectura de “ **no migración** ” de Liberty y de lo sencillo que es actualizar Liberty siguiendo las prácticas comunes descritas en el laboratorio.

**En este laboratorio, adquirió experiencia práctica con las siguientes actividades administrativas para actualizar Liberty:**

- Cree un nuevo paquete de servidor Liberty que incluya un paquete de correcciones actualizado de Liberty
- Implemente el nuevo paquete Liberty Server en una ubicación de instalación dual.
- Despliegue Azul/Verde
- Agregar los servidores como nuevos miembros colectivos al Colectivo
- Utilice el Centro de administración de Liberty para **iniciar** los nuevos servidores Liberty.
- Pruebe la aplicación después de una actualización de migración cero de Liberty
