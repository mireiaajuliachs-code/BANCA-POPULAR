# Análisis exploratorio de una campaña de marketing bancario

El proyecto analiza una campaña telefónica de una institución bancaria portuguesa para describir qué características se asocian con la suscripción a un depósito a plazo. La variable `y` indica si el registro terminó en suscripción (`yes` o `no`).

## Estructura del proyecto

- `README.md` — instrucciones e informe del análisis.
- `data/raw/bank-additional.csv` — registros de la campaña.
- `data/raw/customer-details.xlsx` — datos de clientes en tres hojas (2012, 2013 y 2014).
- `data/processed/bank_marketing_clean.csv` — datos transformados que genera el notebook.
- `notebooks/EDA_Análisis_datos_Banca_Portugal.ipynb` — limpieza, análisis y gráficos.

## Cómo ejecutar el proyecto

1. Abrir la carpeta raíz del repositorio en Visual Studio Code. Es necesario tener Python 3 y las extensiones **Python** y **Jupyter** de VS Code.
2. En la terminal de VS Code, instalar las dependencias:

   ```bash
   python -m pip install pandas numpy openpyxl matplotlib ipykernel
   ```

3. Abrir el notebook y seleccionar como kernel el mismo entorno de Python en el que instalaste las dependencias.
4. Pulsar **Ejecutar todo**. El notebook carga los archivos desde `data/raw/`, muestra las tablas y los gráficos, y genera el CSV en `data/processed/`.

## Preparación y limpieza de los datos

1. Se concatenaron las tres hojas del Excel, se conservó el año de la hoja de origen y se quitó la columna de índice auxiliar.
2. Se normalizaron los identificadores como texto y se comprobó su unicidad. La unión externa con validación uno a uno permitió identificar **43.000 coincidencias** y **170 registros del Excel sin campaña**. El análisis utiliza las 43.000 filas coincidentes.
3. Se convirtieron las fechas y las columnas numéricas con separador decimal de coma. Se conservaron los valores faltantes. Los más frecuentes fueron los de `euribor3m` (21,53 %), `default` (20,89 %) y `age` (11,91 %). Los resúmenes numéricos indican cuántos valores válidos se utilizaron.
4. Se conservó el campo original `pdays` y se creó `pdays_validos` para excluir el código especial `999` del cálculo de días. Quedaron **1.588 valores válidos**, con una mediana de **6 días**.
5. Se exportaron las 43.000 filas sin la columna de auditoría `_merge` ni el identificador duplicado `ID`. El archivo procesado conserva `id_`.

## Principales hallazgos

### Suscripción al depósito

La tasa global fue del **11,27 %**. Para cada grupo, la tasa se calculó dividiendo el número de suscripciones entre el total de registros del grupo.

| Grupo | Registros | Suscripciones | Tasa |
|---|---:|---:|---:|
| Total | 43.000 | 4.844 | 11,27 % |
| Canal móvil | 27.396 | 4.039 | 14,74 % |
| Canal telefónico | 15.604 | 805 | 5,16 % |
| Campaña anterior con éxito (`SUCCESS`) | 1.436 | 938 | 65,32 % |
| Campaña anterior sin éxito (`FAILURE`) | 4.461 | 635 | 14,23 % |
| Resultado anterior `NONEXISTENT` | 37.103 | 3.271 | 8,82 % |

El gráfico de barras muestra una diferencia de **9,58 puntos porcentuales** entre los canales. Entre los grupos por resultado anterior, los registros con `SUCCESS` presentaron la mayor tasa de suscripción.

### Duración de la llamada

El diagrama de caja compara la duración de la última llamada según el resultado. La mediana fue de **449 segundos** en el grupo con suscripción y de **163 segundos** en el grupo sin suscripción. La diferencia es descriptiva. Para determinar si alargar la llamada aumenta la probabilidad de suscripción haría falta otro tipo de análisis.

### Correlaciones entre variables numéricas

Se calcularon correlaciones de Pearson entre edad, ingresos, duración, contactos de la campaña actual, contactos previos y visitas web mensuales. Las de mayor magnitud fueron las del número de contactos de la campaña actual con los contactos previos (**r ≈ −0,08**) y con la duración (**r ≈ −0,07**). Ambas fueron muy débiles. Cada correlación se calculó con los registros que tenían datos en las dos variables correspondientes.

## Alcance de los resultados

Los resultados describen los registros de la campaña analizada. Las diferencias por canal y por resultado anterior son comparaciones sin ajustar por otras características de los grupos. El CSV original contiene `date`, con 248 valores faltantes, y el mes y el año de contacto se pueden derivar de esa columna para un análisis temporal. Las coordenadas se dejaron fuera de la interpretación geográfica.
