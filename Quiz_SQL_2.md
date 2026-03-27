# Quiz SQL 2 - Bienes TIC para Enseñanza
**Dataset:** DANE / MEN (2022 y 2023)

---

## Punto 1: Exploración de tic_2023

### Consulta A — Todos los registros de tic_2023
```sql
SELECT * FROM tic_2023;
```

### Consulta B — Valores distintos de ACTIVIDAD_NOMBRE
```sql
SELECT DISTINCT actividad_nombre FROM tic_2023;
```

---

## Punto 2: Reporte de reconocimiento de tic_2022

```sql
SELECT 
    sede_codigo codigo_sede,
    periodo_anio anio_reporte,
    actividad_codigo cod_actividad,
    actividad_nombre nombre_actividad
FROM tic_2022
WHERE periodo_anio IS NOT NULL
ORDER BY sede_codigo ASC
LIMIT 50;
```

---

## Punto 3: Creación de tabla tic_sedes_resumen

```sql
CREATE TABLE tic_sedes_resumen (
    resumen_id INT PRIMARY KEY,
    sede_codigo INT NOT NULL,
    anio INT NOT NULL,
    Total_actividades INT NOT NULL,
    Tiene_internet BOOLEAN,
    Fecha_carga DATETIME,
    UNIQUE (sede_codigo, anio)
);
```

---

## Punto 4: Conteo de sedes con actividad '05' por departamento

### Consulta A — Completar registros vacíos con actividad 05
```sql
UPDATE tic_2023
SET 
    actividad_codigo = '05',
    actividad_nombre = 'Consulta de contenidos pedagógicos, mediante buscadores en internet'
WHERE actividad_codigo IS NULL;
```

```sql
UPDATE tic_2022
SET 
    actividad_codigo = '05',
    actividad_nombre = 'Consulta de contenidos pedagógicos, mediante buscadores en internet'
WHERE actividad_codigo IS NULL;
```

### Consulta B — Conteo de sedes únicas por departamento
```sql
SELECT 
    SUBSTR(sede_codigo, 1, 2) departamento,
    COUNT(DISTINCT sede_codigo) total_sedes
FROM tic_2023
WHERE actividad_codigo = '05'
GROUP BY SUBSTR(sede_codigo, 1, 2)
HAVING COUNT(DISTINCT sede_codigo) > 500
ORDER BY total_sedes DESC;
```

---

## Punto 5: Comparación entre 2022 y 2023

```sql
SELECT 
    t22.sede_codigo codigo_sede,
    t22.total_act_2022,
    t23.total_act_2023,
    (t23.total_act_2023 - t22.total_act_2022) diferencia,
    CASE 
        WHEN (t23.total_act_2023 - t22.total_act_2022) > 0 THEN 'CRECIÓ'
        WHEN (t23.total_act_2023 - t22.total_act_2022) < 0 THEN 'DECRECIÓ'
        ELSE 'SIN CAMBIO' 
    END tendencia
FROM 
    (SELECT sede_codigo, COUNT(*) total_act_2022 
     FROM tic_2022 
     GROUP BY sede_codigo) t22
INNER JOIN 
    (SELECT sede_codigo, COUNT(*) total_act_2023 
     FROM tic_2023 
     GROUP BY sede_codigo) t23
ON t22.sede_codigo = t23.sede_codigo
WHERE t22.total_act_2022 >= 2
ORDER BY diferencia DESC
LIMIT 30;
```
