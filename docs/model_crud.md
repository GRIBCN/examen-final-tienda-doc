[⬅️ Volver a Inicio](index.md#documentación-del-proyecto-mvc-tienda) <!-- Enlace de regreso -->

---

# 🗂️ Componentes Específicos del Modelo MVC. La Clase `Model_crud`

Este apartado describe cómo la clase `Model_crud` implementa operaciones generales de base de datos en el proyecto **MVC Tienda**. Esta clase se utiliza como base para crear modelos específicos que interactúan con vistas, controladores y la base de datos.

---

## 📌 Función de la Clase `Model_crud`

??? info "¿Qué es y cómo funciona?"
    === "Descripción"
    La clase `Model_crud` es una clase abstracta que proporciona una interfaz generalizada para las operaciones CRUD (Create, Read, Update, Delete). Utiliza **MySQLi** para realizar consultas preparadas de manera segura, minimizando riesgos de inyección SQL.

    === "Responsabilidades principales"
    - **Conexión a la base de datos**: Maneja la apertura y cierre de la conexión.
    - **Consultas preparadas**: Ejecuta consultas de forma segura utilizando métodos como `execute_prepared_query` y `execute_prepared_select`.
    - **Métodos CRUD generalizados**: Incluye métodos como `insert`, `update`, `delete` y `get` para manejar datos.

---

## 🌟 Estructura de la Clase `Model_crud`

??? info "Código principal"
    === "Código"
    ```php title="Model_crud.php"
    abstract class Model_crud {
        private static $db_host = DB_HOST;
        private static $db_user = DB_USER;
        private static $db_pass = DB_PASS;
        private static $db_name = DB_NAME;

        protected $table = '';    // Nombre de la tabla
        protected $column_id = ''; // Nombre de la columna clave

        public function insert($data) { /* Inserción genérica */ }
        public function update($id, $data) { /* Actualización genérica */ }
        public function delete($id) { /* Eliminación genérica */ }
        public function get($id = null) { /* Recuperación genérica */ }
    }
    ```

    === "Explicación"
    Esta clase define métodos genéricos para realizar operaciones en cualquier tabla. Las subclases especifican el nombre de la tabla (`$table`) y su clave primaria (`$column_id`).

---

## 🔧 Subclases de `Model_crud`

Las subclases especializan el comportamiento de `Model_crud` para una tabla o vista específica. En este proyecto, la clase `TiendaController` hereda de `Model_crud` y opera sobre la vista `productos_fabricante`.

??? info "Subclase: `TiendaController`"
    === "Código"
    ```php title="TiendaController.php"
    class TiendaController extends Model_crud {
        protected $table = 'productos_fabricante';
        protected $column_id = 'rowid';

        public function getValueFields($field) { /* Valores únicos */ }
        public function filterTienda($query, $valor) { /* Filtro por texto */ }
        public function filterTiendaPrecio($query, $operador, $precio) { /* Filtro por precio */ }
    }
    ```

    === "Explicación"
    - **`getValueFields($field)`**: Obtiene valores únicos de una columna específica usando procedimientos almacenados.
    - **`filterTienda($query, $valor)`**: Filtra los resultados de la vista `productos_fabricante` con base en una consulta y un valor.
    - **`filterTiendaPrecio($query, $operador, $precio)`**: Aplica un filtro basado en el precio con operadores de comparación (`>`, `<`, `=`).

---

## 📂 Estructura de la Vista `productos_fabricante`

??? info "Vista de base de datos"
    === "Código SQL"
    ```sql title="productos_fabricante"
    CREATE VIEW productos_fabricante AS
    SELECT 
      ROW_NUMBER() OVER (ORDER BY a.nombre, b.nombre) AS rowid,
      a.pk_codigo AS pk_fabricante, 
      a.nombre AS nombre_fabricante, 
      b.id_producto AS pk_producto, 
      b.nombre AS nombre_producto, 
      b.precio AS precio
    FROM fabricante AS a
    JOIN producto AS b ON b.fk_codigo = a.pk_codigo
    ORDER BY nombre_fabricante, nombre_producto;
    ```

    === "Explicación"
    - **Columna `rowid`**: Identificador incremental único generado para cada fila.
    - **Relación `fabricante` y `producto`**: Une ambas tablas para mostrar los datos combinados.
    - **Ordenamiento**: Los resultados están organizados por nombre del fabricante y producto.

    === "Ejemplo de datos"
    ![Vista de la tabla `productos_fabricante`](assets/DB_view.png)

---

## 🌟 Ejemplo Práctico

??? example "Operación CRUD: Filtrar productos por precio"
    === "Código"
    ```php title="Ejemplo de filtrado por precio"
    $tiendaController = new TiendaController();
    $productos = $tiendaController->filterTiendaPrecio(
        "SELECT * FROM productos_fabricante WHERE precio", ">=", 200
    );

    print_r($productos);
    ```

    === "Salida esperada"
    ```plaintext
    Array
    (
        [0] => Array
            (
                [rowid] => 1
                [pk_fabricante] => 1
                [nombre_fabricante] => Asus
                [pk_producto] => 6
                [nombre_producto] => Monitor 24 LED Full HD
                [precio] => 202
            )
        [1] => Array
            (
                [rowid] => 2
                [pk_fabricante] => 1
                [nombre_fabricante] => Asus
                [pk_producto] => 7
                [nombre_producto] => Monitor 27 LED Full HD
                [precio] => 245.99
            )
    )
    ```

    === "Explicación"
    - La consulta filtra productos con un precio mayor o igual a 200.
    - Devuelve un arreglo asociativo con los productos que cumplen la condición.

---

## 🧩 Ventajas de la Implementación

1. **Reutilización**: La clase `Model_crud` permite manejar cualquier tabla de manera genérica.
2. **Seguridad**: Utiliza consultas preparadas para prevenir inyecciones SQL.
3. **Escalabilidad**: Es sencillo añadir nuevos métodos a las subclases para funcionalidades específicas.
4. **Modularidad**: Se separan las responsabilidades del modelo, controlador y vista, manteniendo la estructura MVC.

---
