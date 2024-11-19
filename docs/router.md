[⬅️ Volver a Inicio](index.md#documentación-del-proyecto-mvc-tienda) <!-- Enlace de regreso -->

---

# 🔀 Interacción entre Componentes. Implementación y el uso del Router

En este apartado se detalla cómo interactúan los componentes del proyecto **MVC Tienda**, centrándonos en el flujo de información hacia el `Router` y su implementación. El `Router` es el núcleo encargado de interpretar las solicitudes y dirigirlas hacia las vistas correspondientes.

---

## 📌 La Clase Router: El Corazón de la Aplicación

??? info "¿Qué es el Router y cuál es su función?"
    === "Descripción"
    El `Router` actúa como el cerebro de la aplicación. Su objetivo es recibir solicitudes de las variables `$_GET` y `$_POST`, procesarlas y cargar la vista adecuada. Además, aplica filtrado a los datos para prevenir ataques como la inyección de código.

    === "Diagrama de Flujo"
    ```plaintext
    Solicitud (GET o POST) --> Router --> ViewController --> Vista correspondiente
    ```

---

## 🌟 Flujo de Información hacia el Router

### 🛠️ Variables `$_GET` del Menú

??? info "Variables `$_GET`"
    === "Explicación"
    Las variables `$_GET` se utilizan para determinar la ruta solicitada desde el menú de navegación. Por ejemplo, cuando un usuario selecciona "Inicio", se envía la variable `r=home` al `Router`.

    === "Ejemplo"
    ```php title="Enlace del menú"
    <a href="index.php?r=home">Inicio</a>
    ```

    === "Proceso en el Router"
    ```php title="Router.php"
    $this->route = isset($_GET['r']) ? $_GET['r'] : 'home';
    ```

---

### 🛠️ Variables `$_POST` de las Vistas

??? info "Variables `$_POST`"
    === "Explicación"
    Las variables `$_POST` se envían desde formularios o interacciones AJAX. Estas variables son procesadas por el `Router` para ejecutar acciones específicas, como filtrar tablas o procesar formularios.

    === "Ejemplo de formulario"
    ```html title="Formulario HTML"
    <form method="POST" action="index.php">
        <input type="hidden" name="r" value="tienda-filter">
        <input type="text" name="consulta" placeholder="Buscar">
        <button type="submit">Filtrar</button>
    </form>
    ```

    === "Proceso en el Router"
    ```php title="Router.php"
    $this->route = isset($_POST['r']) ? $_POST['r'] : 'home';
    ```

---

## 🔧 Implementación del Router

??? info "Código del Router"
    === "Código"
    ```php title="Router.php"
    class Router {
        public $route;

        public function __construct() {
            $filtro = new FiltrarDatos();
            $datos_get = $filtro->Filtrar($_GET);
            $datos_post = $filtro->Filtrar($_POST);

            $this->route = isset($datos_get['r']) 
                        ? $datos_get['r'] 
                        : (isset($datos_post['r']) ? $datos_post['r'] : 'home');

            $controller = new ViewController();

            switch ($this->route) {
                case 'home':
                    $controller->load_view('home');
                    break;
                case 'tienda':
                    // Filtrar o procesar datos de la vista tienda
                    $this->filtrarTabla($datos_post);
                    break;
                default:
                    $controller->load_view('404');
                    break;
            }
        }
    }
    ```

---

## 🧩 Interacción entre Componentes

1. **Menú de Navegación (`$_GET`)**: Los enlaces del menú generan solicitudes que son capturadas por el `Router`.
    - Ejemplo: `index.php?r=home` carga la vista principal.
2. **Vistas y Formularios (`$_POST`)**: Las interacciones del usuario en las vistas envían datos que el `Router` filtra y procesa.
    - Ejemplo: Un formulario de búsqueda envía datos al controlador correspondiente.
3. **ViewController**: El `Router` delega la tarea de cargar las vistas al `ViewController`.
    - Ejemplo: La ruta `home` carga `views/home.php`.

---

## 🌐 Consideraciones de Seguridad

??? question "¿Cómo proteger el flujo de datos?"
    === "Recomendaciones"
    - **Filtrar datos**: Utiliza clases como `FiltrarDatos` para validar y limpiar las entradas.
    - **Evitar acceso directo**: Asegúrate de que solo `index.php` sea accesible directamente.
    - **Validar rutas**: Implementa casos predeterminados para rutas no reconocidas, como `404`.

---

## 🌟 Beneficios de este Enfoque

1. **Centralización**: Todas las rutas y vistas están controladas desde una sola clase.
2. **Seguridad**: Los datos son filtrados antes de ser procesados.
3. **Flexibilidad**: Es fácil agregar nuevas rutas o vistas sin modificar el núcleo de la aplicación.

---
