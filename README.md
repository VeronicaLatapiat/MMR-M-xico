# MMR-Mexico

# README - Procesamiento de Mortalidad Materna

Este script procesa datos de mortalidad materna entre los años 2002 y 2022. Su salida es un archivo donde la primera columna corresponde al estado, la segunda al código CIE o ICD-10, y las columnas restantes representan los conteos anuales.

## Requisitos

1. **Python**
2. **Biblioteca Pandas**:

## Archivos requeridos

- Un archivo de entrada llamado `mortalidad_materna_2002_2022_resumen.csv` con las siguientes columnas:
  - `ANIO_DEFUNCION`: Año de la defunción.
  - `ENTIDAD_RESIDENCIAD`: Entidad de residencia.
  - `CAUSA_CIE_4D`: Código CIE o ICD-10 asociado a la causa de defunción.

## Instrucciones de uso

### Paso 1: Leer el archivo de entrada
El script carga los datos de `mortalidad_materna_2002_2022_resumen.csv` usando Pandas.

```python
df = pd.read_csv('mortalidad_materna_2002_2022_resumen.csv', sep=';')
```

### Paso 2: Validar la columna `ANIO_DEFUNCION`
Convierte la columna `ANIO_DEFUNCION` a texto para garantizar la compatibilidad con años.

```python
df['ANIO_DEFUNCION'] = df['ANIO_DEFUNCION'].astype(str)
```

### Paso 3: Separar los datos por año
El script genera archivos separados para cada año en el conjunto de datos.

```python
for year in df['ANIO_DEFUNCION'].unique():
    yearly_data = df[df['ANIO_DEFUNCION'] == year]
    yearly_data.to_csv(f'mortalidad_materna_{year}.csv', index=False)
```

### Paso 4: Clasificar las causas
Define un diccionario para agrupar los códigos CIE en categorías relevantes, como `Sepsis`, `Hypertension`, etc. Luego, clasifica cada fila según estas categorías.

```python
def clasificar_causa(codigo):
    for causa, codigos in causas.items():
        if codigo.startswith(tuple(codigos)):
            return causa
    return None

df['Causa Clasificada'] = df['CAUSA_CIE_4D'].apply(clasificar_causa)
```

### Paso 5: Generar conteos agrupados
Agrupa los datos por estado (`ENTIDAD_RESIDENCIAD`) y categoría de causa (`Causa Clasificada`) y calcula los conteos para cada combinación.

```python
conteo_causas = df.groupby(['ENTIDAD_RESIDENCIAD', 'Causa Clasificada']).size().reset_index(name='Conteo')
```

### Paso 6: Crear la tabla final
La salida final incluye columnas para cada año con los conteos correspondientes. Puedes pivotar los datos para lograr este formato.

```python
tabla_final = conteo_causas.pivot(index=['ENTIDAD_RESIDENCIAD'], columns='ANIO_DEFUNCION', values='Conteo')
tabla_final.to_csv('conteo_anual_por_estado.csv')
```

### Paso 7: Guardar los resultados
El script guarda un archivo CSV para cada año y un archivo consolidado con los conteos por estado y año.

---

## Ejecución del script

1. Coloca el archivo `mortalidad_materna_2002_2022_resumen.csv` en el mismo directorio que el script.
2. Ejecuta el script:
   ```bash
   python script_procesamiento_MM.py
   ```
3. Los archivos generados se guardarán en el mismo directorio.

## Resultado esperado

El archivo consolidado `conteo_anual_por_estado.csv` tendrá la siguiente estructura:

| Estado       | CIE Código | 2002 | 2003 | ... | 2022 |
|--------------|------------|------|------|-----|------|
| Estado 1     | O00        | 12   | 10   | ... | 8    |
| Estado 2     | O10        | 5    | 6    | ... | 7    |

