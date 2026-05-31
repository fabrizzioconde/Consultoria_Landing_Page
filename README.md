# Landing Page — Consultoria em Modelos Matemáticos

Landing page de captação de leads para consultoria em construção de modelos matemáticos para tomada de decisão empresarial.

## Funcionalidades

- Formulário com 8 campos de qualificação do lead
- Integração automática com Google Sheets via Google Apps Script
- Design responsivo (desktop e mobile)
- Estilo Data Science: fundo escuro + acentos verde/azul
- Validação nativa de campos obrigatórios
- Confirmação visual após envio

## Campos capturados

| Campo | Obrigatório |
|---|---|
| Nome completo | Sim |
| E-mail | Sim |
| Empresa | Sim |
| Cargo | Sim |
| Porte da empresa | Sim |
| Área de atuação | Sim |
| Descrição do problema/necessidade | Sim |
| Orçamento estimado | Não |

## Como configurar

### 1. Criar a planilha no Google Sheets

1. Acesse [sheets.google.com](https://sheets.google.com) e crie uma nova planilha
2. Renomeie a aba para **Leads**
3. Na linha 1, adicione os cabeçalhos nesta ordem:

```
A1: Timestamp | B1: Nome | C1: Email | D1: Empresa | E1: Cargo
F1: Porte | G1: Área | H1: Problema | I1: Orçamento
```

### 2. Criar o Google Apps Script

1. Na planilha, clique em **Extensões → Apps Script**
2. Apague o código existente e cole o script abaixo:

```javascript
function doPost(e) {
  try {
    var data = JSON.parse(e.postData.contents);
    var sheet = SpreadsheetApp
                  .getActiveSpreadsheet()
                  .getSheetByName('Leads') || SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();

    sheet.appendRow([
      new Date().toLocaleString('pt-BR', { timeZone: 'America/Sao_Paulo' }),
      data.nome     || '',
      data.email    || '',
      data.empresa  || '',
      data.cargo    || '',
      data.porte    || '',
      data.area     || '',
      data.problema || '',
      data.orcamento || 'Não informado'
    ]);

    return ContentService
      .createTextOutput(JSON.stringify({ status: 'success' }))
      .setMimeType(ContentService.MimeType.JSON);

  } catch (err) {
    return ContentService
      .createTextOutput(JSON.stringify({ status: 'error', message: err.toString() }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}
```

### 3. Publicar como Web App

1. Clique em **Implantar → Nova implantação**
2. Tipo: **Aplicativo da web**
3. Executar como: **Eu mesmo**
4. Quem tem acesso: **Qualquer pessoa**
5. Clique em **Implantar** e autorize as permissões
6. Copie a URL gerada

### 4. Configurar a página

No arquivo `index.html`, localize e substitua:

```javascript
const SCRIPT_URL = 'SEU_SCRIPT_URL_AQUI';
```

pela URL copiada no passo anterior.

### 5. Publicar

```bash
git add index.html
git commit -m "config: adicionar URL do Google Apps Script"
git push
```

## Deploy no GitHub Pages

1. Vá em **Settings → Pages**
2. Source: `Deploy from a branch`
3. Branch: `master` / `/ (root)`
4. Salve — em 1-2 minutos a página estará em:

```
https://fabrizzioconde.github.io/Consultoria_Landing_Page/
```

> **Atenção:** ao editar o Apps Script, sempre crie uma **nova versão** em Implantar → Gerenciar implantações antes de reimplantar.

## Estrutura

```
.
└── index.html   # Página completa (HTML + CSS + JS em arquivo único)
```

## Tecnologias

- HTML5 / CSS3 / JavaScript vanilla
- Google Apps Script (backend serverless)
- Google Sheets (banco de dados dos leads)
- Google Fonts (Inter)
