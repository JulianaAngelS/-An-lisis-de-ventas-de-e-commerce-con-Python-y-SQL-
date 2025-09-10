# -An-lisis-de-ventas-de-e-commerce-con-Python-y-SQL-
Este proyecto analiza un dataset simulado de ventas de un e-commerce, con el objetivo de identificar patrones de compra, calcular métricas clave (ingresos, ticket promedio, clientes recurrentes) y crear visualizaciones que respalden decisiones de negocio.
# =============================
# Librerías
# =============================
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import sqlite3

# =============================
# Cargar dataset
# =============================
df = pd.read_csv("../data/sales.csv")

# Vista rápida
print(df.head())

# =============================
# KPIs principales
# =============================
total_ingresos = df["total_price"].sum()
ticket_promedio = df["total_price"].mean()
clientes_unicos = df["customer_id"].nunique()

print(f"💰 Ingresos totales: {total_ingresos:,.0f}")
print(f"🛒 Ticket promedio: {ticket_promedio:,.2f}")
print(f"👥 Clientes únicos: {clientes_unicos}")

# =============================
# Visualización: Ventas por categoría
# =============================
plt.figure(figsize=(8,5))
sns.barplot(data=df, x="category", y="total_price", estimator=sum, ci=None)
plt.title("Ingresos por categoría")
plt.xticks(rotation=45)
plt.show()

# =============================
# Integración con SQL
# =============================
# Crear DB temporal y cargar datos
conn = sqlite3.connect(":memory:")
df.to_sql("sales", conn, index=False, if_exists="replace")

# Query: Top 5 productos más vendidos
query = """
SELECT product, COUNT(*) as total_ventas, SUM(total_price) as ingresos
FROM sales
GROUP BY product
ORDER BY ingresos DESC
LIMIT 5
"""
top_products = pd.read_sql(query, conn)
print(top_products)
