Para llevar tu proyecto de **Caffenio** al siguiente nivel, necesitas dominar el entorno de comandos de Firebase. Esta es la base para conectar tu código con la nube de Google de forma profesional.

Aquí tienes la guía técnica paso a paso:

---

## 1. Software Base: Node.js y NPM en Windows

Para usar el CLI de Firebase, necesitas **Node.js**, que incluye automáticamente **NPM** (Node Package Manager).

### Cómo verificar si ya lo tienes
Abre una terminal (PowerShell o CMD) y escribe:
```bash
node -v
npm -v
```
Si ves números de versión (ej. `v20.10.0`), ¡estás listo! Si aparece un error, sigue estos pasos:

### Instalación Paso a Paso (Desde Cero)
1.  **Descarga:** Ve al sitio oficial [nodejs.org](https://nodejs.org/) y descarga la versión **LTS** (es la más estable).
2.  **Instalación:** Ejecuta el instalador `.msi`. 
3.  **Configuración Global:** Durante la instalación, asegúrate de que la opción **"Add to PATH"** esté marcada. Esto es lo que permite que el comando `npm` funcione de manera global en cualquier carpeta.
4.  **Herramientas adicionales:** Si el instalador te pregunta si deseas instalar "Tools for Native Modules", marca la casilla (esto instalará Chocolatey y scripts de Python necesarios para procesos internos).
5.  **Reinicia:** Es vital cerrar y volver a abrir tu terminal para que reconozca los nuevos comandos.

---

## 2. Instalación de Firebase CLI

Una vez que NPM está funcionando, instalaremos las herramientas de Firebase.

### Comando de Instalación Global
El parámetro `-g` indica que la instalación es **global**, permitiéndote usar el comando `firebase` en cualquier ruta de tu computadora:

```bash
npm install -g firebase-tools
```

---

## 3. Comandos Esenciales de Firebase

Aquí es donde vinculas tu cuenta de Google con tu proyecto Flutter.

### Acceder a Firebase (Login)
Para conectar tu PC con tu cuenta de Google:
```bash
firebase login
```
* Esto abrirá una pestaña en tu navegador.
* Selecciona tu cuenta de Google (donde creaste la base de datos `cafes`).
* Acepta los permisos. Al terminar, verás un mensaje de éxito en la terminal.

### Comandos de Uso Frecuente
| Comando | Propósito |
| :--- | :--- |
| `firebase projects:list` | Muestra todos tus proyectos creados en la consola. |
| `firebase init` | Inicia el asistente para configurar funciones (Hosting, Firestore, etc.) en tu carpeta local. |
| `firebase deploy` | Sube tus reglas de seguridad o funciones al servidor. |
| `firebase logout` | Cierra la sesión de tu cuenta en la máquina actual. |

---

## 4. El "Puente" para Flutter: FlutterFire CLI

Aunque ya tengas `firebase-tools`, Flutter utiliza una herramienta específica para generar el archivo de configuración `firebase_options.dart` automáticamente.

1.  **Instalar el activador de Dart:**
    ```bash
    dart pub global activate flutterfire_cli
    ```
2.  **Configurar tu app de Caffenio:**
    Dentro de tu carpeta `crudcaffenio`, ejecuta:
    ```bash
    flutterfire configure
    ```
    Este comando es mágico: te pedirá elegir tu proyecto de la lista, creará las apps en Android/iOS por ti y descargará los archivos necesarios.



---

## Práctica de Verificación para Estudiantes

Para asegurar que todo está correcto antes de programar el CRUD, realiza este "Checklist":

1.  **Agente de Configuración:** Ejecuta `firebase projects:list`. ¿Aparece tu proyecto de Caffenio?
2.  **Skill de Dependencia:** Ejecuta `npm list -g firebase-tools`. ¿Aparece la versión instalada?
3.  **Flujo de Trabajo:** Ejecuta `flutterfire configure`. Si termina sin errores en rojo, tu entorno está 100% optimizado para trabajar con la metodología Antigravity.

¿Lograste ver la lista de tus proyectos en la terminal o tuviste algún problema con los permisos de Windows?
