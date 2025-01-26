
# Integración de MySQL con Graylog

Este proyecto tiene como objetivo enviar los logs de **MySQL** a **Graylog** desde diferentes contenedores o máquinas, proporcionando una solución centralizada de monitoreo y análisis de los logs de MySQL.

En este caso he utilizado una base de datos, podéis adaptar el ejemplo a vuestras necesidades usando otra diferente o creándola.
## Contenido del Repositorio

Este repositorio se encuentra dividido en dos directorios principales:

- **`graylog/`**: Contiene la configuración de **Graylog** para recibir los logs de MySQL.
- **`mysql/`**: Contiene la configuración necesaria para MySQL para que los logs sean enviados a **Graylog**.

## Requisitos Previos

Antes de comenzar, asegúrate de tener los siguientes requisitos instalados:

- **Docker** y **Docker Compose** en tu sistema.

## Instalación y Puesta en Marcha

### 1. Clonar el Repositorio

Clona este repositorio en tu máquina:

```bash
git clone https://github.com/jose404notfound/graylog-mysql.git
cd graylog-mysql
```

### 2. Configuración de Graylog

1. **Entrar al directorio de Graylog**:

   ```bash
   cd graylog
   ```

2. **Configurar Graylog para recibir logs**:

   En este directorio encontrarás un archivo de configuración de Docker Compose (`docker-compose.yml`). Este archivo configura los contenedores: **Graylog**, **MongoDB** y **Elasticsearch**, los cuales son necesarios para el funcionamiento de Graylog.

   Si es necesario, ajusta las variables de entorno (como puertos o credenciales) según tus necesidades.

3. **Ejecutar los contenedores de Graylog**:

   Con Docker Compose, ejecuta el siguiente comando:

   ```bash
   docker-compose up -d
   ```

4. **Acceder a Graylog**:

   Una vez que los contenedores estén levantados, accede a la interfaz web de Graylog. Por defecto, Graylog se ejecuta en el puerto 9000 de tu máquina. Abre tu navegador y visita:

   ```
   http://localhost:9000
   ```

   Las credenciales predeterminadas son:

   - Usuario: `admin`
   - Contraseña: `admin`

5. **Crear un "Input" para recibir logs de MySQL**:

   - En la interfaz de Graylog, ve a **System / Inputs**.
   - Selecciona el tipo de input que deseas usar. En este caso, se recomienda un **"GELF UDP"** o **"GELF TCP"**, dependiendo del protocolo de red que prefieras utilizar.
   - Configura el input con los parámetros que desees, como el puerto y la dirección IP.

   **Nota**: Asegúrate de que el puerto del input de Graylog esté accesible desde las máquinas o contenedores donde se ejecuta MySQL.

### 3. Configuración de MySQL

1. **Entrar al directorio de MySQL**:

   ```bash
   cd mysql
   ```

2. **Configurar MySQL para enviar los logs a Graylog**:

   Aquí encontrarás un archivo de configuración de **MySQL**. Necesitarás modificar el archivo de configuración de MySQL (`my.cnf`) para asegurarte de que los logs de MySQL se envíen correctamente a Graylog a través de un socket o puerto específico. Puedes mapear directamente el archivo o copiarlo una vez iniciado el contenedor.

   Algunas configuraciones clave en el archivo `my.cnf` son:

   - **Habilitar el log de consultas generales**.
   - **Habilitar el log de errores**.
   - **Habilitar el log de consultas lentas**.

   La configuración en el archivo `my.cnf` podría verse así:

   ```ini
   [mysqld]
   general_log = 1
   general_log_file = /var/log/mysql/mysql.log
   log_error = /var/log/mysql/error.log
   ```

3. **Instalar y configurar rsyslog**:

   Para enviar los logs desde MySQL a Graylog utilizando **rsyslog**, sigue estos pasos:

   - Instala **rsyslog** en la máquina o contenedor donde se ejecuta MySQL si no está instalado.

   - Configura **rsyslog** para que recoja los logs de MySQL y los envíe a Graylog. Para ello, edita el archivo de configuración de **rsyslog** (`/etc/rsyslog.conf` o crea un archivo nuevo en `/etc/rsyslog.d/`).

     **Nota**: Asegúrate de reemplazar `<Graylog_IP>` con la dirección IP del servidor Graylog.

4. **Reiniciar rsyslog**:

   Después de configurar rsyslog, reinicia el servicio para que los cambios surtan efecto:

   ```bash
   sudo systemctl restart rsyslog
   ```

5. **Verificar que los logs estén siendo enviados a Graylog**:

   Una vez que MySQL esté configurado y rsyslog esté en funcionamiento, los logs deberían aparecer en Graylog. Ve a la interfaz de Graylog y busca los logs de MySQL para asegurarte de que se están recibiendo correctamente.

## Conclusión

Con esta configuración, habrás centralizado los logs de MySQL en Graylog, lo que te permitirá analizarlos, realizar búsquedas avanzadas, crear alertas en base a los datos y monitorear el comportamiento de tus bases de datos de manera eficiente.

Si tienes alguna pregunta o problema, no dudes en abrir un **issue** en este repositorio.
