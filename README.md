## Optimización del Consumo Energético y Análisis Operativo en Proceso de Extrusión Industrial
### Metodología y Supuestos del Proyecto

### Metodología Analítica

El proyecto se estructuró en cinco fases metodológicas orientadas a la evaluación no paramétrica y la optimización del Consumo Específico de Energía ($\text{KPI} = \text{kWh/ton}$):

1. **Estandarización y Calidad de Datos:**
   * Limpieza de nombres de variables bajo convención `snake_case` y conversión de tipos de datos numéricos.
   * Eliminación de registros corruptos/sin medición de KPI (~15.27% de las observaciones iniciales) y asignación categórica de turnos operativos:
     * **Mañana:** 06:00:00 – 17:59:59
     * **Noche:** 18:00:00 – 05:59:59

2. **Detección de Anomalías por Criterio IQR (Rango Intercuartílico):**
   * Debido a la presencia de fallas de lectura en sensores del SCADA (valores astronómicos de hasta $10^{14}\text{ kWh/ton}$ por divisiones por cero implícitas en tiempos muertos), se descartaron las métricas paramétricas (media y desviación estándar).
   * Se aplicó la regla del Rango Intercuartílico sobre el dataset válido:
     $$\text{Límite Inferior} = Q_1 - 1.5 \times \text{IQR} \quad (124.92\text{ kWh/ton})$$
     $$\text{Límite Superior} = Q_3 + 1.5 \times \text{IQR} \quad (217.91\text{ kWh/ton})$$

3. **Prueba de Hipótesis 1 (Comparación de Turnos):**
   * Se aplicó la **Prueba U de Mann-Whitney** ($\alpha = 0.05$) para evaluar diferencias en la mediana del KPI entre la Mañana y la Noche.
   * Se calculó el **Tamaño del Efecto ($r$ de correlación biserial de rangos)** para diferenciar la significancia estadística de la relevancia operativa en el negocio.

4. **Prueba de Hipótesis 2 (Efecto del Perfil Térmico):**
   * Agrupación de la temperatura promedio del barril (`temp_barril_promedio`) y la temperatura de masa fundida (`melt_temp_suv`) en tercioles de igual tamaño ($N \approx 11,318$): **Bajo (T1)**, **Medio (T2)** y **Alto (T3)**.
   * Se ejecutó la **Prueba de Kruskal-Wallis** ($\alpha = 0.05$) para validar formalmente la hipótesis de la empresa sobre la influencia de las recetas de temperatura en el consumo de los motores.

5. **Visualización y Exportación:**
   * Generación de diagramas de caja en escala logarítmica $\log_{10}$ y curvas de densidad en régimen normal.
   * Exportación del dataset consolidado y limpio en formato CSV (`historico_extrusora_procesado.csv`).

---

### 📋 Principales Supuestos

1. **Naturaleza de los Outliers Extremos:**
   * Se asume que los valores atípicos severos ($> 217.91\text{ kWh/ton}$ hasta $10^{14}\text{ kWh/ton}$) no representan fallas físicas de la extrusora en producción activa, sino lecturas de sensor en "vacío" o paradas no programadas con motores/calentadores encendidos ($\text{kWh} > 0$ con $\text{ton} \approx 0$).

2. **Definición de Jornadas Operativas:**
   * Se asumió un esquema de dos turnos de 12 horas consecutivas (Mañana y Noche) de acuerdo con los estándares habituales de operación industrial continuada.

3. **Independencia Muestral:**
   * Se asume independencia entre las observaciones de cada registro horario/minutario para la aplicación de las pruebas no paramétricas (Mann-Whitney U y Kruskal-Wallis).

4. **Carácter Observacional de los Datos:**
   * Se asume que los datos provienen del histórico natural de la planta y no de un experimento diseñado (DOE), por lo que las asociaciones encontradas entre la temperatura y el KPI representan compatibilidad y evidencia estadística, mas no causalidad estricta desprovista de variables de confusión (ej. tipo de polímero o espesor).
