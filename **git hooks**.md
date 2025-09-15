**git hooks**
Cada repositorio local tiene su propia carpeta .git/hooks/.
Esa carpeta se crea automáticamente cuando inicializas el repo (git init o al clonar).

Está oculta dentro de la carpeta .git (junto con toda la configuración interna de Git).
Esa carpeta está oculta dentro de tu repo local (no en GitHub).
  1.
     <u>cd .git/hooks
    ls</u>

2.Crear un archivo nuevo vacío:
    <u>nano commit-msg:</u>

    Escribir el script del hook
    Ejemplo: validar que el mensaje del commit tenga mínimo 10 caracteres:

        #!/bin/sh
        MSG_FILE=$1
        MSG_CONTENT=$(cat "$MSG_FILE")

        if [ ${#MSG_CONTENT} -lt 10 ]; then
        echo "❌ El mensaje de commit debe tener al menos 10 caracteres."
        exit 1
        fi

        exit 0

3. Dar permiso de ejecucción.
chmod +x commit-msg

Cuando se instala LFS, se instalan un hooks importantes por defecto:
pre-push Hook: Este hook se encarga de que, cuando haces un git push, Git LFS primero suba los archivos grandes al servidor de LFS. Esto asegura que los archivos estén disponibles antes de que Git suba el pequeño puntero al repositorio principal.

Scripts que se ejecutan en ciertos momentos, algunos puestos por LFS.
Son scripts que se ejecutan automáticamente (como workflows en Github) en ciertos eventos (ejemplo: antes de un commit, después de un push).
Los hooks se almacenan en el directorio .git/hooks/ de cada repositorio.

Git los incluye por defecto (terminan en .sample). como ejemplos (pre-commit.sample, etc.), pero desactivados. Tienes que configurarlos.
Si renombras uno (quitando .sample) o creas un archivo nuevo en esa carpeta, ya funciona.

Se configuran en la carpeta oculta.
Cada repo local tiene una carpeta oculta .git/ donde Git guarda toda la “caja negra” del repo (objetos, configuración, refs, etc).
Dentro está la subcarpeta .git/hooks/.
Allí verás varios archivos como:

pre-commit.sample
commit-msg.sample
pre-push.sample
...

Esos son ejemplos (plantillas), desactivados porque terminan en .sample.

1.Cambias el nombre quitando .sample:

    mv .git/hooks/pre-commit.sample .git/hooks/pre-commit

2.Editas el contenido del archivo con tu script (ejemplo en Bash).
Ejemplo de un hook pre-commit sencillo:

    #!/bin/sh
    echo ">>> Revisando que no haya archivos .log antes de commitear..."
    if git diff --cached --name-only | grep "\.log"; then
    echo "❌ No puedes commitear archivos .log"
    exit 1
    fi

3.Le das permisos de ejecución:
    chmod +x .git/hooks/pre-commit

Esto es lo que significa “permisos de ejecución”: el sistema operativo debe reconocer que ese archivo se puede ejecutar como un programa.
Resultado: cuando intentes hacer git commit, si hay .log en staging, el hook bloquea el commit.

Ejemplos:

    pre-commit → se ejecuta antes de hacer commit (ej: revisar formato del código, impedir commits con errores).

    commit-msg → valida el mensaje del commit.

    pre-push → se ejecuta antes de subir al remoto (ej: correr tests).

Importante:
Los hooks son locales, no viajan con el repo al remoto (GitHub).

Si quieres compartirlos con tu equipo, hay que guardarlos en otra carpeta (ej: .githooks/) y configurarlos con:

    git config core.hooksPath .githooks
