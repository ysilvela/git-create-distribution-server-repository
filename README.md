# git-create_distribution_server_repository
HowTo create a Git Server repository with distribution capacities suing Hooks

Pasos a seguir para subir ficheros por GIT a un servidor y desplegarlos:

Se necesita en el servidor dos carpetas. Una con la configuracion del repositorio y otra distinta donde desplegaremos el codigo subido

1. Primero creamos en el servidor las capetas donde va a estar el repositorio:

cd /opt/git
mkdir mysite.git
cd mysite.git
git --bare init

2.Ahora creamos el hook que hara que cuando hagamos push en el repositorio, este se copie en el directorio a desplegar:

cd /opt/git/mysite.git/hooks
cat > post-receive

3. Copiamos el siguiente codigo en el fichero. La carpeta de destino especificada en WORK_TREE debe existir, sino falla.:

'#!/bin/sh

_TREE=/opt/node/apps/tiresias
GIT_WORK_TREE=$_TREE git checkout -f
cd $_TREE
pwd
echo "Installing dependencies if not a docker with node"
npm install
ls -la $_TREE
echo "Starting service"

Si la definicion del docker ya tiene npm y nodejs entonces y hacce la instalacion (tutum) entonces no es necesario hacer npm install

4. Damos permisos de escritura. Es muy importante que el usuario ssh tenga acceso de escritura a este directorio y subdirectorios (Los del repositorio y el WORK_TREE)

chmod +x post-receive

5.Ahora ya en el cliente clonamos el repositorio:

git clone ysilvela@192.168.1.6:/opt/git/mysite.git

6. Luego ya podemos hacer add, commit y push


IMPORTANTE:
1. Se debe instalar previamente npm y nodejs con (En Ubuntu):
      apt-get install npm
      apt-get install node (Ir a la pagina de nodejs para ver como se instala -> https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions)
2. Para poner las claves ssh hay que copiar el fichero .pub en la carpeta .ssh del home del usuario y renonbrar el fichero como authorized_keys. Para mas info visitar 	(https://git-scm.com/book/es/v1/Git-en-un-servidor-Preparando-el-servidor)

La guia completa esta en http://seanvbaker.com/using-git-to-deploy-node-js-sites-on-ubuntu/

