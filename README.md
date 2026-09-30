# Práctica PostgreSQL

## 1. Creación de la base de datos

### 1.a

```sql
CREATE DATABASE biblioteca;
```

**Captura:**

> 📷 *Añadir aquí una captura de la creación de la base de datos.*

---

# 2. Usuarios y roles

## 2.a Creación de usuarios

```sql
CREATE USER admin_biblio
WITH PASSWORD 'admin' CREATEDB CREATEROLE;

CREATE USER usuario_biblio
WITH PASSWORD 'admin';
```

Dentro de la base de datos `biblioteca`:

```sql
GRANT USAGE ON SCHEMA public TO usuario_biblio;

GRANT SELECT ON ALL TABLES IN SCHEMA public
TO usuario_biblio;
```

**Captura:**

> 📷 *Añadir aquí una captura.*

---

## 2.b Creación del rol `lectores`

```sql
CREATE ROLE lectores;

GRANT USAGE ON SCHEMA public TO lectores;

GRANT SELECT ON ALL TABLES IN SCHEMA public
TO lectores;
```

**Captura:**

> 📷 *Añadir aquí una captura.*

---

## 2.c Asignación del rol

```sql
GRANT lectores TO usuario_biblio;
```

**Captura:**

> 📷 *Añadir aquí una captura.*

---

## 2.d Consulta de roles

```sql
SELECT * FROM pg_roles;
```

**Captura:**

> 📷 *Añadir aquí una captura.*

---

## 2.e Cambio de contraseña

```sql
ALTER USER usuario_biblio
WITH PASSWORD 'password';
```

**Captura:**

> 📷 *Añadir aquí una captura.*

---

## 2.f Revocar permisos de eliminación

```sql
REVOKE DELETE ON ALL TABLES IN SCHEMA public
FROM usuario_biblio;
```

**Captura:**

> 📷 *Añadir aquí una captura.*

---

# 3. Creación de tablas

## 3.a Creación de las tablas

### Tabla `autores`

```sql
CREATE TABLE autores (
    id_autor SERIAL PRIMARY KEY,
    nombre VARCHAR(100),
    nacionalidad VARCHAR(50)
);
```

### Tabla `libros`

```sql
CREATE TABLE libros (
    id_libro SERIAL PRIMARY KEY,
    titulo VARCHAR(200),
    año_publicacion INT,
    id_autor INT
);
```

### Tabla `prestamos`

```sql
CREATE TABLE prestamos (
    id_prestamo SERIAL PRIMARY KEY,
    id_libro INT,
    fecha_prestamo DATE,
    fecha_devolucion DATE,
    usuario_prestatario VARCHAR(100)
);
```

**Captura:**

> 📷 *Añadir aquí una captura donde se vean las tres tablas creadas.*

---

## 3.b Claves foráneas

### Relación entre `libros` y `autores`

```sql
ALTER TABLE libros
ADD CONSTRAINT fk_libros_autores
FOREIGN KEY (id_autor)
REFERENCES autores(id_autor);
```

### Relación entre `prestamos` y `libros`

```sql
ALTER TABLE prestamos
ADD CONSTRAINT fk_prestamos_libro
FOREIGN KEY (id_libro)
REFERENCES libros(id_libro);
```

**Captura:**

> 📷 *Añadir aquí una captura de las claves foráneas.*

---

# 4. Inserción de datos

## 4.a Inserción de autores

```sql
INSERT INTO autores (nombre, nacionalidad)
VALUES
    ('Gabriel García Márquez', 'Colombiana'),
    ('Jorge Luis Borges', 'Argentina'),
    ('George Orwell', 'Británica'),
    ('Jane Austen', 'Británica'),
    ('Miguel de Cervantes', 'Española');
```

### Inserción de libros

```sql
INSERT INTO libros (titulo, año_publicacion, id_autor)
VALUES
    ('Cien años de soledad', 1967, 1),
    ('El amor en los tiempos del cólera', 1985, 1),
    ('Ficciones', 1944, 2),
    ('El Aleph', 1949, 2),
    ('1984', 1949, 3),
    ('Rebelión en la granja', 1945, 3),
    ('Orgullo y prejuicio', 1813, 4),
    ('Don Quijote de la Mancha', 1605, 5);
```

### Inserción de préstamos

```sql
INSERT INTO prestamos
    (id_libro, fecha_prestamo, fecha_devolucion, usuario_prestatario)
VALUES
    (1, '2026-09-01', '2026-09-10', 'Ana'),
    (2, '2026-09-05', NULL, 'Carlos'),
    (3, '2026-09-07', '2026-09-15', 'Ana'),
    (1, '2026-09-10', NULL, 'Luis'),
    (5, '2026-09-12', NULL, 'Carlos');
```

**Captura:**

> 📷 *Añadir aquí una captura de los datos insertados.*

---

# 5. Consultas básicas

## 5.a Listar todos los libros con su autor

```sql
SELECT
    l.titulo,
    a.nombre AS autor
FROM libros l
LEFT JOIN autores a
    ON a.id_autor = l.id_autor;
```

**Captura:**

> 📷 *Añadir aquí una captura del resultado.*

---

## 5.b Mostrar préstamos sin fecha de devolución

```sql
SELECT
    id_prestamo,
    fecha_prestamo,
    usuario_prestatario
FROM prestamos
WHERE fecha_devolucion IS NULL;
```

**Captura:**

> 📷 *Añadir aquí una captura del resultado.*

---

## 5.c Autores con libros registrados

```sql
SELECT
    a.nombre AS autores,
    COUNT(id_libro) AS libros_registrados
FROM autores a
LEFT JOIN libros l
    ON l.id_autor = a.id_autor
GROUP BY a.nombre, id_libro
HAVING COUNT(id_libro) > 0;
```

**Captura:**

> 📷 *Añadir aquí una captura del resultado.*

---

# 6. Consultas con agregación

## 6.a Total de préstamos

```sql
SELECT DISTINCT COUNT(id_prestamo)
FROM prestamos;
```

**Captura:**

> 📷 *Añadir aquí una captura del resultado.*

---

## 6.b Libros prestados por usuario

```sql
SELECT
    p.usuario_prestatario,
    COUNT(DISTINCT l.id_libro)
FROM prestamos p
LEFT JOIN libros l
    ON l.id_libro = p.id_libro
GROUP BY p.usuario_prestatario;
```

**Captura:**

> 📷 *Añadir aquí una captura del resultado.*

---

# 7. Modificación de datos

## 7.a Actualizar la fecha de devolución

```sql
UPDATE prestamos
SET fecha_devolucion = '2026-10-01'
WHERE id_prestamo = 1;
```

**Captura:**

> 📷 *Añadir aquí una captura del resultado.*

---

## 7.b Eliminar un libro y comprobar el efecto en los préstamos

Primero se elimina la clave foránea anterior:

```sql
ALTER TABLE prestamos
DROP CONSTRAINT fk_prestamos_libros;
```

Después se vuelve a crear utilizando `ON DELETE CASCADE`:

```sql
ALTER TABLE prestamos
ADD CONSTRAINT fk_prestamos_libros
FOREIGN KEY (id_libro)
REFERENCES libros(id_libro)
ON DELETE CASCADE;
```

Finalmente, se elimina el libro:

```sql
DELETE FROM libros
WHERE id_libro = 1;
```

Al utilizar `ON DELETE CASCADE`, los préstamos asociados al libro eliminado también se eliminan automáticamente.

**Captura:**

> 📷 *Añadir aquí una captura antes y/o después de eliminar el libro.*

---

# 8. Creación de vistas

## 8.a Crear la vista `vista_libros_prestados`

```sql
CREATE VIEW vista_libros_prestados AS
SELECT
    libros.titulo,
    autores.nombre AS autor,
    prestamos.usuario_prestatario AS prestatario
FROM prestamos
INNER JOIN libros
    ON prestamos.id_libro = libros.id_libro
INNER JOIN autores
    ON libros.id_autor = autores.id_autor;
```

**Captura:**

> 📷 *Añadir aquí una captura de la vista creada.*

---

## 8.b Dar permiso de consulta a `usuario_biblio`

```sql
GRANT SELECT
ON vista_libros_prestados
TO usuario_biblio;
```

**Captura:**

> 📷 *Añadir aquí una captura.*

---

# 9. Funciones y consultas avanzadas

## 9.a Función para obtener los libros de un autor

```sql
CREATE OR REPLACE FUNCTION libros_de_autor(nombre_autor VARCHAR)
RETURNS TABLE (
    titulo VARCHAR,
    año_publicacion INT
)
AS $$
BEGIN
    RETURN QUERY
    SELECT
        libros.titulo,
        libros.año_publicacion
    FROM libros
    INNER JOIN autores
        ON libros.id_autor = autores.id_autor
    WHERE autores.nombre = nombre_autor;
END;
$$ LANGUAGE plpgsql;
```

Para utilizar la función:

```sql
SELECT *
FROM libros_de_autor('George Orwell');
```

**Captura:**

> 📷 *Añadir aquí una captura del resultado de la función.*

---

## 9.b Tres libros más prestados

```sql
SELECT
    libros.titulo,
    COUNT(prestamos.id_prestamo) AS cantidad_prestamos
FROM libros
INNER JOIN prestamos
    ON libros.id_libro = prestamos.id_libro
GROUP BY libros.id_libro, libros.titulo
ORDER BY cantidad_prestamos DESC
LIMIT 3;
```

**Captura:**

> 📷 *Añadir aquí una captura del resultado.*

---
