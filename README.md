# Módulo 4 — Cobertura de Cartera de Acciones con Futuros de Índice

## Descripción

Proyecto de gestión de riesgo de mercado que implementa una estrategia de cobertura (*hedging*) sobre una cartera de acciones de empresas argentinas utilizando futuros E-mini S&P 500. Se calcula el beta estático y dinámico de la cartera, se deriva el hedge ratio óptimo, y se evalúa la efectividad de la cobertura mediante backtesting histórico (2021–2024).

---

## Estructura del proyecto

Modulo 4
Cobertura_con_Futuros.ipynb   # Analisis completo en Python
parametros.xlsx               # Dashboard de parametros y sensibilidad
README.md

---

## Fases del análisis

| Fase | Contenido |
| 1 — Datos | Descarga de precios históricos via Yahoo Finance, cálculo de retornos logarítmicos diarios, construcción de la cartera ponderada |
| 2 — Beta estático | Regresión OLS sobre el período completo, derivación del hedge ratio puntual y número de contratos |
| 3 — Beta dinámico | Rolling OLS con ventana de 60 días hábiles, evolución temporal del hedge ratio |
| 4 — Beta objetivo | Simulación de distintos niveles de exposición de mercado mediante ajuste de contratos |
| 5 — Backtesting | Comparación de P&L entre cartera sin cubrir y cartera cubierta, métricas de performance |

---

## Cartera

| Ticker (NYSE) | Empresa | Peso |
| GGAL | Grupo Financiero Galicia | 25% |
| YPF | YPF S.A. | 20% |
| BMA | Banco Macro | 20% |
| CEPU | Central Puerto | 20% |
| PAM | Pampa Energía | 15% |

**Índice de referencia:** S&P 500 (`^GSPC`)  
**Instrumento de cobertura:** E-mini S&P 500 (`ES=F`) — multiplicador: USD 50 por punto  
**Período:** 01/01/2021 — 31/12/2024  
**Valor nocional de la cartera:** USD 1.000.000

---

## Parámetros del modelo

| Parámetro | Valor |
| Beta estático | 0.9789 |
| R² | 0.1307 |
| p-value | < 0.0001 |
| Ventana rolling | 60 días hábiles |
| Precio futuro proxy (Vf) | 5.906,94 (S&P500 al 30/12/2024) |
| Contratos hedge total | 3 (redondeado) |

**Convención de signo en contratos:** negativo = vender futuros (reducir exposición), positivo = comprar futuros (aumentar exposición).

---

## Resultados del backtesting

| Métrica | Sin cobertura | Cubierta |
| Retorno acumulado | 504.02% | 281.06% |
| Volatilidad anual | 45.17% | 42.55% |
| Sharpe ratio | 1.29 | 1.05 |
| Max drawdown | -40.18% | -34.13% |

---

## Supuestos y limitaciones

1. **Basis nulo:** se usa el nivel spot del S&P 500 como proxy del precio del futuro E-mini, asumiendo basis ≈ 0. En la práctica, el precio teórico del futuro es `F = S · e^((r-d)·T)`.

2. **Vf histórico:** el número de contratos rolling se calcula usando el precio diario del índice como proxy contemporáneo del futuro.

3. **R² bajo (0.13):** el principal driver de riesgo de una cartera de acciones argentinas no es el mercado americano sino el riesgo país (político, macroeconómico, cambiario). La cobertura con E-mini neutraliza solo el componente de riesgo sistémico global (~13% de la varianza). Una cobertura óptima requeriría instrumentos indexados al riesgo soberano argentino (CDS, futuros de tipo de cambio).

4. **Beta no constante:** el beta rolling muestra un incremento estructural post-2023, atribuible a la reducción del riesgo país tras el cambio de gobierno, lo que aumentó la correlación de las acciones con el mercado global.

---

## Herramientas

| Herramienta | Uso |
| Python (`pandas`, `numpy`, `scipy`, `matplotlib`, `yfinance`) | Descarga de datos, regresión, rolling beta, backtesting, gráficos |
| Excel | Dashboard de parámetros y tabla de sensibilidad por beta objetivo |

---

## Fuentes de datos

- **Yahoo Finance** via `yfinance`: precios históricos de ADRs y S&P 500
- **CME Group**: especificaciones del contrato E-mini S&P 500 (multiplicador USD 50/punto)
