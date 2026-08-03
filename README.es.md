<!-- hide -->
<div align="center">

# Instalar Windows en una máquina virtual

[![Tutorial certificado](https://img.shields.io/badge/4Geeks-tutorial%20certificado-2563eb)](https://github.com/4GeeksAcademy/installing-windows-on-virtual-machine)
[![Autocorregido con LearnPack](https://img.shields.io/badge/LearnPack-autocorregido-2563eb)](https://github.com/learnpack/learnpack)
[![Abrir en Codespaces](https://img.shields.io/badge/Abrir%20en-Codespaces-fb5a1f)](https://codespaces.new/?repo=4GeeksAcademy/installing-windows-on-virtual-machine)

*These instructions are also [available in English](https://github.com/4GeeksAcademy/installing-windows-on-virtual-machine/blob/HEAD/README.md)*

</div>
<!-- endhide -->

Monta tu primer laboratorio de seguridad: este tutorial interactivo instala Windows 10 dentro de una máquina virtual de VirtualBox en 5 pasos guiados — instalar VirtualBox en Windows, Linux o macOS con Intel, crear un ISO de Windows 10 y montar la máquina con instalación desatendida. El último paso se autocorrige con 3 comprobaciones de Jest: que la máquina sea Windows 10 y tenga al menos 2048 MB de RAM y 2 CPUs.

<!-- hide -->
## 📋 Sobre este tutorial

- **Dificultad:** fácil (`"difficulty": "easy"` en [learn.json](https://github.com/4GeeksAcademy/installing-windows-on-virtual-machine/blob/HEAD/learn.json))
- **Duración estimada:** 5 horas (`"duration": 5`)
- **Pasos:** 5 ejercicios en orden, el último autocorregido
- **Tecnologías:** ciberseguridad, windows (`"technologies"` en learn.json)
- **Corrección:** LearnPack + Jest, 3 comprobaciones en un único fichero de test
- **Idiomas:** español e inglés, paso a paso
- **Dónde se ejecuta:** GitHub Codespaces o una instalación local de LearnPack (`"localhostOnly": true`)
- **Código que tienes que escribir:** ninguno — aquí se configura una máquina virtual, no se programa
<!-- endhide -->

## 🎯 ¿Qué vas a aprender?

La virtualización es lo primero que monta quien empieza en seguridad, porque es la única forma segura de romper cosas. Al terminar sabrás:

- Instalar **VirtualBox** en Windows con el asistente oficial, en Linux basado en Debian con un único `wget ... | sudo sh`, en distribuciones RPM (Oracle Linux/RHEL, Fedora, openSUSE) añadiendo el repositorio de Oracle, y en macOS con procesador Intel con el instalador `.dmg`.
- Usar la **herramienta de creación de medios** de Microsoft para generar un archivo ISO en lugar de un USB, eligiendo tú la arquitectura en vez de copiar la del equipo donde la ejecutas.
- Crear una máquina virtual en el **Modo experto** de VirtualBox: nombre, tipo de sistema operativo, imagen ISO y disco duro virtual.
- Aprovechar la **instalación desatendida** para que VirtualBox rellene por ti todo el instalador de Windows, incluida la clave de producto genérica que instala Windows pero no lo activa.
- Dimensionar el hardware de la máquina virtual — núcleos y memoria base — para que funcione bien **sin ahogar al equipo anfitrión**.
- Leer la configuración real de una máquina desde la terminal con `VBoxManage showvminfo --machinereadable`, que es justo lo que el tutorial usa para comprobar tu trabajo.

## 👀 ¿Qué vas a construir?

Una máquina virtual con Windows 10 funcionando, que VirtualBox reporte como Windows 10 y con al menos 2 CPUs y 2048 MB de memoria. Llegarás ahí con los 5 ejercicios de la carpeta `exercises/`:

1. **`01-welcome`** — el mapa del recorrido: qué es un laboratorio virtual y por qué lo peor que puede pasar dentro de él es tener que reinstalar la máquina.
2. **`02-install-virtual-box`** — la instalación de VirtualBox, con un camino distinto para Windows, Linux Debian, Linux RPM y macOS con Intel.
3. **`03-download-windows-iso`** — descargar la herramienta de creación de medios de Windows 10, aceptar los términos, elegir "Crear medios de instalación", personalizar la arquitectura y guardar el archivo `.iso`.
4. **`04-create-new-vm`** — crear la máquina: tipo de sistema, ISO, instalación desatendida con clave de producto, mínimo 2 CPUs y 2 GB de memoria, y el almacenamiento recomendado.
5. **`05-Verify-Installation`** — instalar `curl`, publicar el puerto `3001`, ejecutar el script de validación y pasar las pruebas.

El paso del hardware es el que decide si apruebas. VirtualBox lo abre con **1 CPU seleccionada por defecto**, y ese valor no pasa el test:

![Asistente Crear máquina virtual de VirtualBox en Modo experto, con la sección Hardware desplegada, el deslizador de Memoria base en 2048 MB y el de Procesadores todavía en su valor por defecto de 1 CPU, con los botones Terminar y Cancelar abajo](https://raw.githubusercontent.com/4GeeksAcademy/installing-windows-on-virtual-machine/master/.learn/assets/createvm3.png)

Sube el deslizador de Procesadores a 2 o más antes de pulsar Terminar.

## 🎓 ¿Qué necesitas antes de empezar?

- **Un equipo donde se pueda instalar VirtualBox:** Windows, un Linux Debian/Ubuntu o basado en RPM, o un Mac con procesador Intel. El tutorial enlaza el instalador de macOS solo para procesadores Intel, así que los Mac con Apple Silicon quedan fuera.
- **Hardware libre para la máquina virtual:** al menos 2 núcleos y 2048 MB de RAM que el anfitrión pueda prestarle, más espacio en disco para el ISO de Windows 10 y para el disco duro virtual.
- **Paciencia para dos descargas:** el ISO es un archivo muy pesado y, después, la instalación desatendida corre sola hasta que Windows arranca.
- **`curl` en el equipo donde vive VirtualBox**, porque el script de validación lo usa para subir el informe. En Windows puedes instalarlo desde [curl.se/windows](https://curl.se/windows/).
- **Una cuenta de GitHub** si prefieres hacer los ejercicios en Codespaces en lugar de instalar LearnPack en tu equipo.
- **Nada de experiencia programando.** No hay código que escribir: el fichero de test ya viene hecho.

## ✅ ¿Cómo funciona la autocorrección?

Solo se corrige el último ejercicio, y se corrige contra tu máquina real, no contra un texto que escribas. La cadena tiene cuatro eslabones:

1. El Codespace ejecuta `.devcontainer/postCreate.sh` al arrancar, y ese script lanza `node server.js` — un pequeño receptor Express que escucha en el puerto indicado en `.env` (`HOST_PORT=3001`). LearnPack no lo arranca, así que fuera de un Codespace lo levantas tú.
2. Publicas el puerto `3001` desde el panel **PUERTOS** de tu Codespace, para que tu propio equipo pueda alcanzarlo desde fuera.
3. Descargas y descomprimes el script de validación — `sendDataWin.zip` para Windows, `sendDataBash.zip` para Linux —, lo ejecutas en el equipo donde está instalado VirtualBox y pegas la URL del puerto `3001` cuando te la pida. El script lanza `VBoxManage list vms`, vuelca `VBoxManage showvminfo --machinereadable` de cada máquina en `data.txt` y lo sube con `curl -X POST -F "file=@data.txt"`.
4. El servidor interpreta ese volcado y escribe `vminfo.json`, que es lo que lee el test.

Publicar el puerto es el paso que más se olvida. En la pestaña de puertos, haz clic derecho sobre la fila del puerto `3001`, abre **Visibilidad del puerto** y elige **Public**:

![Panel de puertos de Visual Studio Code dentro de un Codespace de GitHub, con la entrada node_server en el puerto 3001 y el menú contextual abierto en el submenú Visibilidad del puerto con la opción Public marcada](https://raw.githubusercontent.com/4GeeksAcademy/installing-windows-on-virtual-machine/master/.learn/assets/public-ports.png)

El fichero [`exercises/05-Verify-Installation/test.js`](https://github.com/4GeeksAcademy/installing-windows-on-virtual-machine/blob/HEAD/exercises/05-Verify-Installation/test.js) ejecuta entonces 3 comprobaciones sobre la máquina cuyo `ostype` empieza por `Windows 10`:

- Esa máquina con Windows 10 tiene que existir en el informe.
- Su valor de `memory` tiene que ser mayor o igual que `2048`.
- Su valor de `cpus` tiene que ser mayor o igual que `2`.

Si `vminfo.json` no está, la suite se corta antes de la primera comprobación con el mensaje `vminfo.json not found. Please run the BAT file on your local machine.`

## 💡 ¿Qué errores conviene evitar?

- **Ejecutar el script de validación dentro del Windows invitado.** El script llama a `VBoxManage`, que forma parte de VirtualBox y vive en el anfitrión, así que hay que lanzarlo en el equipo donde instalaste VirtualBox.
- **Dejar el deslizador de Procesadores en 1.** Es el valor por defecto, y la tercera comprobación pide 2 o más. Una memoria base por debajo de 2048 MB falla igual.
- **Dejar el puerto `3001` en privado.** El envío nunca llega, `vminfo.json` no se crea y todos los tests fallan con el error de "not found" aunque tu máquina virtual esté perfecta.
- **Elegir otro tipo de sistema operativo.** El informe se compara contra `Windows 10`, así que una máquina creada como Windows 11 o como "Other" genérico no se reconoce.
- **Saltarte la clave de producto en la instalación desatendida.** El tutorial avisa de que dejarla vacía provoca errores durante la instalación; la clave que te da instala Windows, pero no lo activa.
- **Ejecutar el script sin descomprimirlo antes,** o pegar una URL que no sea la dirección reenviada del puerto `3001`.

## ❓ Preguntas frecuentes

### ¿Necesito una licencia de Windows para esto?

La instalación desatendida usa una clave de producto genérica que permite terminar el instalador pero no activa Windows. Acabas con un Windows 10 sin activar, suficiente para el laboratorio y para pasar las pruebas.

### ¿Puedo hacerlo en un Mac con Apple Silicon (M1, M2, M3)?

Con estas instrucciones, no. El tutorial enlaza el `.dmg` de VirtualBox para procesadores Intel y cubre anfitriones Windows y Linux. En un Mac con Apple Silicon necesitarías otra herramienta de virtualización, que queda fuera de estos 5 pasos.

### ¿Cuánta RAM y cuántas CPUs le doy a la máquina virtual?

Como mínimo 2048 MB y 2 CPUs, porque son exactamente los mínimos que comprueban los tests. Subir de ahí hace que Windows vaya más fino, pero nunca le cedas tanto que el equipo anfitrión empiece a arrastrarse: la máquina virtual toma prestado el hardware, no lo añade.

### ¿Tengo que escribir código?

No. Los ejercicios son configuración y capturas de pantalla; el fichero de test ya está escrito y lo único que produces es una máquina virtual funcionando y el informe que sube el script.

### ¿Por qué el test me dice `vminfo.json not found`?

Porque el servidor nunca recibió tu informe. Lo habitual es que el puerto `3001` siga en privado, que hayas lanzado el script dentro de la máquina virtual en vez de en el anfitrión, o que la URL pegada no sea la dirección reenviada del puerto `3001`.

### ¿Puedo hacer el tutorial sin GitHub Codespaces?

Sí. Clona el repositorio, ejecuta LearnPack en tu equipo y arranca tú mismo el receptor con `node server.js`: fuera de un Codespace nadie lo levanta por ti. Entonces escuchará en `HOST_PORT=3001` en tu propia máquina, así que la dirección que pegas en el script de validación será `localhost:3001` en lugar de una URL de Codespaces.

<!-- hide -->
## 📝 Otros tutoriales interactivos

- [Aprende Python Interactivamente (Principiante)](https://4geeks.com/es/interactive-exercise/python-beginner-exercises-es)
- [Domina Python Practicando (interactivo)](https://4geeks.com/es/interactive-exercise/master-python-exercises-es)

## 🚀 Cómo empezar

Lo más rápido es [abrir el repositorio en Codespaces](https://codespaces.new/?repo=4GeeksAcademy/installing-windows-on-virtual-machine). Cuando se abra VS Code, los ejercicios de LearnPack deberían arrancar solos; si no lo hacen, escribe `learnpack start` en la terminal.

Recuerda que la máquina virtual se crea en **tu propio ordenador**, no dentro del Codespace: el Codespace solo aloja las instrucciones y el servidor de validación.

## 💻 Instalación local

1. Clona el repositorio ([cómo clonar un repositorio](https://4geeks.com/how-to/github-clone-repository)) y entra en la carpeta:

   ```bash
   git clone https://github.com/4GeeksAcademy/installing-windows-on-virtual-machine.git
   cd installing-windows-on-virtual-machine
   ```

2. Instala LearnPack y el plugin compilador de node de forma global (necesitas Node.js):

   ```bash
   npm i @learnpack/learnpack -g
   learnpack plugins:install @learnpack/node
   ```

3. Instala las dependencias del proyecto y arranca el tutorial al mismo nivel que `learn.json`:

   ```bash
   npm i
   learnpack start
   ```

4. Para el último ejercicio, abre una segunda terminal y arranca el receptor de validación, que fuera de un Codespace no lo levanta nadie por ti:

   ```bash
   node server.js
   ```

## 📚 Cómo están organizados los ejercicios

- `exercises/` contiene las 5 carpetas numeradas, y LearnPack las recorre en orden.
- Cada carpeta tiene un `README.md` y un `README.es.md`, así que puedes cambiar de idioma en cualquier paso.
- Solo `05-Verify-Installation` tiene `test.js`; los otros cuatro son de leer y hacer.
- `learn.json` guarda la configuración del tutorial: título, descripción, duración, dificultad y tecnologías.
- `server.js` es el receptor Express que convierte el volcado de VirtualBox en `vminfo.json`.
- `.learn/assets/` guarda las capturas y los dos scripts de validación, empaquetados como `sendDataWin.zip` y `sendDataBash.zip`.

## 🤝 Colaboradores

Gracias a estas personas maravillosas:

1. [Arnaldo Perez (arnaldoperez)](https://github.com/arnaldoperez), contribución: build-tutorial, documentación
2. [Alejandro Sánchez (alesanchezr)](https://github.com/alesanchezr), contribución: detector de bugs
3. [Lorena Gubaira (lorenagubaira)](https://github.com/lorenagubaira), contribución: detectora de bugs, editora, traducción

Puedes ver la lista completa en el [gráfico de contribuidores](https://github.com/4GeeksAcademy/installing-windows-on-virtual-machine/graphs/contributors). ¿Encontraste un fallo o una falta de ortografía? [Abre un issue](https://github.com/learnpack/learnpack/issues/new): estos ejercicios se construyen y se mantienen junto a personas como tú.

Este y muchos otros tutoriales interactivos los crean estudiantes y mentores de 4Geeks Academy, y se usan en el [Bootcamp de Ciberseguridad](https://4geeksacademy.com/es/coding-bootcamps/curso-ciberseguridad).
<!-- endhide -->
