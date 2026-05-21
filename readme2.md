# ⚖️ Sistema de Cálculo Judicial

### Inclusão do Abono de Permanência na Base de Cálculo do 13º e 1/3 de Férias

<p align="center">
  <img src="https://img.shields.io/badge/STATUS-EM%20DESENVOLVIMENTO-2ea44f?style=for-the-badge">
  <img src="https://img.shields.io/badge/Vanilla-JavaScript-f7df1e?style=for-the-badge&logo=javascript&logoColor=000">
  <img src="https://img.shields.io/badge/SPA-CLIENT%20SIDE-blue?style=for-the-badge">
  <img src="https://img.shields.io/badge/SEM%20BACKEND-100%25%20LOCAL-orange?style=for-the-badge">
</p>

---

## 📌 Sobre o Projeto

O **Sistema de Cálculo Judicial** é uma aplicação web desenvolvida para automatizar a apuração de diferenças remuneratórias decorrentes da inclusão do **Abono de Permanência** na base de cálculo:

* da **Gratificação Natalina (13º salário)**;
* do **Adicional Constitucional de 1/3 de Férias**.

Toda a arquitetura foi construída em **HTML, CSS e JavaScript puro (Vanilla JS)**, sem dependência de backend, banco de dados ou frameworks externos.

O processamento ocorre integralmente no navegador do usuário, garantindo:

✅ Maior velocidade
✅ Independência de servidor
✅ Baixo custo operacional
✅ Privacidade dos dados processuais
✅ Funcionamento mesmo offline após carregamento inicial

---

# 🧠 Arquitetura da Aplicação

A aplicação segue o modelo de **Single Page Application (SPA)**, centralizando toda a lógica de negócio em uma única página dinâmica.

```text
┌────────────────────────────┐
│        INTERFACE UI        │
│ HTML + CSS Responsivo      │
└────────────┬───────────────┘
             │
             ▼
┌────────────────────────────┐
│      MOTOR JAVASCRIPT      │
│ Processamento dos cálculos │
│ Correção monetária         │
│ Prescrição quinquenal      │
│ Geração de relatórios      │
└────────────┬───────────────┘
             │
             ▼
┌────────────────────────────┐
│  DADOS PROCESSADOS LOCAL   │
│ JSON / LocalStorage / DOM  │
└────────────────────────────┘
```

---

# 🎨 Interface e Estilização

## Temas Dinâmicos

O sistema utiliza variáveis CSS (`:root`) para alternância instantânea de temas:

* Sistema
* Clássico
* Salmão

A troca é feita dinamicamente via JavaScript, alterando classes no `<body>`.

---

## 🌙 Modo Escuro Nativo

Compatibilidade automática com:

```css
@media (prefers-color-scheme: dark)
```

A interface adapta cores e contraste conforme o sistema operacional do usuário.

---

## 🖨️ Engine de Impressão Inteligente

O sistema não depende de bibliotecas externas de PDF.

A geração de relatórios utiliza:

```javascript
window.print()
```

com regras CSS dinâmicas aplicadas em tempo real.

### Recursos implementados:

* ocultação automática de elementos interativos;
* ajuste de margens/sangria;
* suporte a papel timbrado;
* preservação de layout judicial;
* rodapé persistente;
* otimização para impressão profissional.

---

# ⚙️ Motores de Processamento

# 📑 1. Apuração Histórica

## Leitura da Ficha Financeira

Os dados são colados diretamente em um `textarea`.

O sistema:

* identifica colunas automaticamente;
* interpreta tabulações (`\t`);
* organiza competências;
* cruza verbas financeiras.

---

## 🧮 Cálculo das Diferenças

A lógica identifica:

* Abono de Permanência;
* Gratificação Natalina;
* Férias;
* Reflexos devidos.

O agrupamento ocorre por competência (`MM/AAAA`).

---

## ⏳ Prescrição Quinquenal

A função:

```javascript
aplicarPrescricao()
```

realiza:

* retroação automática de 5 anos;
* exclusão matemática de parcelas prescritas;
* atualização dinâmica da tabela.

---

# 💰 2. Atualização Monetária CJF

## Integração com Google Sheets

Os índices são carregados automaticamente através de planilha pública.

---

## 🌐 Sistema de Contorno de CORS

Como a aplicação é 100% client-side, foi desenvolvido um mecanismo resiliente de proxies:

```text
corsproxy.io
allorigins
codetabs
```

Caso um endpoint falhe, o sistema tenta o próximo automaticamente.

---

## 📈 Correção Monetária

A rotina:

```javascript
renderCorrecao()
```

executa:

* abatimento administrativo;
* aplicação de índices CJF;
* juros de mora;
* cálculo da SELIC;
* subtotalização mensal;
* totalização final.

---

# 📊 3. Resumo Final da Condenação

A função:

```javascript
calcularTotaisTodasAbas()
```

é responsável pela distribuição financeira:

* honorários contratuais;
* valor líquido da parte autora;
* sucumbência;
* condenação total;
* resumo consolidado.

---

# 💾 Gerenciamento de Estado e Arquivos

# 📁 Exportação de Projetos

A função:

```javascript
salvarProjeto()
```

gera arquivos `.json` contendo:

* parâmetros do cálculo;
* tabelas processadas;
* configurações visuais;
* estados da aplicação.

---

## 🧷 Nomeação Inteligente

Os arquivos são gerados automaticamente no padrão:

```text
CALC_INC_ABONO_JOAO_DA_SILVA.json
```

---

# 📂 Importação de Projetos

Utilizando:

```javascript
FileReader API
```

o sistema reconstrói integralmente o cálculo salvo anteriormente.

---

# 🖱️ Drag and Drop

O usuário pode simplesmente:

* arrastar o arquivo `.json`;
* soltar na tela;
* restaurar instantaneamente o projeto.

---

# 🧠 Persistência Local

Utiliza:

```javascript
localStorage
```

para armazenar:

* tema escolhido;
* margens de impressão;
* rodapé;
* papel timbrado;
* preferências do usuário.

---

# 🔍 Particularidades Técnicas

# 🧩 Máscara CNJ

Validação rigorosa do padrão:

```text
0000000-00.0000.0.00.0000
```

Aplicada em tempo real com Regex.

---

# 🗓️ Normalização de Datas

O sistema interpreta formatos heterogêneos:

```text
abr/2025
04/2025
2025-04
```

convertendo automaticamente para:

```text
MM/AAAA
```

---

# 🕒 Estabilização de Timezone

Para evitar divergências causadas pelo `Date()` do navegador, a aplicação força:

```text
DD/MM/AAAA
```

com preenchimento seguro via `padStart()`.

---

# 🧱 Desafios de Engenharia

## 🔥 Conflitos de Impressão

Problemas envolvendo:

* `z-index`;
* `position: fixed`;
* papel timbrado;
* rodapé sobreposto.

Foram solucionados com hierarquia visual dinâmica e ajustes automáticos de sangria.

---

## ⚠️ Event Listeners Conflitantes

Eventos indevidos estavam disparando cálculos involuntários.

A solução exigiu:

* isolamento de estados;
* validação prévia de inputs;
* desacoplamento de gatilhos automáticos.

---

## 🧼 Sanitização de Dados

As fichas financeiras apresentavam extrema heterogeneidade textual.

Foi implementado um parser customizado capaz de:

* reconhecer meses por extenso;
* corrigir formatos;
* padronizar competências.

---

# 🏛️ Filosofia do Projeto

Este sistema foi desenvolvido com foco em:

* independência tecnológica;
* segurança processual;
* desempenho;
* confiabilidade matemática;
* compatibilidade com rotinas jurídicas reais.

Sem servidores.
Sem banco de dados.
Sem dependência de frameworks pesados.

Apenas lógica, matemática financeira e processamento local.

---

# 🚀 Tecnologias Utilizadas

| Tecnologia                | Finalidade             |
| ------------------------- | ---------------------- |
| HTML5                     | Estrutura da aplicação |
| CSS3                      | Interface e impressão  |
| JavaScript Vanilla        | Lógica de negócio      |
| Google Sheets API pública | Índices monetários     |
| LocalStorage              | Persistência local     |
| FileReader API            | Importação de arquivos |
| Blob API                  | Exportação JSON        |

---

# 📌 Recursos Implementados

✅ Cálculo de reflexos do Abono de Permanência
✅ Prescrição quinquenal automática
✅ Atualização monetária CJF
✅ Juros de mora e SELIC
✅ Importação/exportação JSON
✅ Drag and Drop
✅ Temas dinâmicos
✅ Papel timbrado para impressão
✅ Modo escuro automático
✅ Persistência local
✅ Máscara CNJ
✅ Engine própria de impressão

---

# ⚖️ Observação Jurídica

O sistema possui finalidade técnica de apoio à elaboração de cálculos judiciais, não substituindo:

* conferência profissional;
* análise processual;
* validação jurídica individualizada.

---

# 👨‍💻 Autor

Desenvolvido para otimizar rotinas de cálculos judiciais previdenciários e administrativos, com foco em precisão matemática, produtividade e autonomia operacional.

---

<p align="center">
  <b>“Automatizar o operacional para dedicar tempo ao estratégico.”</b>
</p>
