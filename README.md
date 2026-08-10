# SheetBase — banco de dados com Google Planilhas

![Status](https://img.shields.io/badge/status-prova%20de%20conceito-116149)
![Front-end](https://img.shields.io/badge/front--end-HTML%20%7C%20CSS%20%7C%20JavaScript-16212b)
![Back-end](https://img.shields.io/badge/back--end-Google%20Apps%20Script-4285F4)
![Dados](https://img.shields.io/badge/dados-Google%20Sheets%20%2B%20Drive-34A853)

## Sobre o projeto

O **SheetBase** é uma prova de conceito que utiliza uma planilha do Google como um banco de dados simples para uma aplicação web. A interface permite cadastrar, listar, atualizar e pesquisar registros. Quando há um anexo, o arquivo é enviado ao Google Drive e o endereço para acessá-lo é armazenado junto aos demais dados na planilha.

O objetivo do teste é validar uma arquitetura de baixo custo e fácil manutenção para projetos pequenos, protótipos, formulários internos e demonstrações que ainda não precisam de um banco de dados tradicional.

> Este projeto tem finalidade experimental e educacional. Google Sheets não substitui um banco relacional em aplicações com alto volume, consultas complexas, dados sensíveis ou muitos acessos simultâneos.

## Proposta do teste

A proposta é verificar se os serviços do Google podem funcionar como uma camada de persistência acessível para um front-end feito apenas com HTML, CSS e JavaScript:

- o **Google Sheets** organiza os registros em linhas e colunas;
- o **Google Drive** armazena os arquivos anexados;
- o **Google Apps Script** recebe as requisições, valida os dados e conecta a interface aos serviços do Google;
- o arquivo **`index.html`** apresenta os dados e oferece o formulário de cadastro;
- a implantação do Apps Script como **Aplicativo da Web** disponibiliza os endpoints usados pelo navegador.

Com isso, o teste cobre o fluxo completo de uma operação de cadastro: preencher o formulário, enviar os dados, salvar um possível anexo, registrar as informações na planilha e devolver o resultado para a interface.

## Funcionalidades testadas

- listagem dos registros armazenados na planilha;
- pesquisa local em todos os campos exibidos;
- atualização manual da tabela;
- cadastro de nome, categoria e descrição;
- envio opcional de documentos, planilhas ou imagens;
- limite de 10 MB por arquivo no front-end;
- armazenamento de anexos no Google Drive;
- exibição do link do arquivo salvo;
- mensagens de sucesso ou falha para o usuário;
- layout responsivo para computadores e dispositivos móveis;
- tratamento básico de conteúdo antes de inseri-lo na tabela HTML.

## Arquitetura

```text
┌───────────────────────────┐
│ Navegador                 │
│ index.html + CSS + JS     │
└─────────────┬─────────────┘
              │ GET / POST (JSON)
              ▼
┌───────────────────────────┐
│ Google Apps Script        │
│ Web App / camada de API   │
└──────────┬─────────┬──────┘
           │         │
           ▼         ▼
┌────────────────┐  ┌────────────────┐
│ Google Sheets  │  │ Google Drive   │
│ dados tabulares│  │ arquivos       │
└────────────────┘  └────────────────┘
```

### Fluxo de leitura

1. A página envia uma requisição `GET` para a URL publicada pelo Apps Script.
2. O Apps Script lê as linhas da planilha.
3. A API devolve os registros em JSON.
4. O JavaScript transforma a resposta em linhas da tabela.
5. A busca filtra, no próprio navegador, os registros já carregados.

### Fluxo de gravação

1. O usuário preenche o formulário e escolhe um arquivo opcional.
2. O navegador converte o arquivo para Base64 e envia uma requisição `POST`.
3. O Apps Script decodifica o anexo e cria o arquivo no Google Drive.
4. Os dados e a URL do arquivo são adicionados como uma nova linha da planilha.
5. A API responde com o resultado da operação.
6. A interface atualiza a tabela e apresenta uma notificação.

## Tecnologias utilizadas

| Tecnologia | Responsabilidade |
| --- | --- |
| HTML5 | estrutura da página e formulário |
| CSS3 | identidade visual e responsividade |
| JavaScript | busca, renderização e comunicação com a API |
| Google Apps Script | API e regras de integração |
| Google Sheets | persistência dos dados tabulares |
| Google Drive | armazenamento dos anexos |

## Capturas de tela

### Tela principal

<p align="center">
  <img src="fotos do projeto/site.png" width="900">
</p>

### Dados no Google Sheets

<p align="center">
  <img src="fotos do projeto/planilha.png" width="900">
</p>

### Arquivos no Google Drive

<p align="center">
  <img src="fotos do projeto/drive.png" width="900">
</p>

## Como executar o front-end

### Pré-requisitos

- uma Conta Google;
- uma planilha no Google Sheets;
- uma pasta no Google Drive para os anexos;
- um projeto no Google Apps Script implantado como Aplicativo da Web;
- um navegador moderno;
- opcionalmente, um servidor local como a extensão **Live Server** do VS Code.

### Execução

1. Clone este repositório:

   ```bash
   git clone URL_DO_SEU_REPOSITORIO
   cd NOME_DO_REPOSITORIO
   ```

2. Abra o arquivo `index.html` no navegador ou inicie um servidor local.

3. No `script.js`, localize a constante abaixo e informe a URL da implantação do seu Apps Script:

   ```javascript
   const BACKEND_URL = 'URL_DA_SUA_WEB_APP';
   ```

4. Atualize a página e verifique se os registros da planilha aparecem na tabela.

> Depois de alterar o código do Apps Script, crie uma nova versão da implantação para que as mudanças cheguem à Web App.

## Preparação da planilha

Crie uma aba para os registros e adicione os cabeçalhos na primeira linha. O front-end reconhece nomes em português ou inglês, mas esta organização é recomendada:

| Nome | Categoria | Descrição | Arquivo | Nome do arquivo | Data |
| --- | --- | --- | --- | --- | --- |
| Projeto Aurora | Documento | Registro de exemplo | URL_DO_ARQUIVO | exemplo.pdf | 09/08/2026 |

O Apps Script deve usar sempre a mesma ordem ao gravar novas linhas. Também é recomendável guardar o ID da planilha, o nome da aba e o ID da pasta do Drive em propriedades do script, evitando espalhar configurações pelo código.

## Contrato esperado da API

O front-end atual espera uma API simples em JSON.

### `GET` — listar registros

Exemplo de resposta:

```json
{
  "ok": true,
  "records": [
    {
      "Nome": "Projeto Aurora",
      "Categoria": "Documento",
      "Descrição": "Registro de exemplo",
      "Arquivo": "https://drive.google.com/...",
      "Nome do arquivo": "exemplo.pdf",
      "Data": "09/08/2026"
    }
  ]
}
```

### `POST` — criar registro

O corpo enviado pelo navegador usa `Content-Type: text/plain` e contém um JSON com a seguinte estrutura:

```json
{
  "name": "Projeto Aurora",
  "category": "Documento",
  "description": "Registro de exemplo",
  "date": "09/08/2026",
  "fileName": "exemplo.pdf",
  "fileType": "application/pdf",
  "fileBase64": "CONTEUDO_DO_ARQUIVO_EM_BASE64"
}
```

Resposta esperada quando a operação termina corretamente:

```json
{
  "ok": true,
  "fileUrl": "https://drive.google.com/..."
}
```

Em caso de erro, a API deve responder com `ok: false` e, preferencialmente, uma mensagem que não exponha IDs, credenciais ou detalhes internos.

## Código do Google Apps Script

O código do back-end não está incluído no arquivo `index.html`. Use a área abaixo para publicar a versão do Apps Script associada ao projeto. Antes de postar, substitua IDs reais por valores de exemplo ou carregue-os por meio de `PropertiesService`.

<details>
<summary><strong>Clique para exibir o espaço reservado ao Apps Script</strong></summary>

const SPREADSHEET_ID = 'id da planilha';
const FOLDER_ID = 'id da pasta do drive';

function doGet() {
  const sheet = SpreadsheetApp.openById(SPREADSHEET_ID).getSheetByName('Registros');
  const values = sheet.getDataRange().getDisplayValues();
  const headers = values.shift();
  const records = values.map(row => {
    const record = Object.fromEntries(headers.map((h, i) => [h.trim(), row[i]]));

    // Recupera o nome real diretamente do Drive. Isso também corrige registros
    // antigos que possuem File ID, mas não possuem "Nome do arquivo" preenchido.
    if (!record['Nome do arquivo'] && record['File ID']) {
      try {
        record['Nome do arquivo'] = DriveApp.getFileById(record['File ID']).getName();
      } catch (error) {
        record['Nome do arquivo'] = 'Arquivo';
      }
    }

    return record;
  });

  return json({ ok: true, records });
}

function doPost(e) {
  const p = JSON.parse(e.postData.contents);
  const sheet = SpreadsheetApp.openById(SPREADSHEET_ID).getSheetByName('Registros');
  let fileUrl = '', fileId = '', savedFileName = '';

  if (p.fileBase64 && p.fileName) {
    const blob = Utilities.newBlob(Utilities.base64Decode(p.fileBase64), p.fileType, p.fileName);
    const file = DriveApp.getFolderById(FOLDER_ID).createFile(blob);
    file.setSharing(DriveApp.Access.ANYONE_WITH_LINK, DriveApp.Permission.VIEW);
    fileUrl = file.getUrl();
    fileId = file.getId();
    savedFileName = file.getName();
  }
  sheet.appendRow([p.name || '', p.category || '', p.description || '', fileUrl, new Date(), fileId, savedFileName]);
  return json({ ok: true, fileUrl, fileId, fileName: savedFileName });
}

function json(data) {
  return ContentService.createTextOutput(JSON.stringify(data)).setMimeType(ContentService.MimeType.JSON);
}

</details>

Outra opção é criar um arquivo `apps-script/Code.gs` no repositório e trocar a área acima por um link para esse arquivo. Essa abordagem facilita a manutenção quando o código crescer.

## Publicação do Apps Script

1. Abra a planilha e acesse **Extensões → Apps Script**.
2. Adicione o código responsável pelas funções `doGet` e `doPost`.
3. Configure os IDs da planilha, da aba e da pasta de anexos.
4. Clique em **Implantar → Nova implantação**.
5. Selecione o tipo **Aplicativo da Web**.
6. Defina quem executará o aplicativo e quem terá acesso, conforme a finalidade do teste.
7. Autorize o acesso ao Sheets e ao Drive.
8. Copie a URL terminada em `/exec` e informe-a na constante `BACKEND_URL` do `script.js`.

## Validações sugeridas

Para confirmar a proposta, teste pelo menos os seguintes cenários:

- carregamento de uma planilha vazia;
- cadastro com todos os campos preenchidos;
- cadastro sem arquivo;
- tentativa de envio de arquivo acima de 10 MB;
- pesquisa por nome, categoria, descrição e data;
- abertura do anexo salvo no Drive;
- atualização da página e persistência do registro;
- indisponibilidade temporária da API;
- nomes de arquivo e textos com acentos e caracteres especiais;
- acesso em telas pequenas.

## Limitações da prova de conceito

- não há autenticação própria na interface;
- as permissões dependem da configuração da implantação e do Google Drive;
- a conversão para Base64 aumenta o tamanho transferido do arquivo;
- o limite informado no front-end não substitui uma validação no back-end;
- o Google Apps Script possui cotas e limites de execução;
- operações simultâneas podem exigir `LockService` para evitar conflitos;
- a busca ocorre no navegador, depois que os dados são carregados;
- não há paginação, edição ou exclusão de registros nesta versão;
- uma URL pública do Apps Script pode ser chamada fora da interface se não houver controles adicionais.

## Segurança e privacidade

Antes de usar a ideia fora de um ambiente de testes:

- valide no Apps Script todos os campos recebidos;
- limite também no servidor o tamanho e os tipos de arquivo aceitos;
- aplique autenticação e autorização de acordo com o público da aplicação;
- defina cuidadosamente as permissões dos arquivos criados no Drive;
- não publique IDs privados, tokens, dados pessoais ou arquivos reais no GitHub;
- evite retornar mensagens internas detalhadas em caso de erro;
- trate fórmulas iniciadas por `=`, `+`, `-` ou `@` antes de gravar dados não confiáveis na planilha;
- use `LockService` se houver possibilidade de gravações simultâneas;
- revise as cotas atuais dos serviços do Google antes de disponibilizar a solução.

## Próximas melhorias

- mover a configuração da API para uma camada apropriada de ambiente;
- implementar autenticação;
- adicionar paginação;
- permitir edição e exclusão de registros;
- exibir progresso durante o upload;
- criar validações equivalentes no front-end e no Apps Script;
- adicionar testes automatizados;
- apresentar mensagens de erro retornadas pela API;
- migrar para um banco de dados dedicado caso o volume ou a complexidade aumentem.

## Conclusão

O SheetBase demonstra que Google Sheets, Drive e Apps Script podem formar um back-end enxuto para validar uma ideia rapidamente. A solução favorece visibilidade dos dados e baixo custo inicial, enquanto o front-end permanece independente e utiliza uma API HTTP simples.

O principal resultado esperado não é substituir bancos de dados convencionais, mas entender até onde essa composição atende um protótipo e identificar os limites que justificariam uma arquitetura mais robusta.
