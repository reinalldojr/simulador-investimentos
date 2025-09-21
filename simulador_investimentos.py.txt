import streamlit as st
import pandas as pd
import matplotlib.pyplot as plt

st.set_page_config(page_title="Simulador de Investimentos", layout="wide")

st.title("💰 Simulador de Investimentos")
st.write("Planeje sua jornada para R$ 1.000.000")

# Parâmetros de entrada
st.sidebar.header("Parâmetros de Investimento")
valor_inicial = st.sidebar.number_input("Valor Inicial (R$)", min_value=0, value=500000, step=1000, format="%d")
aporte_mensal = st.sidebar.number_input("Aporte Mensal (R$)", min_value=0, value=1000, step=100, format="%d")
rentabilidade_anual = st.sidebar.slider("Rentabilidade Anual (%)", min_value=0.0, max_value=50.0, value=12.0, step=0.1)

meta_valor = 1_000_000
taxa_mensal = (1 + rentabilidade_anual / 100) ** (1/12) - 1
max_meses = 600  # limite de 50 anos

valores = []
patrimonio = valor_inicial
for mes in range(max_meses + 1):
    valores.append(patrimonio)
    patrimonio = patrimonio * (1 + taxa_mensal) + aporte_mensal
    if patrimonio >= meta_valor:
        meses_atingir_meta = mes
        break
else:
    meses_atingir_meta = max_meses

anos = meses_atingir_meta // 12
meses_restantes = meses_atingir_meta % 12

total_investido = valor_inicial + aporte_mensal * meses_atingir_meta
valor_final = valores[meses_atingir_meta]
ganhos_juros = valor_final - total_investido

# Exibir resultados
col1, col2, col3, col4 = st.columns(4)
col1.metric("Total Investido (R$)", f"R$ {total_investido:,.2f}")
col2.metric("Juros Ganhos (R$)", f"R$ {ganhos_juros:,.2f}")
col3.metric("Valor Final (R$)", f"R$ {valor_final:,.2f}")
col4.metric("Tempo para Meta", f"{anos} anos e {meses_restantes} meses")

# Dados para gráfico
df = pd.DataFrame({
    "Meses": list(range(meses_atingir_meta + 1)),
    "Valor Total": valores[:meses_atingir_meta + 1],
    "Valor Investido": [valor_inicial + aporte_mensal * i for i in range(meses_atingir_meta + 1)]
})

# Gráfico de Evolução do Patrimônio
st.subheader("Evolução do Patrimônio")
fig, ax = plt.subplots(figsize=(10, 5))
ax.plot(df["Meses"], df["Valor Total"], label="Valor Total (R$)", color="blue")
ax.plot(df["Meses"], df["Valor Investido"], label="Valor Investido (R$)", color="orange")
ax.set_xlabel("Meses")
ax.set_ylabel("R$ (Reais)")
ax.legend()
ax.grid(True)
st.pyplot(fig)

st.caption("Feito com Perplexity Labs - estilo inspirado na imagem enviada.")
