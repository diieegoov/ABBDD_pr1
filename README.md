# Práctica PostgreSQL

## 1. Creación de la base de datos

### 1.a

```sql
CREATE DATABASE biblioteca;
```

**Captura:**

<img width="472" height="359" alt="image" src="https://github.com/user-attachments/assets/af172b28-2577-497f-94d9-bd8374ba8430" />


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

<img width="584" height="379" alt="image" src="https://github.com/user-attachments/assets/2ee560f9-22aa-42e2-b2e0-e478c8eee5a9" />
<img width="666" height="396" alt="image" src="https://github.com/user-attachments/assets/e72092f7-0d7d-4b22-be2e-acfb3522ac30" />



---

## 2.b Creación del rol `lectores`

```sql
CREATE ROLE lectores;

GRANT USAGE ON SCHEMA public TO lectores;

GRANT SELECT ON ALL TABLES IN SCHEMA public
TO lectores;
```

**Captura:**

<img width="666" height="396" alt="image" src="https://github.com/user-attachments/assets/40020a5a-a311-48ca-bfcd-48f13731577d" />


---

## 2.c Asignación del rol

```sql
GRANT lectores TO usuario_biblio;
```

**Captura:**

<img width="666" height="396" alt="image" src="https://github.com/user-attachments/assets/7b1043ab-8b32-426c-b8f0-383813401f4d" />


---

## 2.d Consulta de roles

```sql
SELECT * FROM pg_roles;
```

**Captura:**

<img width="413" height="598" alt="image" src="https://github.com/user-attachments/assets/6bd40c1a-99e2-4641-b70f-b02394d6aedc" />


---

## 2.e Cambio de contraseña

```sql
ALTER USER usuario_biblio
WITH PASSWORD 'password';
```

**Captura:**

<img width="431" height="393" alt="image" src="https://github.com/user-attachments/assets/d88cd462-3b1f-447d-8122-9f2c11c9255a" />


---

## 2.f Revocar permisos de eliminación

```sql
REVOKE DELETE ON ALL TABLES IN SCHEMA public
FROM usuario_biblio;
```

**Captura:**

<img width="628" height="406" alt="image" src="https://github.com/user-attachments/assets/991ee420-ec62-45e9-bda4-68c78cb33edb" />


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

<img width="228" height="139" alt="image" src="https://github.com/user-attachments/assets/502c19f6-f793-4d9e-a125-a056633176f8" />


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

<img width="289" height="272" alt="image" src="https://github.com/user-attachments/assets/0c3bc8af-f252-4a2c-a8c1-6fca4662e2fe" />
<img width="300" height="216" alt="image" src="https://github.com/user-attachments/assets/68080540-d72f-4a2a-96eb-4629104bd79f" />
<img width="287" height="178" alt="image" src="https://github.com/user-attachments/assets/daf8126c-3ad4-45fe-8cdd-6376696ac24c" />



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

<img width="638" height="590" alt="image" src="https://github.com/user-attachments/assets/8e2a1099-ba7a-4646-8366-0f68b03979fd" />
<img width="656" height="615" alt="image" src="https://github.com/user-attachments/assets/81a82833-4c6d-49ba-8f62-84a2b7e4f1b4" />
<img width="764" height="527" alt="image" src="https://github.com/user-attachments/assets/66ad29d3-1bde-41f8-b223-2ae916c22670" />




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

<img width="565" height="634" alt="image" src="https://github.com/user-attachments/assets/dc399a41-4697-4406-9d6a-d4bd6ee9833c" />


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

<img width="540" height="483" alt="image" src="https://github.com/user-attachments/assets/c321ce6e-8674-478f-936a-29a60f793aac" />


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

<img width="520" height="629" alt="image" src="https://github.com/user-attachments/assets/c3cd949c-d66a-4228-aa5b-107106710737" />


---

# 6. Consultas con agregación

## 6.a Total de préstamos

```sql
SELECT DISTINCT COUNT(id_prestamo)
FROM prestamos;
```

**Captura:**

<img width="470" height="434" alt="image" src="https://github.com/user-attachments/assets/03ea677c-f158-40bf-b835-10c7a2103d43" />


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

<img width="481" height="508" alt="image" src="https://github.com/user-attachments/assets/b95345f8-d0dc-428e-8f1b-70b0fd2c6e59" />


---

# 7. Modificación de datos

## 7.a Actualizar la fecha de devolución

```sql
UPDATE prestamos
SET fecha_devolucion = '2026-10-01'
WHERE id_prestamo = 1;
```

**Captura:**

<img width="766" height="84" alt="image" src="https://github.com/user-attachments/assets/58354296-943c-4ff7-9a74-23678b4e1fa6" />


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

<img width="769" height="161" alt="image" src="https://github.com/user-attachments/assets/195ad73e-a9a2-4163-ab73-bacf53359f29" />

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

<img width="1023" height="433" alt="image" src="https://github.com/user-attachments/assets/aac01f64-cc28-4b27-ae4f-f2d96a5a9848" />

---

## 8.b Dar permiso de consulta a `usuario_biblio`

```sql
GRANT SELECT
ON vista_libros_prestados
TO usuario_biblio;
```

**Captura:**

<img width="952" height="433" alt="image" src="https://github.com/user-attachments/assets/e1bb2ea5-ef66-4cd2-a219-948ca415bc83" />


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

<img width="492" height="477" alt="image" src="https://github.com/user-attachments/assets/2e79a106-5334-4f1b-8b28-d5c6214aceb7" />


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

<img width="660" height="503" alt="image" src="https://github.com/user-attachments/assets/bf5ad6d4-7096-498c-92e5-76ef95b998c6" />


---
