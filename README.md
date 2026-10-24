# 🌊 Análise Exploratória de Vazão de Poço com Medidor Ultrassônico Portátil

Projeto de automação e ciência de dados aplicada à instrumentação industrial para análise exploratória de dados (EDA) de vazão e diagnóstico de operação de bombeamento em poço tubular profundo.

---

## 📌 Visão Geral do Projeto

Os dados foram extraídos em campo utilizando um **medidor de vazão ultrassônico portátil por tempo de trânsito (clamp-on)** com transdutores externos acoplados à tubulação de recalque de um poço artesiano.

O objetivo deste projeto é:
1. **Sanitização Forense de Dados (ETL):** Tratar particularidades brutas do arquivo exportado pelo firmware do medidor (bytes nulos `\x00`, linha de unidades físicas, vírgulas residuais e rodapé proprietário).
2. **Análise de Processo / Produção:** Avaliar o regime de vazão da motobomba submersa, estabilidade da coluna d'água e balanço volumétrico totalizado.
3. **Diagnóstico Metrológico Acústico:** Auditar a integridade da medição através da relação sinal-ruído (SNR), ganho eletrônico e velocidade do som no fluido (garantindo ausência de ar arrastado ou cavitação).

---

## 📊 Principais Indicadores do Poço (KPIs - Teste de 15 minutos)

| Indicador | Valor Medido | Observação Técnica |
| :--- | :--- | :--- |
| **Duração do Teste** | 15,0 minutos (900 s) | Amostragem contínua a 1 Hz (1 leitura/segundo) |
| **Vazão Volumétrica Média** | **46,56 m³/h** (~12,93 L/s) | Operação em regime contínuo estável |
| **Faixa Operacional** | 46,20 a 46,96 m³/h | Amplitude de apenas 0,76 m³/h |
| **Estabilidade da Bomba (CV%)** | **0,31%** | Desvio padrão ínfimo (0,08 m³/h), sem golpe de aríete |
| **Volume Total Extraído** | **11.626,11 L** (**11,63 m³**) | Registrado pelo totalizador direto do medidor |
| **Velocidade Média no Tubo** | **2,51 m/s** | Escoamento turbulento plenamente desenvolvido |
| **Velocidade do Som no Fluido** | **1501,88 m/s** | Confirma água pura a ~26,5 °C sem aeração |
| **Relação Sinal-Ruído (SNR)** | **36,93 dB** (Up) / **37,18 dB** (Down) | Muito acima do limite mínimo recomendado (> 30 dB) |
| **Status de Erro** | **0 falhas** (`No Error` em 100%) | Medição 100% válida e confiável |

---

## 🛠️ Desafios Técnicos no Arquivo Bruto (`Dados/P1_Str0248.csv`)

Durante a inspeção em modo texto puro com Python, identificamos que a leitura ingênua com `pandas.read_csv()` falha e corrompe os tipos de dados por 4 motivos:
1. **Bytes Nulos (`\x00`):** Presentes no cabeçalho antes de `Error`, `Sound Speed` e após `Signal to Noise Ratio Down`.
2. **Linha de Unidades:** A linha 2 traz grandezas físicas (`m/s`, `m^3/h`, `db`), convertendo todos os números para texto (`str`) caso não seja ignorada.
3. **Vírgula Residual no Final:** As linhas de dados contêm uma vírgula extra no fim (20 campos contra 19 do cabeçalho), causando deslocamento de índice.
4. **Mensagem de Rodapé:** A linha 903 contém a string `Congrats! Channel 1 Linear END!`, que exige o uso de `skipfooter=1, engine='python'`.

---

## 📁 Estrutura do Repositório

```text
├── Dados/
│   └── P1_Str0248.csv       # Arquivo de dados brutos exportado pelo medidor
├── analise_vazao.py         # Script interativo em células Jupyter (# %%)
├── analise_vazao.ipynb      # Notebook Jupyter com documentação e código
├── requirements.txt         # Dependências do projeto (pandas, numpy, matplotlib)
├── .gitignore               # Arquivos ignorados pelo controle de versão
└── README.md                # Documentação técnica do projeto
```

---

## 🚀 Como Executar o Projeto

### 1. Clonar o Repositório
```bash
git clone https://github.com/<SEU_USUARIO>/<SEU_REPOSITORIO>.git
cd "Medidor de Vazão Portátil"
```

### 2. Criar e Ativar o Ambiente Virtual (Opcional, mas recomendado)
```bash
python -m venv .venv
# No Windows PowerShell:
.venv\Scripts\Activate.ps1
```

### 3. Instalar Dependências
```bash
pip install -r requirements.txt
```

### 4. Executar a Análise

* **Opção 1 (VS Code Interativo):** Abra [analise_vazao.py](analise_vazao.py) e utilize os botões `Run Cell` para executar bloco por bloco interativamente.
* **Opção 2 (Jupyter Notebook):** Abra [analise_vazao.ipynb](analise_vazao.ipynb) no Jupyter Notebook ou JupyterLab.
* **Opção 3 (Terminal):**
  ```bash
  python analise_vazao.py
  ```

---

## 📈 Conclusões para Engenharia e Automação

1. **Capacidade do Poço:** Sob o ponto de operação testado, a bomba operou com vazão contínua de ~46,5 m³/h sem demonstrar rebaixamento crítico do nível da água ou entrada de ar nos 15 minutos de ensaio.
2. **Projeção Contínua 24h:** A produção estimada do poço é de **1.117 m³/dia** (~1.117.000 L/dia) sob regime contínuo.
3. **Confiabilidade da Instrumentação:** O medidor ultrassônico apresentou acoplamento acústico perfeito (SNR > 36 dB), sem perda de pulsos e com ganho estabilizado em 16,05 dB.
