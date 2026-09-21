🇺🇸 [Read in English](README.md)

# Denim West — Predicción de LTV de clientes

Un modelo predictivo que ordena a los clientes según su valor esperado en los próximos 6 meses, para que el esfuerzo de marketing y retención se priorice hacia los clientes con más probabilidad de seguir comprando — en vez de tratar a todos los clientes igual.

**Stack:** Python · pandas · [`lifetimes`](https://github.com/CamDavidsonPilon/lifetimes) (BG/NBD + Gamma-Gamma) · Google Colab

---

## El problema

Denim West tenía años de historial de pedidos crudo en exports de Tiendanube, pero ninguna forma de responder una pregunta básica de retención: *"¿en qué clientes vale la pena invertir ahora, antes de que dejen de comprar?"* En una planilla, todos los clientes se ven iguales — no hay un orden por valor futuro, solo por total histórico, que no dice nada sobre quién es probable que vuelva a comprar.

## El enfoque

Un modelo probabilístico en dos etapas, aplicado al historial de compras de cada cliente:

1. **Resumen RFM** — para cada cliente, calcular Recencia (días desde su última compra), Frecuencia (cantidad de pedidos) y Monetización (total gastado).
2. **Modelo BG/NBD** (Beta-Geometric / Negative Binomial Distribution) — aprende el ritmo de compra de cada cliente y predice **cuántas** compras es probable que haga en los próximos 180 días.
3. **Modelo Gamma-Gamma** — aprende el gasto típico de cada cliente y predice el **valor promedio** de cada compra futura.
4. **LTV = compras predichas × valor promedio predicho** — las dos predicciones combinadas dan una estimación de valor a 6 meses hacia adelante, no solo un total histórico.

El resultado es un CSV ordenado: todos los clientes recurrentes, de mayor a menor valor predicho a 6 meses.

## Pipeline de datos (dentro del notebook)

```
export crudo de Tiendanube (CSV)
  → rellenar hacia abajo campos de pedidos multi-producto
  → quedarse solo con pagos completados ("Recibido")
  → colapsar a una fila por pedido
  → agregación RFM por cliente
  → ajuste BG/NBD → cantidad de compras predicha
  → ajuste Gamma-Gamma → valor promedio de pedido predicho
  → LTV_6_Meses_Predicho = compras × valor promedio
  → export del CSV ordenado
```

## Una nota sobre privacidad

El notebook original se entrenó con datos reales de clientes de Denim West — emails, nombres e historial de compra reales. **Nada de eso está en este repositorio.** El dataset acá (`data/ventas_ejemplo.csv`) tiene cada campo de identificación personal reemplazado por valores ficticios (`cliente_0001@ejemplo.com`, etc.), preservando el patrón transaccional real — misma frecuencia de compra, mismos montos de pedido, mismas fechas — así que el comportamiento y los resultados del modelo son completamente representativos de lo que produjo en realidad, sin exponer a ningún cliente real.

## Cómo correrlo

Abrí `notebook/Predictivo_LTV_DenimWest.ipynb` en Google Colab o Jupyter, con `data/ventas_ejemplo.csv` en el mismo directorio de trabajo (o actualizá la ruta en la primera celda). Instalá la única dependencia que no viene preinstalada en Colab:

```bash
pip install lifetimes
```

Corré todas las celdas de arriba hacia abajo. La última celda exporta `LTV_Predicho_DenimWest.csv` con la lista de clientes ordenada.

---

## Sobre este proyecto

Construido como proyecto autodidacta para practicar modelado probabilístico aplicado sobre datos reales de e-commerce, como analista de datos / performance. Es la pieza complementaria de mi [Pipeline de Analítica — Denim West](https://github.com/jgonzalezarteaga/pipeline-analitica-denim-west) — ese proyecto responde *"qué pasó con las ventas y el gasto en ads"*, este responde *"qué clientes importan más de acá en adelante"*.

**Jesús González** — [LinkedIn](https://www.linkedin.com/in/jes%C3%BAs-gonz%C3%A1lez-arteaga/) · [Portfolio](https://slender-pudding-6db.notion.site/Data-Analytics-E-commerce-Growth-Portfolio-Jes-s-Gonz-lez-2b2537b01ef9806fb8dac6165ff2745d)