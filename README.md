# 🏠 Simulador de Investimento Imobiliário — Portugal (Fiscalidade 2026)

Uma aplicação web **single-file** (um único `index.html`, sem build, sem backend) para
simular e comparar estratégias de investimento imobiliário no mercado português,
com a fiscalidade em vigor em **2026**.

Pensada para quem avalia dezenas de imóveis antes de decidir: prioriza clareza dos
números e velocidade de iteração — mexes nos sliders e tudo recalcula ao instante.

> ⚠️ **Aviso:** todos os valores são estimativas para fins de análise. A fiscalidade
> (sobretudo Categoria B, depreciações e IVA da reabilitação/construção) tem nuances
> que exigem confirmação por um **contabilista certificado**. Não é aconselhamento
> financeiro nem fiscal.

---

## ✨ O que faz

- **Três estratégias** lado a lado, com inputs e outputs que se adaptam a cada uma:
  1. **Reabilitar & Arrendar (BRRR)** — comprar degradado, reabilitar, arrendar e, opcionalmente, refinanciar sobre o valor pós-obras para recuperar capital.
  2. **Reabilitar & Revender (Flip)** — comprar, reabilitar, vender. Foco na margem líquida após impostos.
  3. **Terreno & Construir** — comprar terreno urbano, construir de raiz e depois arrendar **ou** vender.
- **Vista "Comparar as 3"** — tabela e gráficos com capital próprio, ROE/ROI e património final, com os mesmos pressupostos.
- **Glossário integrado** — cada termo (IMT, LTV, equity, yield, cashflow, mais-valias…) explicado em linguagem simples, com pesquisa.
- **Cálculo 100% no browser** — nada é enviado para servidores; o estado vive só em memória.

### Outputs principais (recalculados em tempo real)

Capital próprio necessário · montante financiado e prestação (com/sem carência) ·
cashflow mensal e anual · yield bruto e líquido · ROE · equity criada no dia 1
(separando a valorização forçada pela obra) · cenário de refinanciamento (capital
recuperado vs. preso) · margem líquida da venda após impostos · break-even ·
e uma projeção a X anos do património líquido.

---

## 🚀 Como usar

### Online

Depois do deploy (ver abaixo), basta abrir o URL. Funciona em **telemóvel e PC**.

### Localmente

Como é um único ficheiro estático, não precisas de instalar nada:

- **Opção simples:** faz duplo-clique no `index.html` para abrir no browser.
- **Opção recomendada** (evita restrições de alguns browsers ao abrir ficheiros locais):
  serve a pasta com um servidor estático qualquer, por exemplo:

  ```bash
  # Python (já vem instalado na maioria dos sistemas)
  python3 -m http.server 8000
  # depois abre http://localhost:8000
  ```

  ```bash
  # ou com Node
  npx serve .
  ```

> O React, o ReactDOM e o Babel são carregados via CDN, por isso é preciso ligação à
> internet na primeira vez que abres.

---

## 🧾 Fiscalidade implementada (Portugal, 2026)

As regras estão escritas como **funções puras** e as constantes ficam todas no topo do
`<script>`, comentadas e fáceis de atualizar. Sempre que uma regra tem condições que
podem variar, está assinalada com `// VERIFICAR:`.

| Imposto / regra                   | Implementação                                                                                                                                                                      |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **IMT — habitação secundária**    | Tabela III (Continente) por escalões progressivos com taxa marginal e parcela a abater. 1.º escalão a 1% (sem isenção, ao contrário da habitação própria permanente).              |
| **IMT — terreno de construção**   | Taxa fixa de **6,5%**.                                                                                                                                                             |
| **IMT — prédio rústico**          | 5% (referência).                                                                                                                                                                   |
| **IMT Jovem**                     | Tabela II (≤35 anos, 1.ª habitação própria permanente) — **toggle informativo**, não aplicável a investimento.                                                                     |
| **Imposto do Selo**               | **0,8%** sobre o valor de aquisição.                                                                                                                                               |
| **Escritura + registo**           | Valor fixo editável (default ~1.200 €).                                                                                                                                            |
| **IRS rendas — Categoria F**      | 10% (habitacional, renda moderada — OE 2026), 28% (não habitacional), e reduções por duração de contrato (15% / 10% / 5%). Só deduz despesas; **não** deduz juros nem depreciação. |
| **IRS rendas — Categoria B / AL** | Contabilidade organizada: deduz **juros + depreciação (2%/ano sobre construção, excluindo terreno) + despesas**, tributado por englobamento à taxa marginal.                       |
| **Mais-valias**                   | Cat G (pontual): 50% do ganho tributado. Cat B (recorrente): 95% do ganho.                                                                                                         |
| **IMI anual**                     | Default 0,35% sobre o VPT (taxa de concelho editável, 0,3%–0,45%).                                                                                                                 |

**Fonte das tabelas de IMT:** Ofício Circulado n.º **40129/2026** da Autoridade
Tributária (tabelas em vigor desde 1/1/2026).

### Pressupostos marcados como `// VERIFICAR`

- **Fração do valor imputável ao terreno** (não depreciável em Cat B): assumida em 25%.
- **Alojamento Local**: implementado apenas o regime de **contabilidade organizada**;
  o regime simplificado por coeficientes não está incluído.

---

## 🗂️ Estrutura do projeto

```
simulador-imobiliario-pt/
├── index.html      # a aplicação inteira (lógica fiscal + UI + gráficos)
├── README.md       # este ficheiro
├── LICENSE         # licença MIT
└── .gitignore
```

Dentro do `index.html`, a organização é:

1. **Constantes fiscais** — tabelas de IMT, taxas, regimes de IRS (fácil de atualizar num só sítio).
2. **Funções puras** — `calcIMT`, `prestacaoMensal`, `saldoDivida`, `irsRendas`, `impostoMaisValias`, e o motor `computeScenario(inputs, estrategia)`.
3. **Componentes de UI** — inputs, sliders, cards, tooltips, e gráficos SVG escritos à mão (sem dependências de bibliotecas de charts).
4. **Vistas** — Simular, Comparar e Glossário.

---

## 🧩 Stack técnica

- **React 18** + **ReactDOM** (via CDN, UMD)
- **Babel Standalone** (transpila o JSX no browser — sem passo de build)
- **Gráficos SVG próprios** e **ícones SVG embutidos** (zero dependências de charts/ícones)
- Tipografia: _Space Grotesk_, _Inter_, _IBM Plex Mono_ (Google Fonts)

Sem `node_modules`, sem bundler, sem backend. Um ficheiro.

---

## ➕ Como estender

O código foi pensado para acrescentar uma 4.ª estratégia sem grande refactor:

1. Adiciona uma entrada em `STRATS` (nome, ícone, cor).
2. Trata o novo ramo em `computeScenario(...)`.
3. Acrescenta os inputs específicos nas secções e os cards de output correspondentes.

As funções fiscais já são reutilizáveis e independentes da estratégia.

---

## ⚖️ Aviso legal

Esta ferramenta produz **estimativas** com base nos pressupostos que introduzes e nas
regras parametrizadas no código. Não substitui aconselhamento profissional. Confirma
sempre os números com um contabilista certificado e/ou intermediário de crédito antes
de tomar decisões de investimento.

---

## 📄 Licença

[MIT](LICENSE) — usa, modifica e partilha à vontade.
