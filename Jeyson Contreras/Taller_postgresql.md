# 📘 Resumen consultado sobre SQL e Índices en PostgreSQL

# Integrantes: Nixon Alejo, David Alejo, Gabriel Leal.

## 1. Conceptos / Teoría

### Relaciones en PostgreSQL
- **Tabla**: Representa una **relación** dentro del modelo relacional. Contiene filas (tuplas) y columnas (atributos).
- **Atributos**: Son las **columnas** de una tabla. Cada atributo tiene un **dominio** (tipo de dato permitido, como `INTEGER`, `TEXT`, `DATE`, etc.).
- **Dominios**: Definen el conjunto de valores válidos que un atributo puede tener. Ejemplo: un atributo `edad` de dominio `INTEGER` solo acepta números enteros.
- **Tuplas**: Son las **filas** de la tabla; cada tupla representa un registro único con valores para cada atributo.

Ejemplo:
```sql
CREATE TABLE estudiantes (
    id SERIAL,
    nombre TEXT,
    edad INTEGER,
    PRIMARY KEY (id)
);
```

---

## 2. Claves primarias y foráneas

### Clave primaria (PRIMARY KEY)
- **Definición**: Es un atributo o conjunto de atributos que identifica de forma única a cada tupla en una tabla.
- **Características**:
  - No permite valores `NULL`.
  - No se repiten valores.
- **Sintaxis**:
```sql
CREATE TABLE estudiantes (
    id SERIAL PRIMARY KEY,
    nombre TEXT NOT NULL
);
```

### Clave foránea (FOREIGN KEY)
- **Definición**: Es un atributo en una tabla que referencia la clave primaria de otra tabla.
- **Sirve para**: Mantener **integridad referencial**.
- **Sintaxis**:
```sql
CREATE TABLE matriculas (
    id SERIAL PRIMARY KEY,
    estudiante_id INT,
    curso_id INT,
    FOREIGN KEY (estudiante_id) REFERENCES estudiantes(id)
);
```

### Buenas prácticas
- Definir claves primarias en todas las tablas.
- Usar claves foráneas para garantizar integridad de datos.
- Evitar usar atributos sin dominio definido o que acepten nulos si no es necesario.

---

## 3. Índices en PostgreSQL

### Tipos de índices
- **B-Tree (por defecto)**:
  - Soporta comparaciones de rango (`<, <=, =, >=, >`) y búsquedas rápidas.
  - Ideal para consultas `ORDER BY`, `BETWEEN`, `IN`, `LIKE 'abc%'`.
  - **Ventajas**: rápido, versátil, es el tipo por defecto.
  - **Desventajas**: ocupa espacio adicional y puede ralentizar inserciones/actualizaciones.

- **Hash**:
  - Optimizado para búsquedas de igualdad (`=`).
  - **Ventajas**: consultas de igualdad muy rápidas.
  - **Desventajas**: limitado, no sirve para búsquedas de rango ni para ordenamiento.

### Sintaxis de creación de índice
```sql
CREATE INDEX idx_estudiante_nombre
ON estudiantes (nombre);

CREATE INDEX idx_matricula_curso
ON matriculas (curso_id);
```

### Otros índices avanzados
- **GiST**: para búsquedas complejas (geometría, texto).
- **GIN**: útil para búsquedas de texto completo y arrays.
- **BRIN**: eficiente en tablas grandes con datos ordenados naturalmente (ejemplo: fechas).
- **SP-GiST**: para distribuciones de datos irregulares.

---

## 4. Integridad referencial

PostgreSQL asegura integridad entre tablas mediante restricciones `FOREIGN KEY`.  
Al definir la relación, se pueden controlar qué ocurre al **eliminar** o **actualizar** un registro referenciado.

### Opciones de `ON DELETE` y `ON UPDATE`
- **CASCADE**: El cambio se propaga automáticamente a las tablas relacionadas.
- **SET NULL**: Los valores de la clave foránea se establecen en `NULL`.
- **SET DEFAULT**: Los valores se reemplazan por el valor por defecto de la columna.
- **RESTRICT** / **NO ACTION**: Impiden la acción si existen referencias.

Ejemplo:
```sql
CREATE TABLE cursos (
    id SERIAL PRIMARY KEY,
    nombre TEXT
);

CREATE TABLE matriculas (
    id SERIAL PRIMARY KEY,
    estudiante_id INT REFERENCES estudiantes(id) ON DELETE CASCADE,
    curso_id INT REFERENCES cursos(id) ON DELETE SET NULL
);
```

---

## 5. Lectura recomendada

- **Modelo Relacional en PostgreSQL**: PostgreSQL implementa el modelo relacional mediante tablas, claves y restricciones.
- **Documentación oficial**:
  - [Claves primarias y foráneas](https://www.postgresql.org/docs/current/tutorial-fk.html)
  - [Índices](https://www.postgresql.org/docs/current/indexes.html)

---

## ✅ Conclusión
PostgreSQL es fiel al **modelo relacional**, asegurando que:
- Las tablas estén correctamente estructuradas (relaciones, atributos, dominios y tuplas).
- Se mantenga la **unicidad** con claves primarias.
- Se garantice la **integridad referencial** con claves foráneas y restricciones.
- Se optimicen las consultas mediante **índices** (B-Tree y Hash principalmente).
