# SSH (Secure Shell)

SSH (Secure Shell) es un protocolo de red que permite una comunicación segura y cifrada entre dos dispositivos a través de una red. Proporciona una forma segura de acceder y administrar de forma remota sistemas y servidores.

Puedes acceder a datos y escribirlos en repositorios en GitHub.com mediante SSH. Al conectarse a través de SSH, se realiza la autenticación mediante un archivo de clave privada en el equipo local y una clave publica en GitHub. Esto permite que desde el equipo local se puedan "empujar" cambios a un repositorio en GitHub. 

A continuación se describe el proceso para  generar la llave publica y la llave privada necesarias para utilizar SSH entre el quipo local y GitHub.

# Generacion de llaves SSH en Mac

## Paso 1: Generación de llaves SSH

Abrir la terminal y entrar a la carpeta .ssh con
```
$cd ~/.ssh
```

En caso de que no exista la carpeta __.ssh__ se debe crear y luego moverse dentro de ella con
```
$mkdir ~/.ssh
$cd .ssh
```

Es importante que se asegure de que esta dentro de la carpeta __.ssh__ antes de que continue con el proceso. 

Para eso escriba el comando `pwd` que imprimirá la ruta actual, en la cual podra ver que efectivamente esta dentro de la carpeta.
```
$pwd
/Users/usuario_local/.ssh
```

Ya dentro de la carpeta __.ssh__ es necesario generar las llaves publica y privada SSH utilizando el siguiente comando.
```
$ssh-keygen -t ed25519 -C "your_email@itsoeh.edu.mx"
```

Este comando creará una llave privada  y una llave publica, esta ultima puede ser etiquetada. En este caso la etiqueta será el correo electrónico con el cual se creo la cuenta en GitHub.com. Por lo tanto en el comando anterior se debe reemplazar `your_email@itsoeh.edu.mx` por el correo con el que se creo la cuenta de GitHub.com (de preferencia el correo institucional). 


El comando anterior solicitará que escriba el nombre del archivo en el cual se van a guardar las llaves, en este caso se debe escribir `github`.
```
Generating public/private ALGORITHM key pair.  
Enter a file in which to save the key (/Users/YOU/.ssh/id_ALGORITHM): github [Presionar enter]  
```

Despues pedira que se ingrese una contraseña (passphrase) para agregar mas seguridad a las llaves, la cual se debe volver a escribir para ser validada. `Es importante mencionar que cuando se escribe una contraseña en la terminal no aparecen los caracteres o algun indicador visual de que se esta escribiendo, esto por motivos de seguridad`.
```
Enter passphrase (empty for no passphrase): [Escribir contraseña y Presionar enter]  
Enter same passphrase again: [Escribir contraseña y Presionar enter]  

```



# Paso 2: Agregar tu clave SSH al ssh-agent

El `ssh-agent` es un administrador de llaves por lo que se debe de iniciar con el siguiente comando
```
eval "$(ssh-agent -s)"
Agent pid 59566
```
Ahora que el agente ha iniciado se deben cargar las llaves al agente y guardar la contraseña en el `keychain`. Para realizar esto, es necesario editar el archivo `~/.ssh/config`. 

Abrir el archivo `~/.ssh/config`. En caso de que no exista el archivo, este sera creado al abrirlo con VScode.
```
code config
```

Pegar la siguiente configuración en el archivo `config` y guardar los cambios.

```
Host github.com  
  AddKeysToAgent yes  
  UseKeychain yes  
  IdentityFile ~/.ssh/github  
```


Con el siguiente comando se agregan las llaves al agente y la constraseña al keychain.
```
ssh-add --apple-use-keychain ~/.ssh/github
```


# Paso 3: Agregar la llave publica a GitHub.com 
Ahora es necesario agregar el contenido del archivo de la llave publica `github.pub` a GitHub.com. El siguiente comando agrega el contenido de la llave publica `github.pub` al portapapeles.

```
pbcopy < ~/.ssh/github.pub
```

Ahora entrar a GitHub.com y agregar la llave publica, ver la siguiente imagen.
![Agregar llave publica a GitHub.com](/resources/llave_publica.gif)

Nota: Como la llave ya esta en el portapapeles solo debe presionar `Command + v` dentro de la caja de texto etiqueta como `key` y después presionar el boton verde `Add SSH key`.

# Paso 4: Verificar que las llaves funcionan adecuadamente
Con el siguiente comando verificamos que podamos conectarnos a github.com utilizando nuestro par de llaves publica y privada.
```
ssh -T git@github.com
```

El comando anterior produce algo parecido a lo que aparece debajo.
```
The authenticity of host 'github.com (IP ADDRESS)' can't be established.  
ED25519 key fingerprint is SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU.  
Are you sure you want to continue connecting (yes/no)? yes [Presiona enter]

Hi USERNAME! You've successfully authenticated, but GitHub does not provide shell access.
```
