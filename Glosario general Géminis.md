# Glosario y Guía de Comandos: Apache en Ubuntu Server

Este documento recopila los comandos, rutas y configuraciones esenciales para la administración de un servidor web Apache, basado en las prácticas de la asignatura de Servicios en Red.

## 1. Instalación y Gestión del Servicio

Antes de configurar, es necesario instalar y saber controlar el estado del servidor.

### Instalación
* **Actualizar repositorios:**
    ```bash
    sudo apt update
    ```
    *[span_0](start_span)Garantiza que se reflejen las versiones más recientes de los paquetes[span_0](end_span).*

* **Instalar Apache:**
    ```bash
    sudo apt install apache2
    ```
    *[span_1](start_span)Instala el servidor y todas sus dependencias requeridas[span_1](end_span).*

### Gestión del proceso (Systemctl)
* **Verificar estado:**
    ```bash
    sudo systemctl status apache2
    ```
    *[span_2](start_span)Comprueba si el servicio está activo y en ejecución[span_2](end_span).*

* **Iniciar servicio:**
    ```bash
    sudo systemctl start apache2
    ```
    [span_3](start_span)

* **Detener servicio:**
    ```bash
    sudo systemctl stop apache2
    ```
    [span_3](end_span)

* **Reiniciar (Stop + Start):**
    ```bash
    sudo systemctl restart apache2
    ```
    *[span_4](start_span)Se usa para aplicar cambios mayores[span_4](end_span).*

* **Recargar (Reload):**
    ```bash
    sudo systemctl reload apache2
    ```
    *[span_5](start_span)Aplica cambios de configuración sin perder las conexiones activas[span_5](end_span).*

* **Habilitar al arranque:**
    ```bash
    sudo systemctl enable apache2
    ```
    *[span_6](start_span)Configura Apache para iniciarse automáticamente al encender el servidor[span_6](end_span).*

* **Deshabilitar al arranque:**
    ```bash
    sudo systemctl disable apache2
    ```
    [span_7](start_span)

---

## 2. Directorios y Archivos Importantes

Es vital conocer la estructura de archivos de Apache para la configuración.

### Archivos de Configuración (`/etc/apache2`)
* **`/etc/apache2/apache2.conf`**: Archivo principal de configuración global[span_7](end_span).
* **[span_8](start_span)`/etc/apache2/ports.conf`**: Especifica los puertos de escucha (80 por defecto, 443 para SSL)[span_8](end_span).
* **[span_9](start_span)`/etc/apache2/sites-available/`**: Directorio donde se crean los archivos de configuración de los sitios virtuales (Virtual Hosts)[span_9](end_span).
* **[span_10](start_span)`/etc/apache2/sites-enabled/`**: Directorio con enlaces simbólicos a los sitios que están realmente activos[span_10](end_span).

### Contenido Web y Logs
* **[span_11](start_span)`/var/www/html`**: Directorio raíz por defecto donde se aloja el contenido web[span_11](end_span).
* **[span_12](start_span)`/var/log/apache2/access.log`**: Registro de todas las solicitudes hechas al servidor[span_12](end_span).
* **[span_13](start_span)`/var/log/apache2/error.log`**: Registro de errores del servidor[span_13](end_span).

### Resolución de Nombres Local
Para probar dominios sin un servidor DNS real, se modifica el archivo `hosts`:
* **[span_14](start_span)Linux:** `/etc/hosts`[span_14](end_span).
* **[span_15](start_span)Windows:** `C:\Windows\System32\drivers\etc\hosts`[span_15](end_span).
* **[span_16](start_span)Formato:** `IP nombre_dominio` (Ej: `192.168.3.1 www.ejemplo.com`)[span_16](end_span).

---

## 3. Virtual Hosts (Sitios Virtuales)

[span_17](start_span)Permite alojar varios dominios en un solo servidor[span_17](end_span).

### Pasos de creación
1.  **Crear estructura de directorios:**
    ```bash
    sudo mkdir -p /var/www/[ejemplo.com/html](https://ejemplo.com/html)
    ```
    *[span_18](start_span)La opción `-p` crea los directorios padres necesarios[span_18](end_span).*

2.  **Asignar permisos:**
    * Asignar propietario:
        ```bash
        sudo chown -R $USER:$USER /var/www/[ejemplo.com/html](https://ejemplo.com/html)
        ```
        [span_19](start_span)
    * Asegurar permisos (lectura/ejecución):
        ```bash
        sudo chmod -R 755 /var/www/ejemplo.com
        ```
        [span_19](end_span)

3.  **Crear archivo de configuración del sitio:**
    * Se recomienda copiar el predeterminado para no modificar el original:
        ```bash
        sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/ejemplo.com.conf
        ```
        *[span_20](start_span)Nota: Comando deducido del contexto de copiar contenido[span_20](end_span).*

4.  **Editar directivas del Virtual Host:**
    * Archivo: `/etc/apache2/sites-available/ejemplo.com.conf`
    * **[span_21](start_span)ServerName:** Dominio base (ej: `ejemplo.com`)[span_21](end_span).
    * **[span_22](start_span)ServerAlias:** Otros nombres (ej: `www.ejemplo.com`)[span_22](end_span).
    * **[span_23](start_span)DocumentRoot:** Ruta a los archivos web (ej: `/var/www/ejemplo.com/html`)[span_23](end_span).

### Herramientas de activación
* **Habilitar un sitio:**
    ```bash
    sudo a2ensite ejemplo.com.conf
    ```
    *[span_24](start_span)[span_25](start_span)Crea el enlace simbólico en sites-enabled[span_24](end_span)[span_25](end_span).*

* **Deshabilitar un sitio:**
    ```bash
    sudo a2dissite 000-default.conf
    ```
    [span_26](start_span)

* **Comprobar sintaxis:**
    ```bash
    sudo apache2ctl configtest
    ```
    *Debe devolver "Syntax OK"[span_26](end_span).*

---

## 4. Autenticación y Seguridad

Configuración para restringir el acceso a directorios mediante usuario y contraseña.

### Creación de Usuarios y Contraseñas
Se utiliza el comando `htpasswd` para gestionar el archivo de contraseñas.
* **Crear archivo y primer usuario:**
    ```bash
    htpasswd -c /etc/apache2/password nombre_usuario
    ```
    *La opción `-c` crea el fichero nuevo. [span_27](start_span)Se pedirá la contraseña interactivamente[span_27](end_span).*

* **Añadir usuarios adicionales:**
    ```bash
    htpasswd /etc/apache2/password otro_usuario
    ```
    *[span_28](start_span)NO usar `-c` si el archivo ya existe, o se sobrescribirá[span_28](end_span).*

### Configuración en Apache (`.conf`)
[span_29](start_span)Para proteger una carpeta, se añade un bloque `<Directory>` dentro del archivo de configuración del sitio (Virtual Host)[span_29](end_span).

```apache
<Directory /var/www/dominio/html/carpeta_privada>
    AuthType basic
    AuthName "Mensaje para el usuario"
    AuthUserFile /etc/apache2/password
    Require valid-user
</Directory>
