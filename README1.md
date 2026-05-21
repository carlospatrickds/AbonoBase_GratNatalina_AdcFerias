# 🧮 Arquitetura e Funcionamento: Sistema de Cálculo Judicial

Este código é uma **Single Page Application (SPA)** — uma aplicação web de página única — construída puramente com HTML, CSS e JavaScript (Vanilla JS). Ele não precisa de um servidor (backend) para funcionar; todo o processamento dos dados ocorre diretamente no navegador do usuário, o que garante rapidez e privacidade dos dados manipulados.

A finalidade principal do sistema é automatizar a **apuração de reflexos financeiros** decorrentes da inclusão do "Abono de Permanência" na base de cálculo do 13º salário (Gratificação Natalina) e no adicional de 1/3 de férias, aplicando as devidas correções monetárias e juros da Justiça Federal.

Abaixo está o detalhamento técnico de como o código está estruturado e o que cada bloco faz:

## 1. Estrutura Visual e Estilização (CSS)

* **Variáveis Dinâmicas (`:root`):** O layout utiliza variáveis CSS para permitir a troca instantânea de temas. Ao selecionar um tema (Sistema, Clássico, Salmão), o JavaScript altera a classe da tag `<body>`, e o navegador repinta as cores das tabelas e botões em tempo real.
* **Modo Escuro Nativo:** Utiliza a *media query* `@media (prefers-color-scheme: dark)` para adaptar automaticamente a interface gráfica para tons escuros caso o sistema operacional do usuário esteja configurado assim.
* **Otimização para Impressão (`@media print`):** Quando o usuário clica em imprimir, o CSS esconde todos os elementos interativos (botões, modais, inputs de arquivo) e reformata a tela para gerar um relatório em formato de documento. Também é o responsável por forçar a exibição do rodapé, aplicar o papel timbrado de fundo na dimensão correta e respeitar as margens (sangria) escolhidas.

## 2. Motores de Processamento (JavaScript)

### A. Apuração Histórica (Aba 1)

* **Leitura da Ficha Financeira:** O usuário cola dados tabulares em um `textarea`. A função `processar()` lê essas linhas e quebra os dados através de separadores de tabulação (`\t`).
* **Agrupamento e Matemática:** O script varre as colunas em busca dos cabeçalhos chave ("Descrição", "Valor", "Competência"). Ele agrupa os valores por mês/ano e cruza o que foi pago ("Abono", "Gratificação", "Férias") para encontrar a diferença (o que é devido à parte autora).
* **Prescrição Quinquenal:** A função `aplicarPrescricao()` automatiza o corte de parcelas prescritas. Ela pega a data de ajuizamento, retroage 5 anos (mês a mês) e desabilita matematicamente qualquer parcela da tabela que seja anterior a esse marco temporal.

### B. Atualização Monetária CJF (Aba 2)

* **Conexão Externa (Fetch/Proxies):** Para não depender de preenchimento manual dos índices de correção, a função `carregarIndicesPlanilha()` se conecta a uma planilha pública do Google Sheets. Para driblar o bloqueio de segurança dos navegadores (CORS), o código roteia a requisição de forma inteligente por serviços de proxy (`corsproxy.io`, `allorigins`, etc.).
* **Matemática Financeira:** A rotina `renderCorrecao()` é o coração do cálculo atualizado. Ela isola o subtotal de cada mês, permite o abatimento de valores pagos administrativamente, aplica o fator de correção monetária sobre o saldo (índice da planilha) e, sobre o resultado, calcula a porcentagem dos Juros de Mora e da Taxa SELIC.

### C. Distribuição do Crédito e Resumo (Resumo Final)

* **Função `calcularTotaisTodasAbas()`:** Pega o montante final da condenação e o distribui. Ela retém os honorários contratuais (ex: 20%) e isola o valor líquido do autor. Se houver sucumbência inserida, ela calcula o percentual a ser pago pelo réu por cima da condenação total.

## 3. Gerenciamento de Estado e Arquivos

* **Máscaras Baseadas em Regex:** A função `mascaraCNJ()` utiliza Expressões Regulares (Regex) para formatar o número do processo simultaneamente à digitação, inserindo traços e pontos no padrão `0000000-00.0000.0.00.0000`.
* **Engine de Arquivos (`.json`):** * **Exportação:** Para não perder o cálculo, a rotina `salvarProjeto()` constrói um objeto JavaScript com todas as variáveis da tela, converte para texto JSON, empacota como um arquivo binário (Blob) no navegador e dispara o download automático. Ela usa o nome do Autor digitado na interface para nomear o arquivo.
* **Importação:** Usando a `FileReader API`, a função `processarArquivoJSON()` captura o arquivo carregado, desempacota o JSON e injeta os valores diretamente no DOM (HTML), reconstruindo o exato momento em que o trabalho foi salvo.


* **Drag and Drop:** Ouve os eventos do mouse (`dragover`, `drop`) para criar uma camada visual (`dragOverlay`) onde o usuário pode simplesmente arrastar o arquivo JSON para dentro da tela para carregá-lo.
* **Armazenamento Local (Cache):** Informações de personalização visual — como o tema escolhido, as margens da impressão, a cor do texto do rodapé e a imagem Base64 do papel timbrado — são salvas permanentemente no `localStorage` do navegador para serem recuperadas quando a página for reaberta no futuro.

4. Desafios Enfrentados na Refatoração
Durante o aprimoramento e estabilização do código, lidamos com obstáculos técnicos bem peculiares, principalmente envolvendo a renderização do navegador e o gerenciamento de estados:

Conflitos de Sobreposição na Impressão (Z-Index e Background): Um dos maiores desafios foi o comportamento do CSS no modo @media print. Ao inserir uma imagem de papel timbrado como fundo (com position: fixed e cobrindo 100vh/100vw), a camada da imagem estava engolindo o rodapé do documento. A solução exigiu forçar a hierarquia visual (z-index: 9999 !important) no texto do rodapé e amarrar dinamicamente a margem inferior (padding) do container à variável de sangria escolhida pelo usuário.

Gatilhos de Eventos (Event Listeners) Conflitantes: O sistema sofria de "efeitos colaterais" indesejados na interface. Por exemplo, o simples ato de clicar no campo "Data de Ajuizamento" estava acionando a função aplicarPrescricao(), que por sua vez encontrava o campo vazio e resetava abruptamente o seletor para "Não Aplicar". Foi necessário isolar a lógica para que o sistema só processasse a prescrição quando a data estivesse, de fato, completamente preenchida.

Sanitização de Dados Heterogêneos (Datas):
Como as fichas financeiras coladas pelos usuários variam muito, o sistema não conseguia ler datas abreviadas por extenso (ex: "abr/2025"). Tivemos que implementar um "tradutor" nativo (parseMesAnoIndex) que intercepta a colagem dos dados, identifica substrings de meses em português e converte tudo para o padrão matemático "MM/YYYY" antes de injetar na tabela.

A "Regra de Ouro" de Manutenção: O desafio arquitetônico final foi consertar lógicas profundas em JavaScript sem violar o ecossistema de HTML e CSS que já estava homologado e aprovado visualmente.

5. Particularidades da Arquitetura
O sistema possui características muito próprias que o distanciam de uma aplicação web tradicional:

Arquitetura 100% Client-Side (Sem Servidor Backend): Para garantir a segurança de dados sensíveis de processos e evitar custos de hospedagem de servidores, toda a inteligência do sistema roda na memória RAM do computador do usuário final. Se a internet cair após a página carregar, o sistema continua calculando e gerando PDFs normalmente.

Contorno de CORS para Consumo de Planilhas: Como a aplicação não tem servidor próprio, buscar os índices da Justiça Federal direto do Google Sheets gera um bloqueio de segurança do navegador (CORS). A particularidade aqui foi construir um sistema de "Roleta de Proxies" (corsproxy.io, allorigins, codetabs). Se um proxy for bloqueado pelo firewall do tribunal, o código tenta o próximo automaticamente, garantindo a resiliência da atualização monetária.

Motor Customizado de Impressão (Sangria Dinâmica):
Em vez de usar bibliotecas pesadas geradoras de PDF (como jsPDF ou html2pdf), a aplicação "hackeia" o motor de impressão nativo do navegador (window.print()). Ela injeta regras CSS dinâmicas no <head> do documento no exato milissegundo antes de imprimir, permitindo que o usuário ajuste margens de topo e fundo em centímetros para encaixar o conteúdo perfeitamente no seu papel timbrado.

6. Especificidades e Regras de Negócio Implementadas
Detalhes granulares que tornam o sistema uma ferramenta judicial, e não apenas uma calculadora comum:

Máscara Rigorosa do CNJ:
Diferente de máscaras comuns (como CPF), a máscara do Processo Judicial (0000000-00.0000.0.00.0000) exige validações específicas. A lógica de Regex foi construída para aplicar traços e pontos em tempo real, travando exatamente em 25 caracteres para evitar erros de autuação.

Estabilização de Fusos Horários na Geração de Relatórios:
Instâncias JavaScript de new Date() costumam puxar o timezone do sistema operacional, o que ocasionalmente gerava datas do tipo "Ontem às 21h" em vez de "Hoje". A especificidade foi contornar o objeto de data genérico, forçando a extração isolada de Dia, Mês e Ano, formatando-os com padStart para garantir o carimbo estrito DD/MM/AAAA.

Organização Dinâmica de Arquivos Salvos:
Como o fluxo de trabalho jurídico lida com dezenas de clientes, baixar arquivos com o nome genérico projeto.json causaria caos. A função de salvamento varre o campo "Parte Autora", substitui espaços por underlines (_) e converte para maiúsculas, gerando o download com uma nomenclatura limpa, como CALC_INC_ABONO_JOAO_DA_SILVA.json.
