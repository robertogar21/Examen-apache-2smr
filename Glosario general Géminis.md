# Glosario y Guía de Comandos: Apache en Ubuntu Server

Este documento recopila los comandos, rutas y configuraciones esenciales para la administración de un servidor web Apache, basado en las prácticas de la asignatura de Servicios en Red.

## 1. Instalación y Gestión del Servicio

Antes de configurar, es necesario instalar y saber controlar el estado del servidor.

### Instalación
* **Actualizar repositorios:**
    ```bash
    sudo apt update
    ```
    *[0]()Garantiza que se reflejen las versiones más recientes de los paquetes[0]().*

* **Instalar Apache:**
    ```bash
    sudo apt install apache2
    ```
    *[1]()Instala el servidor y todas sus dependencias requeridas[1]().*

### Gestión del proceso (Systemctl)
* **Verificar estado:**
    ```bash
    sudo systemctl status apache2
    ```
    *[2]()Comprueba si el servicio está activo y en ejecución[2]().*

* **Iniciar servicio:**
    ```bash
    sudo systemctl start apache2
    ```
    [3]()

* **Detener servicio:**
    ```bash
    sudo systemctl stop apache2
    ```
    [3]()

* **Reiniciar (Stop + Start):**
    ```bash
    sudo systemctl restart apache2
    ```
    *[4]()Se usa para aplicar cambios mayores[4]().*

* **Recargar (Reload):**
    ```bash
    sudo systemctl reload apache2
    ```
    *[5]()Aplica cambios de configuración sin perder las conexiones activas[5]().*

* **Habilitar al arranque:**
    ```bash
    sudo systemctl enable apache2
    ```
    *[6]()Configura Apache para iniciarse automáticamente al encender el servidor[6]().*

* **Deshabilitar al arranque:**
    ```bash
    sudo systemctl disable apache2
    ```
    [7]()

---

## 2. Directorios y Archivos Importantes

Es vital conocer la estructura de archivos de Apache para la configuración.

### Archivos de Configuración (`/etc/apache2`)
* **`/etc/apache2/apache2.conf`**: Archivo principal de configuración global[7]().
* **[8]()`/etc/apache2/ports.conf`**: Especifica los puertos de escucha (80 por defecto, 443 para SSL)[8]().
* **[9]()`/etc/apache2/sites-available/`**: Directorio donde se crean los archivos de configuración de los sitios virtuales (Virtual Hosts)[9]().
* **[10]()`/etc/apache2/sites-enabled/`**: Directorio con enlaces simbólicos a los sitios que están realmente activos[10]().

### Contenido Web y Logs
* **[11]()`/var/www/html`**: Directorio raíz por defecto donde se aloja el contenido web[11]().
* **[12]()`/var/log/apache2/access.log`**: Registro de todas las solicitudes hechas al servidor[12]().
* **[13]()`/var/log/apache2/error.log`**: Registro de errores del servidor[13]().

### Resolución de Nombres Local
Para probar dominios sin un servidor DNS real, se modifica el archivo `hosts`:
* **[14]()Linux:** `/etc/hosts`[14]().
* **[15]()Windows:** `C:\Windows\System32\drivers\etc\hosts`[15]().
* **[16]()Formato:** `IP nombre_dominio` (Ej: `192.168.3.1 www.ejemplo.com`)[16]().

---

## 3. Virtual Hosts (Sitios Virtuales)

[17]()Permite alojar varios dominios en un solo servidor[17]().

### Pasos de creación
1.  **Crear estructura de directorios:**
    ```bash
    sudo mkdir -p /var/www/[ejemplo.com/html](https://ejemplo.com/html)
    ```
    *[18]()La opción `-p` crea los directorios padres necesarios[18]().*

2.  **Asignar permisos:**
    * Asignar propietario:
        ```bash
        sudo chown -R $USER:$USER /var/www/[ejemplo.com/html](https://ejemplo.com/html)
        ```
        [19]()
    * Asegurar permisos (lectura/ejecución):
        ```bash
        sudo chmod -R 755 /var/www/ejemplo.com
        ```
        [19]()

3.  **Crear archivo de configuración del sitio:**
    * Se recomienda copiar el predeterminado para no modificar el original:
        ```bash
        sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/ejemplo.com.conf
        ```
        *[20]()Nota: Comando deducido del contexto de copiar contenido[20]().*

4.  **Editar directivas del Virtual Host:**
    * Archivo: `/etc/apache2/sites-available/ejemplo.com.conf`
    * **[21]()ServerName:** Dominio base (ej: `ejemplo.com`)[21]().
    * **[22]()ServerAlias:** Otros nombres (ej: `www.ejemplo.com`)[22]().
    * **[23]()DocumentRoot:** Ruta a los archivos web (ej: `/var/www/ejemplo.com/html`)[23]().

### Herramientas de activación
* **Habilitar un sitio:**
    ```bash
    sudo a2ensite ejemplo.com.conf
    ```
    *[24]()[25]()Crea el enlace simbólico en sites-enabled[24]()[25]().*

* **Deshabilitar un sitio:**
    ```bash
    sudo a2dissite 000-default.conf
    ```
    [26]()

* **Comprobar sintaxis:**
    ```bash
    sudo apache2ctl configtest
    ```
    *Debe devolver "Syntax OK"[26]().*

---

## 4. Autenticación y Seguridad

Configuración para restringir el acceso a directorios mediante usuario y contraseña.

### Creación de Usuarios y Contraseñas
Se utiliza el comando `htpasswd` para gestionar el archivo de contraseñas.
* **Crear archivo y primer usuario:**
    ```bash
    htpasswd -c /etc/apache2/password nombre_usuario
    ```
    *La opción `-c` crea el fichero nuevo. [27]()Se pedirá la contraseña interactivamente[27]().*

* **Añadir usuarios adicionales:**
    ```bash
    htpasswd /etc/apache2/password otro_usuario
    ```
    *[28]()NO usar `-c` si el archivo ya existe, o se sobrescribirá[28]().*

### Configuración en Apache (`.conf`)
[29]()Para proteger una carpeta, se añade un bloque `<Directory>` dentro del archivo de configuración del sitio (Virtual Host)[29]().

```apache
<Directory /var/www/dominio/html/carpeta_privada>
    AuthType basic
    AuthName "Mensaje para el usuario"
    AuthUserFile /etc/apache2/password
    Require valid-user
</Directory>
