# Guía de conexión al cluster HPC de la Facultad de Ingeniería desde Linux

Esta guía explica cómo conectarse al HPC desde Linux usando OpenConnect para la VPN institucional y SSH con autenticación mediante llaves RSA.

El acceso al VPN y al HPC depende de una aprobación previa de los administradores. Primero debe generar su llave pública, enviarla junto a sus datos mediante el formulario y esperar una respuesta favorable antes de intentar conectarse.

## Introducción

El acceso al cluster no está disponible directamente desde Internet. Primero debe establecerse una conexión VPN con la Universidad Nacional.

## Arquitectura general

```text
Computador personal
  |
  v
VPN UNAL con OpenConnect
  |
  v
Red interna UNAL
  |
  v
Cluster HPC
```

## Requisitos previos

- Cuenta institucional UNAL.
- Linux instalado.
- Acceso a terminal.

## Paso previo: Enviar la llave pública y esperar aprobación

Antes de conectarse a la VPN, debe completar el formulario con sus datos y adjuntar la llave pública generada en el Paso 1.

Formulario de acceso:

[Formulario de acceso](https://docs.google.com/forms/d/e/1FAIpQLSesV0MxEX2LuW0-qb3do4K5E4pAYCZPuE7c8N2kmcX-bvVP2Q/viewform?usp=header)

Hasta recibir una respuesta favorable, no debe intentar usar la VPN ni acceder al HPC.

## Paso 1: Generar una llave SSH RSA de 4096 bits

Genere una nueva llave RSA:

```bash
ssh-keygen -t rsa -b 4096 -C "correo@unal.edu.co"
```

Presione Enter cuando pregunte dónde guardar la llave para usar la ruta predeterminada.

Cuando se solicite una passphrase, puede dejarla vacía o definir una contraseña segura.

Se crearán los archivos:

```text
~/.ssh/id_rsa
~/.ssh/id_rsa.pub
```

La llave privada no debe compartirse. La llave pública sí debe enviarse al formulario de acceso al HPC.

## Paso 2: Visualizar la llave pública

Si desea revisar el contenido de la llave pública:

```bash
cat ~/.ssh/id_rsa.pub
```

## Paso 3: Verificar el tamaño de la llave

Verifique que la llave sea RSA de 4096 bits:

```bash
ssh-keygen -lf ~/.ssh/id_rsa.pub
```

## Paso 4: Instalar OpenConnect

Instale OpenConnect según su distribución:

### Debian / Ubuntu

```bash
sudo apt update
sudo apt install openconnect -y
```

### RHEL / Fedora / Rocky / AlmaLinux

```bash
sudo dnf install openconnect -y
```

### Arch Linux

```bash
sudo pacman -S openconnect
```

Verifique la instalación:

```bash
openconnect --version
```

## Paso 5: Conectarse a la VPN de la Universidad

Conéctese usando el protocolo de GlobalProtect:

```bash
sudo openconnect --protocol=gp vpnpa.unal.edu.co
```

Cuando aparezca `Username:`, ingrese su usuario institucional.

Cuando aparezca `Password:`, ingrese la contraseña institucional.

Mantenga la terminal abierta mientras use la VPN.

## Paso 6: Configurar SSH

En otra terminal, edite el archivo de configuración SSH:

```bash
nano ~/.ssh/config
```

Agregue este bloque:

```text
Host clusterFacIngUN
    HostName 168.176.27.104
    User usuarioInstitucional
    Port 22
    IdentityFile ~/.ssh/id_rsa
    IdentitiesOnly yes
```

Reemplace `usuarioInstitucional` por su usuario sin el dominio `@unal.edu.co`.

Guarde el archivo y ajuste permisos:

```bash
chmod 600 ~/.ssh/config
```

## Paso 7: Inicializar el agente SSH

Inicie el agente SSH y agregue la llave:

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```

Verifique que la llave quedó cargada:

```bash
ssh-add -l
```

## Paso 8: Conectarse al HPC

Con la VPN activa y la autorización del administrador, conecte con:

```bash
ssh clusterFacIngUN
```

Si desea compresión en la sesión para transferir grandes cantidades de datos, use:

```bash
ssh -CY clusterFacIngUN
```

## Primera conexión

La primera vez aparecerá un mensaje sobre la autenticidad del host. Responda:

```text
yes
```

Luego SSH guardará la huella digital del servidor.

## Conexión exitosa

Cuando el acceso sea correcto, verá el prompt del sistema remoto y podrá trabajar dentro del HPC.

## Desconectarse del cluster

Salga de la sesión SSH con:

```bash
exit
```

También puede usar `Ctrl + D`.

## Desconectarse de la VPN

Regrese a la terminal donde está ejecutándose OpenConnect y presione:

```text
Ctrl + C
```

La VPN se cerrará.
