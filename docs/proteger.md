[⬅️ Volver a Inicio](index.md#documentación-del-proyecto-mvc-tienda) <!-- Enlace de regreso -->

---

# 🔐 El Uso de los Ficheros .env

La protección de aplicaciones web es esencial para garantizar la seguridad de los datos y evitar vulnerabilidades que puedan ser explotadas por atacantes. En este proyecto, implementamos varias estrategias de protección, incluyendo el uso de ficheros `.env` para gestionar configuraciones sensibles y un archivo `.gitignore` para evitar exponer información privada en sistemas de control de versiones.

---

## 🌟 Importancia de la Protección en Aplicaciones Web

Las aplicaciones web manejan datos sensibles, desde credenciales de base de datos hasta información personal de los usuarios. Es crucial:
1. **Proteger Credenciales**: Evitar la exposición de contraseñas y configuraciones críticas en el código fuente.
2. **Evitar Vulnerabilidades**: Minimizar el riesgo de ataques como inyecciones SQL, ataques XSS o accesos no autorizados.
3. **Separar Entornos**: Diferenciar configuraciones para desarrollo y producción para prevenir errores o accesos indebidos.

---

## 📜 Uso de Ficheros `.env`

En este proyecto, los ficheros `.env` contienen información sensible, como las credenciales de la base de datos y la URL base de la aplicación. Aquí se utilizan dos variantes:
- **`.env.desarrollo`**: Configuración para el entorno de desarrollo.
- **`.env.produccion`**: Configuración para el entorno de producción.

??? example "Ejemplo de `.env.desarrollo`"
    ```plaintext
    # Configuración de la BD
    DB_HOST=localhost
    DB_NAME=tienda
    DB_USER=root
    DB_PASSWORD=

    # Configuración de la aplicación
    BASE_URL=http://examen-final-tienda.test
    ```

### 🛠️ Cómo Funciona en Este Proyecto

1. **Instalación de `phpdotenv` con Composer**

    La biblioteca `phpdotenv` se utiliza para cargar automáticamente las variables de entorno definidas en los archivos `.env`. Esta herramienta facilita la configuración y manejo de datos sensibles.

    **Comando de Instalación**

    Ejecuta el siguiente comando en la raíz del proyecto para instalar `phpdotenv`:

    ```bash
    composer require vlucas/phpdotenv
    ```

2. **Carga del `.env`**: En el archivo `index.php`, se utiliza la biblioteca `phpdotenv` para cargar las variables del archivo `.env` correspondiente al entorno actual:
    ```php
    use Dotenv\Dotenv;

    $envFile = getenv('APP_ENV') ?: 'desarrollo';
    $dotenv = Dotenv::createImmutable(__DIR__, ".env.$envFile");
    $dotenv->load();

    define('DB_HOST', $_ENV['DB_HOST']);
    define('DB_USER', $_ENV['DB_USER']);
    define('DB_PASS', $_ENV['DB_PASSWORD']);
    define('BASE_URL', $_ENV['BASE_URL']);
    ```

3. **Separación de Entornos**:
    - La variable `APP_ENV` determina si el entorno es de desarrollo o producción.
    - Se carga dinámicamente el archivo `.env` correspondiente, permitiendo que las configuraciones se mantengan separadas y seguras.

---

## 🛡️ Uso del Archivo `.gitignore`

El archivo `.gitignore` asegura que los ficheros sensibles, como `.env`, no se incluyan en el repositorio de control de versiones, evitando su exposición en plataformas como GitHub.

??? example "Ejemplo de `.gitignore`"
    ```plaintext
    # Ignorar archivos sensibles
    *.env*

    # Ignorar configuraciones específicas del sistema operativo
    .DS_Store
    Thumbs.db

    # Ignorar carpetas de logs y caché
    logs/
    cache/
    vendor/
    public/tmp/
    ```

En este proyecto, el archivo `.gitignore`:
1. **Protege Credenciales**: Ignora los archivos `.env`.
2. **Optimiza el Repositorio**: Excluye archivos temporales, logs y carpetas generadas automáticamente (como `vendor/`).
3. **Mantiene el Orden**: Ignora configuraciones específicas de editores de código (`.vscode/`, `.idea/`).

---

## 🔄 Ventajas del Enfoque `.env` Sobre `config.php`

### 📂 **Uso de Ficheros `.env`**
- **Flexibilidad**: Facilita la gestión de múltiples entornos (desarrollo, producción, pruebas).
- **Seguridad**: Evita que las credenciales se incluyan en el repositorio de control de versiones.
- **Simplicidad**: Centraliza las configuraciones sensibles en un solo lugar.

### 🛠️ **Limitaciones de `config.php`**
- **Exposición Potencial**: Si `config.php` no está bien protegido, podría ser accesible desde el servidor web.
- **Falta de Separación**: Mezclar configuraciones para diferentes entornos en un único archivo puede ser complicado.
- **Mantenimiento**: Requiere actualizaciones manuales para cada entorno.

En este proyecto, se prefirió el uso de `.env` por estas ventajas, junto con herramientas como `phpdotenv`, que hacen que la carga de configuraciones sea automática y segura.

---

## 📝 Consideraciones Adicionales

1. **Asegurarse de Configurar el Archivo `.gitignore` Correctamente**:
    - Verificar que los archivos `.env` y otros sensibles estén excluidos del repositorio.
2. **Proteger el Archivo `.env`**:
    - Ajustar los permisos del sistema operativo para evitar accesos no autorizados.
3. **Usar HTTPS en Producción**:
    - En producción, asegurarse de que todas las comunicaciones sean seguras mediante HTTPS.

---
