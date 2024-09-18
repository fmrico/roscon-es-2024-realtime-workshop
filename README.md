Programación en tiempo real con ROS 2
=====================================

Material para el workshop de Tiempo Real para la ROSCon España 2024.

Este repositorio está basado en el [repositorio](https://github.com/ros-realtime/roscon-2023-realtime-workshop) del workshop `Real-time programming with ROS 2` del ROSCon 2023 de Nueva Orleans.

**Gracias a [Shuhao Wu](https://github.com/shuhaowu), [Jan Staschlat](https://github.com/janstaschulat), [Stephanie Eng](https://github.com/stephanie-eng) and [Oren Bell](https://github.com/nightduck) for putting together the excellent workshop. [Traspas originales disponibles aquí](https://docs.google.com/presentation/d/1yHaHiukJe-87RhiN8WIkncY23HxFkJynCQ8j3dIFx_w/edit#slide=id.p) ([enlace de backup](https://github.com/ros-realtime/roscon-2023-realtime-workshop/blob/main/Realtime%20workshop%20ROScon%202023.pdf)).**

Configuración de taller para portátiles
---------------------------------------

Para aprovechar al máximo el taller, completa las instrucciones de configuración para tu portátil con antelación. Esto tomará alrededor de 10-20 minutos de tu tiempo y, con suerte, hará que el taller se desarrolle mucho más suavemente. Si encuentras algún problema, por favor [informa de un problema en este repositorio](https://github.com/ros-realtime/roscon-2023-realtime-workshop/issues).

### Requisitos del sistema

El código del taller **solo es compatible con Linux**. Oficialmente, solo probamos **Ubuntu 22.04 o Ubuntu 24.04** como sistema operativo de la portátil/desarrollo. Sin embargo, con la configuración de Docker descrita a continuación, podría ser posible ejecutarlo en otras variantes de Linux, aunque esto no ha sido probado.

**No es necesario un kernel en tiempo real**, aunque es posible que veas cifras de latencia máxima más bajas si tienes un kernel en tiempo real. Como referencia, el código del taller se desarrolla en kernels regulares de Ubuntu.

**Docker para Mac y Docker para Windows no son compatibles. Por favor, intenta traer una portátil con Linux al taller para obtener los mejores resultados.** Es posible que una máquina virtual que se ejecute en Linux funcione para ti, aunque esto tampoco es compatible.

**Podman no es compatible**. Podría funcionar, pero no hemos probado la configuración de Docker a continuación con Podman.

El código se ha probado en `amd64` y `arm64` (a través del Raspberry Pi 4). No se han realizado pruebas en Macbooks M1/M2.

### Uso de Docker

La forma más sencilla de ejecutar el código del taller es utilizando la imagen de Docker específica para el taller. Para obtener la imagen de Docker, hay dos opciones: (1) obtener la imagen antes del taller **(altamente recomendado)**, o (2) obtener la imagen durante el taller (no podemos garantizar que esta experiencia sea buena debido a las limitaciones de la red durante una conferencia).

Antes de obtener la imagen, debes instalar Docker Engine en tu máquina Linux. Por favor, sigue las [instrucciones de instalación de Docker Engine][docker-install]. **Asegúrate de no usar Docker Desktop, ya que no es compatible debido a su uso de una máquina virtual**. Al final, debes confirmar que ves la siguiente salida al ejecutar los siguientes comandos:

```
console
$ docker version | grep -A2 Client
Client: Docker Engine - Community
 Version:           24.0.6
 API version:       1.43

$ docker version | grep -A2 Server
Server: Docker Engine - Community
 Engine:
  Version:          24.0.6
```

La versión exacta de Docker puede ser diferente de la salida anterior.

También debes instalar Docker de manera que puedas gestionar Docker sin necesidad de usar root. [Consulta las instrucciones aquí para obtener detalles](https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user).

[docker-install]: https://docs.docker.com/engine/install/ubuntu/

### Obtener la imagen ANTES del taller

1. Descarga el archivo de imagen desde [la última versión](https://github.com/ros-realtime/roscon-2023-realtime-workshop/releases/latest). El archivo se llama `docker-image.tar.gz`.
2. Clona el repositorio [roscon-2024-realtime-workshop](https://github.com/fmrico/roscon-2024-realtime-workshop) con la opción `--recursive`: `git clone --recursive https://github.com/fmrico/roscon-2024-realtime-workshop.git`.
3. Accede al repositorio `roscon-es-2024-realtime-workshop`.
4. Ejecuta `docker/fetch ~/Downloads/docker-image.tar.gz`.

Esto debería importar la imagen de Docker con el nombre `roscon-2023-realtime-workshop`.

Nota: **NO** utilices `docker import` o `docker load`. Por favor, usa el script `docker/fetch` mencionado anteriormente.

### Iniciar el contenedor Docker

**Si te gustaría usar VS Code y su sistema de contenedores de desarrollo, puedes saltarte esta sección e ir directamente a [Usar Visual Studio Code](#usar-visual-studio-code).**

Después de importar la imagen de Docker, puedes iniciar el contenedor Docker a través del archivo shell especial [`docker/start`](docker/start):

1. Accede a este repositorio.
2. Ejecuta `docker/start`.

Esto debería iniciar el contenedor Docker y montará este repositorio en el directorio `/code` dentro del contenedor. Como resultado, los cambios en el repositorio en el host se reflejarán en el contenedor.

Esto debería iniciar el contenedor Docker con todos los privilegios adecuados para ejecutar aplicaciones programadas en tiempo real. También configura el contenedor con todo lo necesario para ejecutar programas GUI.

### Iniciar sesión en el contenedor Docker

Después de iniciar el contenedor Docker, puedes iniciar sesión en el contenedor Docker utilizando el script especial [`docker/shell`](docker/shell):

1. Accede a este repositorio.
2. Ejecuta `docker/shell`.

Deberías ser recibido con algo como lo siguiente:

```
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

user@6896a5d8dd84:/code$
```

Este script te inicia sesión como un usuario llamado `user` que tiene el mismo UID y GID que el usuario de tu host. Esto elimina algunos de los errores de permisos que podrías encontrar al usar Docker. También te permite ejecutar programas GUI.

### Comprobar que todo funciona

Después de iniciar sesión en el contenedor Docker, deberías comprobar que todo funciona:

**Verificar que las herramientas de visualización de trazas están disponibles**

1. Después de iniciar el contenedor Docker, abre un navegador.
2. Navega a http://localhost:3100.
3. Asegúrate de que se carga una página web como la siguiente captura de pantalla.

![](imgs/perfetto.png)

**Asegúrate de que todos los ejercicios se compilan y que el ejercicio 1 se ejecuta**

Después de iniciar sesión en el contenedor Docker:

```console
$ cd /code/exercise1 && colcon build
$ cd /code/exercise2-1 && colcon build
$ cd /code/exercise2-2 && colcon build
$ cd /code/exercise3-1 && colcon build
$ cd /code/exercise3-2 && ./build.sh
$ cd /code/exercise4-1 && colcon build
$ cd /code/exercise4-2 && colcon build
```

Asegúrate de que todo esto se compila. Luego, asegúrate de que al menos el ejercicio 1 se ejecuta (lo que toma 10 segundos):


```console
$ cd /code/exercise1
$ install/latency_tester/bin/latency_tester
Testing latency for 10 seconds with 2 threads...
Latency testing complete. Trace file available at: exercise1.perfetto
```

Puedes cargar el archivo `exercise1.perfetto` en el visor de trazas que verificaste anteriormente y ver algunos datos siendo graficados.

**Verifica que rviz2 está funcionando**

Después de iniciar sesión en el contenedor Docker, ejecuta:

```console
$ rviz2
```

La ventana GUI habitual de rviz2 debería aparecer si todo está bien.

### Usar Visual Studio Code

Si te gusta usar VS Code para el desarrollo, debes instalar el [Remote Development Extension Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack), que habilita el sistema de contenedores de desarrollo.

Este repositorio contiene una configuración [`.devcontainer`](.devcontainer). Esta configuración depende de la imagen importada anteriormente utilizando el script `docker/fetch`. Por lo tanto, si no has realizado el [paso de importación de la imagen](#getting-the-image-before-the-workshop), iniciar VS Code con contenedores de desarrollo no funcionará, ya que fallará al intentar encontrar la imagen.

Una vez que [hayas importado la imagen](#getting-the-image-before-the-workshop), simplemente puedes abrir VS Code con contenedores de desarrollo en este repositorio. Puedes usar la terminal de VS Code o usar el script `.devcontainer/shell` para iniciar sesión en el contenedor. Todas las funciones mencionadas anteriormente deberían funcionar y deberías verificarlas.

**Hacer que IntelliSense funcione**

Para hacer que IntelliSense funcione con la extensión de C++, debes seleccionar la configuración de compilación adecuada para cada ejercicio con el botón en la parte inferior derecha de tu barra de estado, como se indica en la siguiente captura de pantalla:

![](imgs/vscode.png)

También debes compilar el código para ese ejercicio al menos una vez usando `colcon build`. A veces, es necesario un comando para recargar la ventana (CTRL+P -> `Developer: Reload Window`) para que IntelliSense funcione completamente.

Configuración del taller para el Raspberry Pi 4
-----------------------------------------------

**(Nota: En la ROSCon España 2024 de Sevilla el soporte que ofrecemos en esta parte está muy limitado)**

Tendremos un número limitado de Raspberry Pi 4 para prestar a los asistentes. Esperamos que la gente forme grupos de 3-5 personas para trabajar juntos en los problemas. Dicho esto, también te invitamos a traer tus propios Raspberry Pi 4. Recomendamos el siguiente hardware:

- Un Raspberry Pi 4 (se recomienda 4GB o más, 2GB puede ser suficiente)
- Un cable Ethernet y cualquier adaptador USB Ethernet necesario para conectar el Raspberry Pi directamente a tu portátil
- Una tarjeta microSD con más de 8GB
- La fuente de alimentación del Raspberry Pi 4 con enchufes de alimentación de EE.UU.

### Antes del taller: flasheo de la imagen

Se debe flashear una imagen especialmente diseñada para el taller en la tarjeta SD. Esta imagen contiene una instalación de Ubuntu 22.04 con ROS 2 humble y un kernel en tiempo real que se basa en [la imagen de Raspberry Pi 4 mantenida por el grupo de trabajo en tiempo real de ROS 2](https://github.com/ros-realtime/ros-realtime-rpi4-image). Tiene el código de los ejercicios incorporado y todas las dependencias instaladas, para asegurar que la experiencia pueda realizarse completamente offline. También contiene una serie de utilidades que facilitan la participación de los asistentes en el taller.

Para descargar la imagen, ve a la [última versión](https://github.com/ros-realtime/roscon-2023-realtime-workshop/releases/latest). Descarga el archivo `roscon2023-rt-workshop-rpi4-ubuntu-22.04.1-ros2-humble.img.zst`. Este es un archivo [comprimido con zstd](https://en.wikipedia.org/wiki/Zstd) para ahorrar espacio. Deberías poder hacer doble clic en él y extraer el archivo de imagen. Alternativamente, puedes usar el comando:

```
$ unzstd roscon2023-rt-workshop-rpi4-ubuntu-22.04.1-ros2-humble.img.zst
```

Ubica una tarjeta microSD con más de 16GB de almacenamiento y usa [Etcher](https://etcher.balena.io/) para flashear el archivo de imagen resultante en tu tarjeta microSD.

#### Flashear la imagen directamente con un solo comando

Alternativamente, también puedes usar el comando:

```
$ unzstd roscon2023-rt-workshop-rpi4-ubuntu-22.04.1-ros2-humble.img.zst --stdout | sudo dd of=/dev/sdX bs=16M conv=fdatasync oflag=direct
```

Por favor, reemplaza `/dev/sdX` con el dispositivo correspondiente.

### Iniciar y conectar el Raspberry Pi 4

Inicia el Raspberry Pi normalmente. No necesitas conectar un monitor o un teclado. Para conectarte al Raspberry Pi:

1. Conecta el cable Ethernet al único puerto Ethernet del Raspberry Pi 4.
2. Conecta el otro extremo del cable Ethernet a tu portátil. Es posible que necesites hacer esto a través de un adaptador USB Ethernet si tu portátil no tiene un puerto Ethernet.
3. Espera un momento para que el Raspberry Pi 4 arranque y tu portátil se conecte a la red. Esto puede tardar unos minutos en el primer arranque.
4. Una vez conectado, el Raspberry Pi 4 es accesible en la dirección IP `192.168.10.1`.
5. Puedes conectarte con `ssh ubuntu@192.168.10.1`. La contraseña es **`ubuntu`**.
6. El código para los ejercicios se encuentra en la ruta `/code`.

### Ejecutar el ejercicio 1 precompilado

La imagen no solo contiene el código fuente, sino también los binarios precompilados para todos los ejercicios. Esto es para reducir el tiempo necesario para compilar durante el taller. Por favor, verifica que esto funciona ejecutando el ejercicio 1:

1. Inicia sesión en el Raspberry Pi siguiendo las instrucciones anteriores.
2. `cd /code/exercise1`
3. `./run.sh`

Es posible que veas advertencias sobre bucles excedidos. Esto es esperado, ya que parte del ejercicio consiste en resolver esto.

Este programa generará un archivo de trazas ubicado en `/code/exercise1/exercise1.perfetto`. Puedes descargarlo vía `scp` o usar el navegador como se documenta a continuación.

### Conectar al servidor HTTP incorporado del Raspberry Pi 4 para descargar archivos

La imagen contiene un servidor HTTP incorporado donde puedes explorar el directorio `/code` ubicado en el Pi. Esto te permite descargar archivos de trazas más fácilmente, lo cual haremos durante el taller. Por favor, verifica que esto está funcionando antes del taller:

1. Con el Ethernet conectado al Raspberry Pi, ve a http://192.168.10.1/repo/.
2. Haz clic en `exercise1` y descarga `exercise1.perfetto` (esto solo existirá si ejecutaste el ejercicio 1 como se indicó en la sección anterior).
3. Ve a http://192.168.10.1/perfetto/
4. En la parte superior izquierda de la pantalla, haz clic en `Open trace file` y abre el archivo `exercise1.perfetto` que acabas de descargar.
5. Debería aparecer una vista de línea de tiempo. Puedes presionar `W` en tu teclado para hacer zoom. Presiona `?` para obtener ayuda sobre cómo usar la interfaz si deseas explorar más a fondo.
