# 📚 Gerador de Ficha Catalográfica IFB

Um aplicativo web interativo e intuitivo para gerar fichas catalográficas de acordo com os padrões do Instituto Federal de Brasília (IFB). A ferramenta automatiza o processo de preenchimento e formatação de fichas catalográficas para trabalhos acadêmicos, gerando arquivos PDF prontos para uso.

## 🎯 O que é uma Ficha Catalográfica?

A ficha catalográfica é um documento obrigatório que deve constar no verso da folha de rosto de trabalhos acadêmicos (TCCs, monografias, dissertações e teses). Ela padroniza informações sobre a obra, facilitando o registro e localização em sistemas de bibliotecas.

## ✨ Características Principais

- **Interface Amigável**: Formulário limpo e organizado com campos bem definidos
- **Suporte Múltiplos Tipos de Trabalho**:
  - TCCs de Graduação
  - Trabalhos de Especialização
  - Dissertações de Mestrado
- **Múltiplos Cursos**: Suporta todos os cursos do IFB distribuídos entre seus diversos campi
- **Geração de PDF Automática**: Cria a ficha catalográfica em formato PDF pronto para impressão
- **Validação de Dados**: Verifica se todos os campos obrigatórios foram preenchidos
- **Suporte a Múltiplos Autores**: Permite adicionar até 5 autores para o trabalho
- **Coorientação**: Opção de incluir coorientador(a) quando necessário
- **Palavras-chave**: Suporta de 3 a 5 palavras-chave do trabalho

## 🚀 Como Usar

### 1. **Acessar a Ferramenta**
Simplesmente abra o arquivo `index.html` em seu navegador web. Nenhuma instalação é necessária!

### 2. **Preencher os Dados do Autor(a) Principal**
Na primeira seção, insira:
- **Nome completo (exceto último sobrenome)**: Ex: Maria Aquino Gomes da
- **Último sobrenome**: Ex: Silva

> 💡 **Dica**: Separe o nome completo da forma correta. O último sobrenome deve ser colocado em campo separado para garantir a formatação correta na ficha.

### 3. **Adicionar Outros Autores (Opcional)**
Se o trabalho possui múltiplos autores, clique em "Clique aqui para inserir demais autores" para expandir os campos de autores adicionais. Você pode adicionar até 4 autores extras (total de 5).

### 4. **Informar o Título do Trabalho**
Digite o título completo do seu trabalho:
- **Regra de capitalização**: Maiúscula apenas na primeira letra e em nomes próprios
- **Subtítulo**: Se houver, separe com dois pontos (:)

Exemplo: *Machado de Assis: vida e obra*

### 5. **Selecionar o Tipo de Trabalho e Curso**
Escolha uma das três opções:

#### 📖 **TCC — Graduação**
- Selecione o curso em que você se graduou
- Os cursos estão organizados por campus do IFB

#### 🎓 **TCC — Especialização**
- Escolha o curso de especialização concluído
- Disponível em diversos campi

#### 🏆 **Dissertação — Mestrado**
- Para trabalhos de mestrado profissional
- Selecione o programa de mestrado

### 6. **Informar Dados de Orientação**
Preencha:
- **Nome completo do orientador(a)**: Ex: Luciana Diniz de
- **Último sobrenome do orientador(a)**: Ex: Albuquerque

### 7. **Adicionar Coorientador(a) (Opcional)**
Se o trabalho possui coorientação, clique em "Clique aqui para inserir o(a) Coorientador(a)" e preencha os dados do(a) coorientador(a).

### 8. **Informar Detalhes do Trabalho**
Complete os seguintes campos:
- **Ano de Defesa**: Ano em que você defendeu seu trabalho (formato: AAAA)
- **Nº de folhas**: Quantidade total de folhas do trabalho (ex: 66)
- **Ilustrações**: Selecione o tipo de ilustração do trabalho:
  - Sem ilustrações
  - Ilustrações em preto e branco
  - Ilustrações coloridas

### 9. **Adicionar Palavras-chave**
Insira entre 3 e 5 palavras-chave que representam o conteúdo do seu trabalho:
- Minimum: 3 palavras-chave (obrigatório)
- Máximo: 5 palavras-chave

> 📌 **Regra Importante**: As palavras-chave devem estar em **ordem decrescente de importância**, sem repetição ou redundância.

Exemplo:
1. Conhecimento
2. Informação
3. Serendipidade
4. Metodologia científica
5. Trabalho de conclusão de curso

### 10. **Gerar o PDF**
Depois de preencher todos os campos obrigatórios, clique no botão **"Gerar PDF"**. A ficha catalográfica será gerada e baixada automaticamente com o nome `ficha_catalografica_IFB.pdf`.

## 📋 Campos Obrigatórios

Os seguintes campos **devem ser preenchidos** para gerar a ficha:
- ✅ Nome do autor(a) principal
- ✅ Último sobrenome do autor(a) principal
- ✅ Título do trabalho
- ✅ Tipo de trabalho (TCC Graduação, Especialização ou Mestrado)
- ✅ Curso selecionado (de acordo com o tipo)
- ✅ Nome e sobrenome do orientador(a)
- ✅ Ano de defesa
- ✅ Número de folhas
- ✅ Tipo de ilustração
- ✅ Mínimo de 3 palavras-chave

## 🎨 Tecnologias Utilizadas

O projeto foi desenvolvido com:

- **HTML5**: Estrutura semântica do formulário
- **CSS (Tailwind CSS)**: Estilização moderna e responsiva através do CDN
- **JavaScript (Vanilla)**: Lógica de interatividade e geração de PDF
- **jsPDF**: Biblioteca para criar e manipular documentos PDF
- **Google Fonts (Inter)**: Tipografia profissional

## 🏗️ Estrutura do Projeto

```
Gerador_Ficha_Catalografica_IFB/
├── index.html          # Página única contendo formulário e lógica
├── README.md           # Este arquivo
└── [Orientações]       # Link para documento de orientações no Google Drive
```

## 💻 Como o PDF é Gerado

Quando você clica em "Gerar PDF", o aplicativo:

1. **Coleta todos os dados** preenchidos no formulário
2. **Valida os campos obrigatórios** para garantir completude
3. **Formata as informações** de acordo com os padrões bibliográficos do IFB
4. **Monta a estrutura** da ficha catalográfica com:
   - Dados do autor(a) principal
   - Título e subtítulo
   - Informações de orientação
   - Descrição física (páginas, ilustrações)
   - Dados do trabalho (tipo, curso, instituição, ano)
   - Palavras-chave numeradas
   - Entrada secundária (outros autores, orientador, instituição)
5. **Gera o arquivo PDF** e o baixa automaticamente no seu computador

## 📌 Dicas Importantes

### ✍️ Formatação de Nomes
- Separe o **último sobrenome** dos demais nomes
- A ficha utilizará o formato: `SOBRENOME, Nome Completo`

### 📚 Múltiplos Autores
- Quando há 3 ou mais autores, apenas o primeiro é exibido no PDF, seguido de "[et al.]"
- Todos os autores são listados na entrada secundária (seção de referências)

### 🏛️ Campus Disponíveis
- Brasília
- Ceilândia
- Estrutural
- Gama
- Planaltina
- Recanto das Emas
- Riacho Fundo
- Samambaia
- São Sebastião
- Taguatinga

### 🔧 Limpeza do Formulário
Use o botão **"Limpar"** para resetar todos os campos e começar novamente.

## 📖 Orientações Oficiais

Para mais informações sobre o padrão de fichas catalográficas do IFB, consulte as [Orientações para Preenchimento](https://drive.google.com/open?id=1PJ5Lz5duildBQ-XhAvmBAMFZGF4Kuak5&usp=drive_fs) disponível no Google Drive.

## 🌐 Compatibilidade

- ✅ Google Chrome
- ✅ Mozilla Firefox
- ✅ Safari
- ✅ Microsoft Edge
- ✅ Responsivo (funciona em tablets e dispositivos móveis)

**Requisito**: Navegador moderno com suporte a JavaScript habilitado.

## 📧 Suporte

Para dúvidas ou sugestões sobre o uso desta ferramenta, entre em contato com a biblioteca do IFB ou verifique as orientações oficiais fornecidas.

## 📝 Licença

Este projeto é fornecido como ferramenta auxiliar para a comunidade acadêmica do Instituto Federal de Brasília.

---

**Desenvolvido com ❤️ para facilitar a vida dos estudantes do IFB**

*Última atualização: 2026*
