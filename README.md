🇦🇷 [Leer en español](README.es.md)

# Denim West — Customer LTV Prediction

A predictive model that ranks customers by their expected value over the next 6 months, so marketing and retention efforts can be prioritized toward the customers most likely to keep buying — instead of treating every customer the same.

**Stack:** Python · pandas · [`lifetimes`](https://github.com/CamDavidsonPilon/lifetimes) (BG/NBD + Gamma-Gamma) · Google Colab

---

## The problem

Denim West had years of raw order history sitting in Tiendanube exports, but no way to answer a basic retention question: *"which customers are worth investing in right now, before they churn?"* Every customer looked the same in a spreadsheet — there was no ranking by future value, only by past totals, which says nothing about who's likely to buy again.

## The approach

A two-stage probabilistic model, applied to each customer's purchase history:

1. **RFM summary** — for every customer, compute Recency (days since last order), Frequency (number of orders), and Monetary value (total spent).
2. **BG/NBD model** (Beta-Geometric / Negative Binomial Distribution) — learns each customer's buying rhythm and predicts *how many* purchases they're likely to make in the next 180 days.
3. **Gamma-Gamma model** — learns each customer's typical spend and predicts the *average value* of each future purchase.
4. **LTV = predicted purchases × predicted average value** — the two predictions combined give a 6-month forward-looking value estimate per customer, not just a backward-looking total.

The output is a ranked CSV: every recurring customer, sorted from highest to lowest predicted 6-month value.

## Data pipeline (inside the notebook)

```
raw Tiendanube export (CSV)
  → forward-fill multi-product order fields
  → keep only completed ("Recibido") payments
  → collapse to one row per order
  → RFM aggregation per customer
  → BG/NBD fit → predicted purchase count
  → Gamma-Gamma fit → predicted average order value
  → LTV_6_Meses_Predicho = purchases × avg value
  → ranked CSV export
```

## A note on privacy

The original notebook was trained on real Denim West customer data — real emails, names, and purchase history. **None of that is in this repository.** The dataset here (`data/ventas_ejemplo.csv`) has every personally identifying field replaced with fake values (`cliente_0001@ejemplo.com`, etc.) while preserving the real transactional patterns — same purchase frequency, same order values, same dates — so the model's behavior and results are fully representative of what it actually produced, without exposing a single real customer.

## Running it

Open `notebook/Predictivo_LTV_DenimWest.ipynb` in Google Colab or Jupyter, with `data/ventas_ejemplo.csv` in the same working directory (or update the path in the first cell). Install the one dependency not preinstalled in Colab:

```bash
pip install lifetimes
```

Run all cells top to bottom. The final cell exports `LTV_Predicho_DenimWest.csv` with the ranked customer list.

---

## About this project

Built as a self-directed project to practice applied probabilistic modeling on real e-commerce data, as a data analyst / performance analyst. It's the companion piece to my [Denim West Analytics Pipeline](https://github.com/jgonzalezarteaga/pipeline-analitica-denim-west) — that project answers *"what happened with sales and ad spend,"* this one answers *"which customers matter most going forward."*

**Jesús González** — [LinkedIn](https://www.linkedin.com/in/jes%C3%BAs-gonz%C3%A1lez-arteaga/) · [Portfolio](https://slender-pudding-6db.notion.site/Data-Analytics-E-commerce-Growth-Portfolio-Jes-s-Gonz-lez-2b2537b01ef9806fb8dac6165ff2745d)