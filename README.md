# ¿Los minutos en cancha generan puntos?

Rendimiento anotador en la NBA y el problema de la especificación.

**Proyecto final de Econometría — Universidad Panamericana, otoño 2026**
Gabriel · Alberto · Albert

---

## La pregunta

¿El tiempo en cancha tiene un efecto propio sobre los puntos que anota un jugador, o su aparente efecto es solo un reflejo de cuántos tiros toma?

La respuesta depende por completo de una decisión de especificación, y ese contraste es el argumento del proyecto.

| | Modelo 1 — contable | Modelo 2 — rol y uso |
|---|---|---|
| R² | 0.9917 | 0.8639 |
| β de MP | +0.0024 | +0.4773 |
| t de MP | 0.27 | **17.21** |
| Decisión (α = 0.05) | no se rechaza H₀ | **se rechaza H₀** |
| VIF máximo | 17.3 (FGA) | 5.7 (TOV) |

El modelo con el R² más alto es el que menos responde la pregunta.

## El problema

En el basquetbol se cumple, por definición:

```
PTS = 2 × FG + 3P + FT        donde    FG = FGA × FG%
```

Verificado en el **100% de los 16,512 registros** del archivo.

Por lo tanto `FGA`, `FG%`, `3PA` y `FTA` son los insumos aritméticos de `PTS`. Un modelo que los usa como regresores no estima una relación económica: reproduce una ecuación contable. Su R² de 0.99 es mecánico.

El Modelo 2 los excluye y explica los puntos a partir del rol del jugador. Ahí sí aparece el efecto de los minutos: **+0.477 puntos por minuto adicional**, IC 95% [0.423, 0.532].

## Cómo correr el análisis

1. Abrir [Google Colab](https://colab.research.google.com) → Archivo → Subir notebook → `Proyecto_Econometria_NBA.ipynb`
2. Correr la celda 1. Pide subir `NBA_Regresion_Completo.xlsx` (está en este repo).
3. Correr las demás celdas en orden.

No hace falta instalar nada: Colab ya trae `pandas`, `numpy`, `statsmodels`, `scipy` y `matplotlib`.

También corre en local con `pip install pandas numpy statsmodels scipy matplotlib openpyxl`, dejando el `.xlsx` en la misma carpeta.

## Qué hay en este repositorio

| Archivo | Qué es |
|---|---|
| `Proyecto_Econometria_NBA.ipynb` | El análisis completo: limpieza, descriptivos, los dos modelos, inferencia, multicolinealidad, diagnóstico y robustez |
| `NBA_Regresion_Completo.xlsx` | Datos crudos: 16,512 registros partido a partido, temporada 2024-25 |
| `Presentacion_Econometria_NBA.pptx` | Presentación de 8 minutos, con el guion en las notas del orador |

## Datos

| | |
|---|---|
| Fuente | Estadísticas oficiales de la NBA, temporada regular 2024-25 |
| Periodo | 22-oct-2024 a 7-feb-2025 |
| Unidad de análisis | Jugador (promedios de temporada) |
| Muestra | 474 jugadores con al menos 10 partidos |

**Tres decisiones de limpieza documentadas:**

1. **Se promedia por jugador.** Usar los 16,512 registros violaría la independencia de las observaciones: cada jugador aparece hasta 50 veces.
2. **FG% se calcula como aciertos totales / intentos totales**, no como promedio de los porcentajes por partido.
3. **Se repara la codificación de los nombres** (`Nikola Jokiƒá` → `Nikola Jokić`), que venían con una doble conversión UTF-8 / Mac Roman en 504 registros.

## Diagnóstico

| Supuesto | Prueba | p | Decisión al 5% |
|---|---|---|---|
| Varianza constante | Breusch-Pagan | < 0.001 | se rechaza H₀ |
| Normalidad | Jarque-Bera | < 0.001 | se rechaza H₀ |

Ninguno invalida el resultado: con n = 474 el Teorema Central del Límite cubre la falta de normalidad, y la varianza desigual se corrige con errores robustos HC3 — con los que **ninguna de las diez decisiones de significancia cambia**.

Verificaciones adicionales: la forma log-nivel mantiene el resultado (t = 21.04), y excluyendo temporalmente las 64 observaciones influyentes el coeficiente cambia solo 6.6%. El modelo reportado incluye a los 474 jugadores.

## Límites

1. **No se identifica causalidad.** Los minutos no se asignan al azar: el entrenador da más tiempo a quien ya anota bien. Falla el supuesto de media condicional cero y ninguna corrección de errores estándar lo arregla.
2. **Variables omitidas:** posición, talento previo, sistema ofensivo, lesiones.
3. **TOV es un proxy** del uso del balón, no una causa de los puntos.
4. El modelo explica promedios de temporada, no partidos individuales.
5. El periodo no cubre la temporada completa.
