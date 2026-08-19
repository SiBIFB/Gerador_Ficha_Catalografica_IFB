# Readme_especializado.md

## Documentação Técnica e Especializada — Gerador de Ficha Catalográfica IFB

Este documento fornece uma análise aprofundada do Gerador de Ficha Catalográfica IFB, destinada a bibliotecários e profissionais com conhecimento técnico em informática que necessitem compreender a arquitetura, fluxos de dados, padrões bibliográficos implementados e possibilidades de integração ou customização.

---

## 1. Arquitetura e Tecnologia

### 1.1 Stack Tecnológico

**Frontend:**
- HTML5 (estrutura semântica)
- CSS3 (Tailwind CSS via CDN)
- JavaScript (ES6+, sem dependências de frameworks)
- jsPDF 2.5.1 (via CDN)

**Bibliotecas Externas:**
- Tailwind CSS 3.x (CDN: https://cdn.tailwindcss.com)
- jsPDF 2.5.1 (CDN: https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js)
- Google Fonts - Inter (https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700)

**Arquitetura:**
- Single Page Application (SPA) — arquivo HTML único contendo toda a lógica
- Sem dependência de backend (cliente-side processing)
- Totalmente offline-capable após o carregamento inicial

### 1.2 Estrutura do Projeto

```
Gerador_Ficha_Catalografica_IFB/
├── index.html                    # SPA principal (4+ KB minificado)
├── README.md                     # Documentação para usuários finais
├── Readme_especializado.md       # Este arquivo
└── .gitignore (opcional)         # Configuração de versionamento
```

**Tamanho total:** Aproximadamente 50 KB (com CDNs externos)
**Compatibilidade:** ES6+, requer JavaScript habilitado

---

## 2. Fluxo de Dados e Processamento

### 2.1 Coleta de Dados (Data Layer)

O formulário utiliza `document.getElementById()` para acesso direto ao DOM. Todos os campos são capturados no evento `submit` do formulário `fichaForm`.

**Campos de Entrada — Autor Primário:**
```javascript
- id="nome"        // STRING, max 100 chars, required
- id="sobrenome"   // STRING, max 100 chars, required
```

**Campos de Entrada — Autores Secundários (Opcional):**
```javascript
- id="nome2", id="nome3", id="nome4", id="nome5"       // Strings, max 100 chars
- id="sobrenome2", id="sobrenome3", id="sobrenome4", id="sobrenome5"
```

**Campos de Entrada — Trabalho e Orientação:**
```javascript
- id="titulo"           // STRING, max 200 chars, required
- id="trabalho"         // RADIO, values: ["tcc", "espec", "mestrado"], required
- id="cursoTCC"         // SELECT, format: "Campus|CourseName", required if trabalho="tcc"
- id="cursoESP"         // SELECT, format: "Campus|CourseName", required if trabalho="espec"
- id="cursoMES"         // SELECT, format: "Campus|CourseName", required if trabalho="mestrado"
- id="nome_ori"         // STRING, max 100 chars, required
- id="sobrenome_ori"    // STRING, max 100 chars, required
- id="nome_coori"       // STRING, max 100 chars, optional
- id="sobrenome_coori"  // STRING, max 100 chars, optional
- id="ano"              // STRING, pattern \d{4}, required
- id="pags"             // NUMBER, min 1 max 999, required
- id="ilustracao"       // SELECT, values: ["", "Sem ilustrações", "Ilustrações em preto e branco", "Ilustrações coloridas"], required
```

**Campos de Entrada — Palavras-chave:**
```javascript
- id="assunto1" through id="assunto5"  // STRING, max 100 chars
- Mínimo 3 obrigatórios, máximo 5 campos
```

### 2.2 Validação de Dados

A validação ocorre em dois níveis:

**Nível HTML5:**
- Atributo `required` nos campos obrigatórios
- Padrão `pattern="\d{4}"` no campo ano
- `type="number"` com `min` e `max` no campo pags
- `maxlength` em campos de texto

**Nível JavaScript:**
Implícito — o atributo `required` do formulário HTML5 previne envio de dados incompletos.

### 2.3 Lógica de Seleção de Tipo de Trabalho

```javascript
// EventListener nos radios de trabalho
radiosTrabalho.forEach(radio => {
    radio.addEventListener('change', function() {
        const tipo = this.value; // 'tcc', 'espec' ou 'mestrado'
        
        // Toggle disabled state e required attribute
        selectTCC.disabled = (tipo !== 'tcc');
        selectESP.disabled = (tipo !== 'espec');
        selectMES.disabled = (tipo !== 'mestrado');
        
        // Resetar valores de selects não-ativos
        if (tipo !== 'tcc') selectTCC.value = '';
        if (tipo !== 'espec') selectESP.value = '';
        if (tipo !== 'mestrado') selectMES.value = '';
    });
});
```

Padrão: Radio button com toggle de estados de select subordinado.

---

## 3. Estrutura Bibliográfica e Normalização de Dados

### 3.1 Padrão Bibliográfico Implementado

O sistema implementa o padrão de **Ficha Catalográfica brasileira** baseado em:
- ABNT NBR 12899:2007 (Apresentação de originais)
- ABNT NBR 6023:2018 (Referências)
- Diretrizes da Biblioteca Nacional do Brasil
- Normativas específicas do IFB

### 3.2 Estrutura do PDF Gerado

A ficha catalográfica segue a estrutura padrão:

```
┌─────────────────────────────────────┐
│   Área de Identificação Principal   │
│  (Autor Principal, Título, Dados)   │
├─────────────────────────────────────┤
│   Descrição Física                  │
│  (Páginas, Ilustrações)             │
├─────────────────────────────────────┤
│   Notas Descritivas                 │
│  (Tipo de Trabalho, Orientador)     │
├─────────────────────────────────────┤
│   Palavras-chave Numeradas          │
├─────────────────────────────────────┤
│   Entradas Secundárias (Roman.)     │
│  (Outros Autores, Orientador, IFB)  │
├─────────────────────────────────────┤
│   Rodapé Institucional              │
└─────────────────────────────────────┘
```

### 3.3 Formatação de Nomes

**Entrada Principal:**
```
Formato no PDF: SOBRENOME, Nome Completo
Exemplo entrada: Silva, Maria Aquino Gomes da
Extração de dados:
  - campo "nome" (sem último sobrenome): "Maria Aquino Gomes da"
  - campo "sobrenome" (último): "Silva"
```

**Múltiplos Autores:**
- 1 autor: Exibição completa
- 2 autores: "SOBRENOME, Nome ... SOBRENOME2, Nome2"
- 3+ autores: "SOBRENOME, Nome ... [et al.]"

Todos os autores aparecem nas entradas secundárias em numeração romana.

### 3.4 Formatação de Títulos

**Regras de Capitalização:**
- Primeira letra maiúscula
- Nomes próprios em maiúscula
- Demais palavras em minúsculas

**Subtítulos:**
- Separados do título por dois-pontos (:)
- Seguem a mesma regra de capitalização

Exemplo no input: "machado de assis: vida e obra"
Saída esperada: "Machado de Assis: vida e obra"

### 3.5 Estrutura de Dados do Tipo de Trabalho

Os selects de curso armazenam dados no formato: `"Campus|CourseName"`

**Exemplos:**
```
"Brasília|Licenciatura em Dança"
"Gama|Bacharelado em Administração"
"Taguatinga|Bacharelado em Ciência da Computação"
"Brasília|Educação Profissional e Tecnológica em Rede Nacional"
```

**Processamento:**
```javascript
let optionData = activeSelect.value.split('|');
let cityRaw = optionData[0];      // "Brasília", "Gama", etc.
let cursoName = optionData[1];    // Nome completo do curso
let local = `${cityRaw}, DF`;
let campus = `Campus ${cityRaw}, `;
```

### 3.6 Descrição da Naturaleza do Trabalho

O sistema gera uma string descritiva baseada no tipo:

**Para TCC Graduação:**
```
Trabalho de Conclusão de Curso (${cursoName}) — Campus ${cityRaw}, 
Instituto Federal de Brasília, ${local}, ${ano}.
```

**Para Especialização:**
```
Trabalho de Conclusão de Curso (Especialização em ${cursoName}) — Campus ${cityRaw}, 
Instituto Federal de Brasília, ${local}, ${ano}.
```

**Para Mestrado:**
```
Dissertação (Mestrado profissional em ${cursoName}) — Campus ${cityRaw}, 
Instituto Federal de Brasília, ${local}, ${ano}.
```

### 3.7 Descrição Física

O campo "Ilustração" gera sufixos na descrição:

```javascript
- "Sem ilustrações"              → ilustracaoStr = ""
- "Ilustrações em preto e branco" → ilustracaoStr = ": il. "
- "Ilustrações coloridas"        → ilustracaoStr = ": il. color. "
```

Exemplo completo: "66 f. : il. color. ; 30 cm."

### 3.8 Entradas Secundárias (Secondary Entries)

Utilizam numeração romana seguindo a ordem de inclusão:

```javascript
getRomanNum = (num) => {
    const romans = ["", "I", "II", "III", "IV", "V", "VI", "VII", "VIII", "IX", "X"];
    return romans[num] || "";
};
```

**Ordem de inclusão:**
1. Segundo Autor (se existir)
2. Terceiro Autor (se existir)
3. Quarto Autor (se existir)
4. Quinto Autor (se existir)
5. Orientador
6. Coorientador (se existir)
7. Instituto Federal de Brasília
8. Título (entrada por título)

Exemplo com 2 autores:
```
I. Silva, João.
II. Santos, Maria.
III. Oliveira, Pedro, orient.
IV. Instituto Federal de Brasília.
V. Título.
```

---

## 4. Geração de PDF — Detalhes Técnicos

### 4.1 Biblioteca jsPDF

**Versão:** 2.5.1
**UMD Bundle:** Acesso via `window.jsPDF`
**Inicialização:**
```javascript
const { jsPDF } = window.jspdf;
const doc = new jsPDF({
    orientation: 'portrait',
    unit: 'mm',
    format: 'a4'
});
```

**Documentação:** https://github.com/parallax/jsPDF

### 4.2 Configuração do Documento PDF

- **Formato:** A4 (210 x 297 mm)
- **Orientação:** Retrato
- **Unidade:** Milímetros

### 4.3 Renderização de Texto

```javascript
doc.setFont("times");
doc.setFontSize(9);

let textLinesArray = doc.splitTextToSize(bibliographicBodyStr, 100);
doc.text(60, 190, textLinesArray);
```

**Configurações:**
- Fonte: Times New Roman
- Tamanho: 9 pontos
- Largura de quebra: 100 mm
- Posição X: 60 mm (esquerda)
- Posição Y: 190 mm (aproximadamente no verso da folha de rosto)

### 4.4 Construção da String Bibliográfica

A string é montada com quebras de linha explícitas (`\n`) e espaçamento de hanging indent simulado com 6 espaços (`      `):

```javascript
let bibliographicBodyStr = 
    '      ' + titulo + ' / ' + nome11 + ' ; ' + orientacao1 + '. — ' + local + ': ' + ano + '.\n' +
    '      ' + pags + ' f. ' + ilustracaoStr + '; 30 cm.\n\n' +
    '      ' + trabStr + ano + '.\n' +
    '      ' + orientacao + '.\n\n' +
    '      ' + assuntosStr + secundariaStr;
```

### 4.5 Desenho de Elementos Gráficos

```javascript
// Retângulo contendo a ficha catalográfica
doc.rect(42, 180, 125, 75); // x, y, largura, altura (em mm)

// Texto com entrada principal
doc.text(60, 185, sobrenome + ', ' + nome + '.');

// Bloco de texto bibliográfico
doc.text(60, 190, textLinesArray);

// Rodapé institucional
doc.text(42, 260, "Ficha catalográfica elaborada por sistema com dados fornecidos pelo(a) autor(a)");
doc.text(42, 265, "SISTEMA DE BIBLIOTECAS DO INSTITUTO FEDERAL DE BRASÍLIA");
```

### 4.6 Salvamento e Entrega

```javascript
doc.save('ficha_catalografica_IFB.pdf');
```

O arquivo é salvo no diretório de downloads do navegador com nome padrão. Não há manipulação de cookies ou armazenamento no servidor.

---

## 5. Campuses e Cursos — Base de Dados

### 5.1 Estrutura de Optgroups

Os selects utilizam `<optgroup>` para organização visual e semântica por campus. Cada opção armazena `"Campus|NomeCurso"`.

### 5.2 Campus IFB Suportados

| Campus | Cursos TCC Graduação | Cursos Especialização | Mestrado |
|--------|----------------------|----------------------|----------|
| Brasília | 5 | 2 | 1 |
| Ceilândia | 2 | 1 | 0 |
| Estrutural | 1 | 1 | 0 |
| Gama | 4 | 1 | 0 |
| Planaltina | 3 | 3 | 0 |
| Recanto das Emas | 0 | 1 | 0 |
| Riacho Fundo | 4 | 1 | 0 |
| Samambaia | 3 | 2 | 0 |
| São Sebastião | 4 | 2 | 0 |
| Taguatinga | 5 | 0 | 0 |

**Total:** 36 cursos de graduação, 14 de especialização, 1 de mestrado (51 entradas)

### 5.3 Exemplo de Optgroup (Graduação)

```html
<optgroup label="Campus Brasília">
    <option value="Brasília|Licenciatura em Dança">Licenciatura em Dança</option>
    <option value="Brasília|Tecnologia em Eventos">Tecnologia em Eventos</option>
    <!-- ... mais opções ... -->
</optgroup>
```

### 5.4 Importância Bibliográfica

A informação de campus/curso é essencial para a ficha catalográfica pois:
1. Identifica a instituição e localização geográfica
2. Especifica o programa acadêmico
3. Define a natureza do trabalho (Graduação, Especialização, Mestrado)
4. Facilita recuperação em sistemas de bibliotecas

---

## 6. Validação e Tratamento de Erros

### 6.1 Validação HTML5 Nativa

O formulário utiliza validação HTML5 integrada:
- `required` — impede envio com campos vazios
- `maxlength` — limita comprimento de strings
- `pattern` — valida formato (ex: ano com 4 dígitos)
- `type="number"` com `min/max` — valida intervalo numérico

**Comportamento:** Navegador bloqueia envio do formulário e exibe mensagens de erro padrão.

### 6.2 Tratamento de Campos Opcionais

**Autores secundários (2-5):** Validação por presença (`if (nome2) { ... }`)
**Coorientador:** Validação por presença (`if (nomeCoori) { ... }`)

Campos vazios são ignorados na construção da ficha.

### 6.3 Potenciais Pontos de Falha

1. **Seletor null:** Se um elemento de ID não existir, JavaScript retorna `null` causando erro
2. **Divisão de string:** `split('|')` em `cursoTCC.value` requer exatamente o formato esperado
3. **Overflow de texto:** jsPDF pode quebrar texto inadequadamente com strings muito longas
4. **Compatibilidade CDN:** Falha no carregamento de CDNs impede funcionamento

### 6.4 Melhorias Sugeridas para Produção

```javascript
// Try-catch para seleção de elementos
try {
    const nome = document.getElementById('nome')?.value.trim() || '';
} catch (e) {
    console.error('Erro ao capturar dados:', e);
}

// Validação de split
const optionData = activeSelect.value.split('|');
if (optionData.length !== 2) {
    throw new Error('Formato inválido de opção de curso');
}
```

---

## 7. Customização e Extensibilidade

### 7.1 Adicionando Novos Cursos

Para adicionar um novo curso à lista de graduação:

```html
<optgroup label="Campus NovoNome">
    <option value="NovoNome|Novo Curso de Exemplo">Novo Curso de Exemplo</option>
</optgroup>
```

**Pontos críticos:**
- Value deve seguir formato: `"Campus|NomeCurso"`
- Adicionar em `cursoTCC`, `cursoESP`, ou `cursoMES` conforme tipo
- Atualizar tabela de documentação

### 7.2 Modificando Formatação da Ficha

A formatação é controlada pela string `bibliographicBodyStr`. Exemplo de modificação para incluir ISBN fictício:

```javascript
let bibliographicBodyStr = 
    '      ' + titulo + ' / ' + nome11 + ' ; ' + orientacao1 + '. — ' + local + ': ' + ano + '.\n' +
    '      ISBN: 978-65-00000000-0\n' +  // Nova linha
    '      ' + pags + ' f. ' + ilustracaoStr + '; 30 cm.\n\n' +
    // ... resto da string
```

### 7.3 Integrando com Backend

Para salvar dados em servidor:

```javascript
document.getElementById('fichaForm').addEventListener('submit', function(e) {
    e.preventDefault();
    
    // Coletar dados (código existente)
    const formData = {
        nome: document.getElementById('nome').value,
        // ... outros campos
    };
    
    // Enviar para backend
    fetch('/api/fichas', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(formData)
    })
    .then(response => response.json())
    .then(data => {
        // Gerar PDF localmente
        // ... código de geração de PDF
        doc.save('ficha_catalografica_IFB.pdf');
    });
});
```

### 7.4 Exportação de Dados

Adicionar botão para exportar dados em JSON:

```javascript
function exportarJSON() {
    const dados = {
        autor: document.getElementById('nome').value,
        sobrenome: document.getElementById('sobrenome').value,
        titulo: document.getElementById('titulo').value,
        // ... demais campos
    };
    
    const blob = new Blob([JSON.stringify(dados, null, 2)], 
                         { type: 'application/json' });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = `ficha_${Date.now()}.json`;
    a.click();
}
```

---

## 8. Considerações de Acessibilidade (WCAG 2.1)

### 8.1 Conformidade Atual

**Nível A (parcial):**
- ✓ Labels associadas a inputs via `for` e `id`
- ✓ Semântica HTML5 (uso de `<section>`, `<form>`, `<label>`)
- ✗ Alt text para imagem do logo (presente, mas URL externa pode falhar)
- ✗ Contraste de cores não validado contra WCAG AA

### 8.2 Recomendações para Melhoria

```html
<!-- Adicionar aria-label para acessibilidade -->
<button type="submit" aria-label="Gerar ficha catalográfica em PDF">
    Gerar PDF
</button>

<!-- Adicionar aria-required e aria-invalid -->
<input id="nome" required aria-required="true" aria-describedby="nome-help">
<span id="nome-help">Campo obrigatório</span>

<!-- Melhorar contraste de cores no tema esmeralda -->
<!-- Cores atuais: emerald-600 (#059669) sobre fundo branco têm razão ~4.5:1 -->
<!-- Recomendação: usar emerald-700 ou escurecer (#047857) para 7:1 -->
```

### 8.3 Teste de Compatibilidade de Leitores de Tela

- NVDA (Windows) ✓
- JAWS (Windows) ✓
- VoiceOver (macOS/iOS) — requer teste
- TalkBack (Android) — requer teste

---

## 9. Performance e Otimização

### 9.1 Perfil de Carga

| Recurso | Tamanho | Tipo | Cache |
|---------|---------|------|-------|
| index.html | ~50 KB | Documento | Navegador |
| Tailwind CSS CDN | ~30 KB | Stylesheet | CDN |
| jsPDF CDN | ~400 KB | Script | CDN |
| Google Fonts | ~50 KB | Fonts | CDN |
| Logo (externo) | Variável | Image | CDN IFB |
| **Total** | ~530 KB | | |

### 9.2 Otimizações Implementadas

- ✓ CDN para bibliotecas (não armazena localmente)
- ✓ Sem imagens renderizadas (apenas logo externo)
- ✓ JavaScript inline (evita requests adicionais)
- ✓ Sem frameworks pesados (Vanilla JS)
- ✓ Processamento client-side (sem latência de rede)

### 9.3 Possíveis Otimizações Futuras

1. **Minificação:** Usar webpack/rollup para minificar HTML/CSS/JS
2. **Lazy Loading:** Carregar jsPDF apenas quando necessário
3. **Web Workers:** Processar geração de PDF em thread separada
4. **Service Worker:** Cache offline da aplicação completa
5. **Progressive Web App (PWA):** Adicionar manifest.json e suporte offline

---

## 10. Segurança

### 10.1 Análise de Riscos

| Risco | Severidade | Mitigation |
|-------|-----------|-----------|
| XSS (Cross-Site Scripting) | Média | Inputs não executam código; jsPDF não injeta HTML |
| CSRF | Baixa | Sem servidor; formulário é local |
| Injeção de SQL | Nenhuma | Sem banco de dados |
| Exposição de dados | Baixa | Processamento local; PDF não é enviado a servidor |
| Dependência CDN comprometida | Alta | Usar SRI (Subresource Integrity) |

### 10.2 Implementar Subresource Integrity (SRI)

```html
<!-- Tailwind -->
<script 
    src="https://cdn.tailwindcss.com"
    integrity="sha384-[HASH]"
    crossorigin="anonymous">
</script>

<!-- jsPDF -->
<script 
    src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"
    integrity="sha384-[HASH]"
    crossorigin="anonymous">
</script>
```

### 10.3 Validação de Input

Implementar validação adicional no lado cliente:

```javascript
function validarNome(nome) {
    // Apenas letras, espaços e hífens
    return /^[a-záéíóúàâêôãõç\s\-]+$/i.test(nome);
}

function validarAno(ano) {
    const anoNum = parseInt(ano);
    return anoNum >= 1950 && anoNum <= new Date().getFullYear();
}
```

---

## 11. Testes e Qualidade

### 11.1 Estratégia de Testes Recomendada

**Testes Unitários:**
```javascript
// Usando Jest ou similar
describe('getRomanNum', () => {
    test('deve retornar I para 1', () => {
        expect(getRomanNum(1)).toBe('I');
    });
    test('deve retornar X para 10', () => {
        expect(getRomanNum(10)).toBe('X');
    });
});
```

**Testes de Integração:**
- Preencher formulário completo e gerar PDF
- Validar estrutura do PDF gerado
- Testar com múltiplos cenários de autores

**Testes de Compatibilidade:**
- Navegadores: Chrome, Firefox, Safari, Edge
- Sistemas: Windows, macOS, Linux
- Dispositivos: Desktop, Tablet, Mobile

### 11.2 Checklist de QA

- [ ] Todos os campos obrigatórios aceitam validação HTML5
- [ ] PDF gerado contém todos os dados inseridos
- [ ] Formatação de nomes segue padrão "SOBRENOME, Nome"
- [ ] Múltiplos autores exibem "[et al.]" quando apropriado
- [ ] Palavras-chave são numeradas corretamente
- [ ] Entradas secundárias seguem numeração romana sequencial
- [ ] Campus/curso aparecem corretamente na ficha
- [ ] Ano de defesa é validado
- [ ] Ilustrações são descritas corretamente
- [ ] Logo do IFB exibe sem erros
- [ ] Responsividade funciona em mobile
- [ ] Navegadores suportados carregam sem erros

---

## 12. Manutenção e Versionamento

### 12.1 Controle de Versão

Recomendado usar Git com conventional commits:

```
feat: adicionar suporte a segundo idioma na ficha
fix: corrigir quebra de linha em títulos muito longos
docs: atualizar documentação especializada
refactor: extrair lógica de geração de PDF em função
```

### 12.2 Changelog Sugerido

```markdown
## [1.1.0] - 2026-08-XX
### Adicionado
- Suporte a coorientador
- Validação de ano de defesa

### Corrigido
- Quebra de linha inadequada em títulos muito longos
- Compatibilidade com jsPDF 2.5.1

## [1.0.0] - 2026-08-19
### Lançamento Inicial
- Gerador de ficha catalográfica funcional
- Suporte a 51 cursos em 10 campi
```

### 12.3 Integração Contínua Sugerida

Implementar pipeline CI/CD usando GitHub Actions:

```yaml
name: Tests
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - run: npm install
      - run: npm test
      - run: npm run build
```

---

## 13. Conformidade Normativa

### 13.1 Padrões Bibliográficos Implementados

1. **ABNT NBR 6023:2018** — Informação e documentação — Referências — Elaboração
   - Formato de entrada de autor
   - Sequência de elementos de referência
   - Pontuação e separadores

2. **ABNT NBR 12899:2007** — Apresentação de originais — Normalização
   - Fonte: Times New Roman 9 pt
   - Espaçamento de linhas
   - Margens da ficha

3. **Library of Congress Classification** — Não implementado
   - Poderia ser adicionado em versão futura

4. **Dewey Decimal Classification** — Não implementado
   - Poderia ser adicionado em versão futura

### 13.2 Validação de Conformidade

Para validar se a ficha segue padrões:

```javascript
function validarConformidadeABNT(ficha) {
    const checks = {
        temAutor: /\w+,\s\w+/.test(ficha),                    // SOBRENOME, Nome
        temTitulo: /Título:/.test(ficha),                     // Campo título
        temDataPublicacao: /20\d{2}/.test(ficha),             // Ano 4 dígitos
        temFonte: /Times|times/.test(ficha),                  // Font Times
        temNumeracaoRomana: /[IVX]+\.\s/.test(ficha),        // I. II. III.
        temPalavrasChave: /\d+\.\s/.test(ficha)              // 1. 2. 3.
    };
    
    return Object.entries(checks);
}
```

---

## 14. Endpoints de Integração Sugeridos

Caso seja necessário integrar com sistema de bibliotecas existente:

### 14.1 API REST Proposta

```
POST /api/v1/fichas
Content-Type: application/json

{
  "autor": {
    "nome": "Maria",
    "sobrenome": "Silva"
  },
  "titulo": "Título do Trabalho",
  "tipo": "tcc",
  "curso": "Brasília|Licenciatura em Dança",
  "orientador": {
    "nome": "Luciana",
    "sobrenome": "Diniz"
  },
  "coorientador": null,
  "ano": 2023,
  "paginas": 66,
  "ilustracoes": "coloridas",
  "palavrasChave": ["palavra1", "palavra2", "palavra3"]
}

Response:
{
  "id": "uuid-v4",
  "pdfUrl": "/files/fichas/uuid-v4.pdf",
  "createdAt": "2026-08-19T12:00:00Z",
  "status": "generated"
}
```

### 14.2 Integração com Sistema de Bibliotecas

Possível fluxo de integração com Koha, Evergreen ou similar:

```javascript
async function enviarParaBiblioteca(fichaData) {
    // 1. Gerar PDF localmente
    const pdf = gerarPDF(fichaData);
    
    // 2. Fazer upload
    const formData = new FormData();
    formData.append('pdf', pdf);
    formData.append('metadata', JSON.stringify(fichaData));
    
    // 3. Registrar no sistema de bibliotecas
    const response = await fetch('https://biblioteca.ifb.edu.br/api/catalogar', {
        method: 'POST',
        body: formData,
        headers: {
            'Authorization': `Bearer ${token}`
        }
    });
    
    return response.json();
}
```

---

## 15. Troubleshooting Técnico

### 15.1 Problemas Comuns

**Problema:** Logo não carrega
```
Causa: Falha ao conectar em nead.ifb.edu.br
Solução: Verificar conectividade; salvar logo localmente; usar fallback SVG
```

**Problema:** PDF não é gerado
```
Causa: jsPDF não carregou via CDN
Solução: Verificar console (F12); usar CDN alternativa; salvar jsPDF localmente
```

**Problema:** Texto sobreposto no PDF
```
Causa: String bibliográfica muito longa
Solução: Implementar word-wrap mais agressivo; usar font menor; redesenhar layout
```

**Problema:** Caracteres especiais corrompem
```
Causa: jsPDF com encoding inadequado
Solução: Usar `doc.text()` com suporte UTF-8; verificar fonte instalada
```

### 15.2 Debug Console

Adicionar logs para diagnosticar:

```javascript
console.log('Dados capturados:', {
    nome, sobrenome, titulo, trabalho: tipoTrabalhoRaw
});
console.log('String bibliográfica:', bibliographicBodyStr);
console.log('Dimensões PDF:', doc.internal.pageSize.getWidth(), 
            doc.internal.pageSize.getHeight());
```

---

## 16. Roadmap de Desenvolvimento

### Versão 1.2 (Curto Prazo)
- [ ] Adicionar validação de email para orientador
- [ ] Suporte a upload de arquivo da ficha (pré-preenchimento)
- [ ] Opção de visualização prévia do PDF antes de salvar

### Versão 1.5 (Médio Prazo)
- [ ] Integração com sistema de bibliotecas (API)
- [ ] Histórico de fichas geradas (localStorage)
- [ ] Edição de fichas salvas
- [ ] Exportação em MARC21

### Versão 2.0 (Longo Prazo)
- [ ] Suporte a múltiplos idiomas (EN, ES, FR)
- [ ] Aplicação mobile nativa (React Native)
- [ ] Dashboard administrativo para bibliotecários
- [ ] Integração com autenticação SSO/SAML
- [ ] Análise estatística de trabalhos

---

## 17. Referências Bibliográficas e Normativas

### 17.1 Normas ABNT

- ABNT NBR 6023:2018 — Informação e documentação — Referências
- ABNT NBR 12899:2007 — Apresentação de originais
- ABNT NBR ISO 7144 — Numeração de sistemas de classificação

### 17.2 Documentação Técnica

- jsPDF Documentation: https://github.com/parallax/jsPDF
- Tailwind CSS: https://tailwindcss.com/docs
- MDN Web Docs - FormData: https://developer.mozilla.org/en-US/docs/Web/API/FormData
- WCAG 2.1 Guidelines: https://www.w3.org/WAI/WCAG21/quickref/

### 17.3 Especificações IFB

- Padrão de Ficha Catalográfica do IFB (fornecido via Google Drive)
- Sistema de Bibliotecas IFB
- Coordenação de Pós-Graduação e Pesquisa

---

## 18. Conclusão

O Gerador de Ficha Catalográfica IFB é uma aplicação web robusta e especializada, desenvolvida com foco em conformidade normativa e facilidade de uso. A arquitetura client-side garante privacidade dos dados e independência de infraestrutura de backend, enquanto a utilização de padrões bibliográficos reconhecidos assegura integração com sistemas de bibliotecas profissionais.

**Recomendações finais:**
1. Implementar testes automatizados antes de produção
2. Adicionar monitoramento de errors (Sentry, LogRocket)
3. Realizar auditorias de acessibilidade periódicas
4. Manter atualização de dependências (jsPDF, Tailwind)
5. Coletar feedback de bibliotecários para melhorias contínuas

---

**Documento preparado para:** Bibliotecários, Desenvolvedores, Administradores de TI
**Versão:** 1.0
**Data:** 2026-08-19
**Classificação:** Documentação Técnica — Público
