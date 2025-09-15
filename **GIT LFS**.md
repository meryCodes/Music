**GIT LFS**
El archivo grande real se guarda  en un sistema de almacenamiento LFS
y en el repo guarda un puntero (un pequeño texto con metadatos).
Git LFS también usa sus propios hooks internos para que cuando hagas git add o git push los archivos grandes se transformen en punteros automáticamente
Git LFS por sí solo no sabe qué archivos debe gestionar.
Las reglas de qué extensión o qué archivo va a usar LFS se guardan en .gitattributes.

1. Instalar Git LFS (una sola vez en tu máquina) relación con ~/.gitconfig (ANEXO1) global (QUÉ HACER CON LOS ARCHIVOS LFS)

    git lfs install

Esto agrega a tu ~/.gitconfig global Esos filtros le dicen a Git cómo manejar archivos grandes cuando se suben o descargan.

Git mete en tu archivo ~/.gitconfig (global) unas reglas especiales llamadas filtros (clean, smudge, process, required).
Esas reglas le dicen a Git cómo tratar archivos grandes:

    clean → qué hacer antes de guardar el archivo en el repo (lo convierte en un “puntero”).
    smudge → qué hacer al descargarlo (recuperar el archivo real).
    process → cómo comunicar Git ↔ LFS.
    required → que siempre se aplique LFS si el archivo lo necesita.


2. Decir qué archivos quieres que maneje LFS (por repositorio) relación con .gitattributes (ANEXO2) (EN QUÉ ARCHIVOS APLICAR LFS)

Ejemplo: si en tu proyecto quieres que las imágenes pesadas se guarden con LFS:

git lfs track "*.png"
git lfs track "*.jpg"
git lfs track "*.mp4"


Esto genera/modifica el archivo .gitattributes en tu repo.
Ejemplo de .gitattributes resultante:

*.png filter=lfs diff=lfs merge=lfs -text
*.jpg filter=lfs diff=lfs merge=lfs -text
*.mp4 filter=lfs diff=lfs merge=lfs -text

Relación: .gitconfig tiene las reglas generales de cómo funciona LFS, y .gitattributes dice qué archivos específicos usarán esas reglas.

3. Añadir y subir normalmente
<u>git add .gitattributes
git commit -m 
git push origin main</u>


Git se encarga de guardar el archivo en LFS y en tu repo solo queda un “puntero” (no el archivo pesado completo).

.gitconfig → se configura solo cuando instalas LFS (git lfs install), afecta a tu máquina.

.gitattributes → se guarda en el repositorio, define qué tipos de archivos usarán LFS.

---

**ANEXO 1:**
**.gitconfig (puede estar en varios niveles):** 
Para ver la configuracion debo de estar en la carpeta del repo que me interesa 
    cd Music
Archivo de configuración con tus preferencias por secciones que controla como se comporta Git

    Global (~/.gitconfig en tu usuario) → contiene tu nombre, email, alias, y configuraciones de Git.
        git config --global --list


    Local (.git/config dentro de cada repo) → configuraciones específicas para ese proyecto.
        git config --local --list



En la misma pagina del terminal escribo los git alias/atajos uno a uno

git config --global alias.s "status" estado normal (git status).
git config --global alias.lg "log --oneline --graph --all"historial resumido y gráfico.
git config --global alias.ch "checkout"cambiar de rama o restaurar archivos.
git config --global alias.co "commit -m"commit directo con mensaje.

Para comprobar y ver todas configuración sin abrir archivos, listado:
  <u>  git config --list</u>
    para salir pulsar tecla q
Para ver el origen de cada configuración:
   <u> git config --list --show-origin</u>(te muestra el origen de cada cinfiguración)

---

**ANEXO2:.gitattributes** 
Se guarda en el repo como archivo de texto, igual que el .gitignore.
Y como está dentro del repo, cuando alguien clona tu proyecto, también se baja el .gitattributes.
Así, todos los que trabajen en ese repo respetan las mismas reglas.
Configurar fusión automática para algunos tipos de archivo.
Controla cómo Git maneja ciertos archivos:

Forzar finales de línea (LF vs CRLF).
Decirle a Git cómo hacer diff en archivos binarios o de idiomas especiales.
Define atributos de archivos, por ejemplo:
Usar Git LFS para ciertos tipos (*.jpg filter=lfs).
Reglas de fin de línea (*.sh text eol=lf).

Solo lo tienes si configuraste Git LFS o reglas específicas.

1.Siempre en local → tú creas o editas el archivo .gitattributes dentro de tu repo.
    <u>nano .gitattributes</u>
Se abre el editor y pego dentro:
Archivos de audio
*.mp3 filter=lfs diff=lfs merge=lfs -text
*.wav filter=lfs diff=lfs merge=lfs -text

Archivos de video
*.mp4 filter=lfs diff=lfs merge=lfs -text
*.mov filter=lfs diff=lfs merge=lfs -text

Archivos de imágenes
*.jpg filter=lfs diff=lfs merge=lfs -text
*.png filter=lfs diff=lfs merge=lfs -text

Guardo y cierro


2.Luego haces:
    <u>git add .gitattributes
    git commit -m "Añadir reglas de atributos"
    git push origin main
    git status (me tiene que decir que el arbol esta limpio)</u>

3.Una vez subido, cualquier persona que clone ese repo se lo lleva automáticamente → y Git en su máquina aplicará esas reglas.

Cuando él haga git add imagen.png, Git automáticamente lo manda a LFS (porque .gitattributes se lo indica).
Ese archivo no se “ejecuta” como un programa, sino que Git lo lee automáticamente cada vez que trabajas en ese repo.

En GitHub deberías ver que el archivo no pesa casi nada, sino que contiene un puntero LFS (unas líneas de texto que empiezan por version https://git-lfs.github.com/...


