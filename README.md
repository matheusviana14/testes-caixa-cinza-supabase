# Introdução

- **Aluno:** Gabriel Matheus Ferreira Viana (RA 252088)
- **Curso:** Análise e Desenvolvimento de Sistemas, Unifacens
- **Disciplina:** UX/UI e Testes de Software
- **Atividade:** Testes de caixa cinza em API de autenticação

Este repositório documenta a configuração e a execução de testes funcionais de autenticação na API do **Supabase**, usando o **Postman Desktop**. Em um teste de **caixa cinza**, o testador conhece parte da estrutura do sistema (endpoint, headers, formato do body e respostas HTTP), mas não o código interno.

# Objetivo da Atividade

Aplicar testes de caixa cinza em uma API de autenticação, executando requisições HTTP, validando status e respostas JSON, e documentando todo o processo.

# Configuração do Supabase

**Como o projeto foi criado:** criei uma conta no Supabase, uma organização (`matheusviana14`, plano Free) e o projeto `teste-auth-matheus`, na região South America (São Paulo).

**Tipo de autenticação:** e-mail e senha. O provider **Email** está habilitado e a opção **Confirm email** foi desativada, para que o usuário de teste consiga logar sem confirmação por e-mail.

**Como o usuário foi criado:** em *Authentication → Users → Add user → Create new user*, com a opção *Auto Confirm User* marcada.

**Objetivo da configuração:** ter um ambiente de autenticação real, com um usuário válido, para testar cenários de sucesso e de erro.

**Credenciais obtidas:** Project URL (`base_url`) e Publishable key (`api_key`). A `secret key` não foi utilizada nos testes nem copiada para o repositório (no print das API Keys ela aparece mascarada).

**Identificação do aluno no ambiente:** o projeto pertence à minha conta no Supabase (papel *Owner*, print 06) e o Workspace do Postman está no meu nome (print 07 do Postman). A organização usa o apelido `matheusviana14`.

### Informações registradas para os testes

| Item | Registro |
|---|---|
| Nome do projeto | `teste-auth-matheus` |
| Região | South America (São Paulo), `sa-east-1` |
| Tipo de autenticação | E-mail e senha (provider Email) |
| API URL | `https://<project-id>.supabase.co` (valor completo no print 01) |
| API KEY | Publishable key `sb_publishable_...` (print 05) |
| Usuário de teste | criado manualmente no painel, com e-mail confirmado automaticamente (print 04) |
| Senha do usuário | definida por mim, usada apenas via variável `{{password}}` |

### Evidências

| Evidência | Print |
|---|---|
| Projeto criado | ![Projeto criado](prints/01-projeto-criado.png) |
| Painel do Supabase | ![Painel](prints/02-painel-supabase.png) |
| Autenticação habilitada | ![Auth habilitada](prints/03-auth-habilitada.png) |
| Usuário criado | ![Usuário criado](prints/04-usuario-criado.png) |
| Credenciais da API | ![Credenciais](prints/05-credenciais-api.png) |
| Configuração do projeto e identificação do aluno (conta Owner) | ![Config do projeto](prints/06-config-projeto.png) |

# Configuração do Postman

**Workspace:** criei o workspace `Testes Auth Supabase - Matheus` (modo Blank).

**Environment:** criei o environment `Supabase Auth` e o selecionei como ativo.

| Variável | Descrição |
|---|---|
| `base_url` | URL do projeto Supabase |
| `api_key` | Publishable key do projeto |
| `email` | E-mail do usuário de teste |
| `password` | Senha do usuário de teste |

**Finalidade das variáveis:** evitar repetir valores em cada requisição e permitir trocar de projeto ou usuário alterando só o environment. Nas requisições, elas são usadas como `{{base_url}}`, `{{api_key}}`, `{{email}}` e `{{password}}`.

**Uso nos testes:** uma coleção (`Auth Supabase`) com uma requisição por cenário, todas usando o environment ativo.

### Evidências

| Evidência | Print |
|---|---|
| Workspace criado | ![Workspace](prints/07-workspace.png) |
| Environment criado | ![Environment](prints/08-environment.png) |
| Variáveis configuradas | ![Variáveis](prints/09-variaveis.png) |
| Organização do Postman (coleção `Auth Supabase` com as 5 requisições e environment ativo) | ![Organização](prints/18-credenciais-invalidas.png) |

# Configuração das Requisições

- **Endpoint:** `{{base_url}}/auth/v1/token?grant_type=password`
- **Método HTTP:** `POST`
- **Finalidade:** autenticar um usuário com e-mail e senha e obter um `access_token` (JWT).

### Headers

| Header | Valor |
|---|---|
| `apikey` | `XXXXX` (valor de `{{api_key}}`) |
| `Authorization` | `Bearer XXXXX` (valor de `{{api_key}}`) |
| `Content-Type` | `application/json` |

### Body JSON

```json
{
  "email": "usuario@email.com",
  "password": "123456"
}
```

Na requisição real, o body usa as variáveis `{{email}}` e `{{password}}`.

### Evidências

| Evidência | Print |
|---|---|
| Endpoint configurado | ![Endpoint](prints/10-endpoint.png) |
| Headers | ![Headers](prints/11-headers.png) |
| Body JSON | ![Body](prints/12-body.png) |
| Requisição criada | ![Requisição](prints/13-requisicao-criada.png) |

# Execução dos Testes

Foram executados os 5 cenários obrigatórios, cada um em uma requisição da coleção `Auth Supabase`.

| Cenário | Entrada Utilizada | Resultado Esperado | Resultado Obtido | Status |
|---|---|---|---|---|
| Login válido | E-mail e senha corretos | Status 200 + access token | 200 OK, `access_token` retornado | OK |
| Senha incorreta | E-mail válido + senha `senhaerrada123` | Erro de autenticação | 400 Bad Request, `invalid_credentials` | OK |
| Usuário inexistente | `naoexiste@teste.com` + senha válida | Acesso negado | 400 Bad Request, `invalid_credentials` | OK |
| Campos vazios | `email: ""` e `password: ""` | Erro de validação | 400 Bad Request, `validation_failed`, "missing email or phone" | OK |
| Credenciais inválidas | `email: "abc"` e `password: "123"` | Mensagem de erro | 400 Bad Request, `invalid_credentials`, "Invalid login credentials" | OK |

**Validações feitas em cada teste:** status HTTP, corpo JSON, mensagem de erro, presença (ou ausência) do `access_token` e comportamento geral da autenticação.

**Erros encontrados:** nenhum erro de funcionamento. Observações de comportamento:

- Senha incorreta, usuário inexistente e e-mail malformado retornam a mesma resposta (`invalid_credentials`).
- Só os campos vazios geram `validation_failed`.

### Evidências

| Teste | Print |
|---|---|
| Login válido (200 + token) | ![Login válido](prints/14-login-valido.png) |
| Senha incorreta | ![Senha incorreta](prints/15-senha-incorreta.png) |
| Usuário inexistente | ![Usuário inexistente](prints/16-usuario-inexistente.png) |
| Campos vazios | ![Campos vazios](prints/17-campos-vazios.png) |
| Credenciais inválidas | ![Credenciais inválidas](prints/18-credenciais-invalidas.png) |
| Último login registrado no Supabase | ![Último login](prints/04b-usuario-ultimo-login.png) |

# Registro dos Testes

Os testes foram registrados na planilha [`planilha/testes-caixa-cinza.xlsx`](planilha/testes-caixa-cinza.xlsx), com as colunas: ID do teste, Cenário, Entrada, Resultado esperado, Resultado obtido, Status e Observações.

**Importância da documentação:** registrar entrada, resultado esperado e resultado obtido torna os testes reproduzíveis, permite comparar execuções futuras e deixa evidente se o comportamento da API mudou.

**Falhas identificadas:** nenhuma falha de funcionamento. Todos os cenários se comportaram como esperado. Foram anotadas na planilha as observações sobre a mensagem genérica de erro.

### Exemplos dos registros realizados

| ID | Cenário | Entrada | Resultado esperado | Resultado obtido | Status | Observações |
|---|---|---|---|---|---|---|
| CT01 | Login válido | E-mail e senha corretos | Status 200 + access token | 200 OK, `access_token` retornado | Sucesso | Token expira em 3600 s |
| CT02 | Senha incorreta | E-mail válido + senha `senhaerrada123` | Erro de autenticação | 400 Bad Request, `invalid_credentials` | Sucesso | Nenhum token devolvido |
| CT03 | Usuário inexistente | `naoexiste@teste.com` + senha válida | Acesso negado | 400 Bad Request, `invalid_credentials` | Sucesso | Mesma mensagem da senha incorreta (evita enumeração de usuários) |
| CT04 | Campos vazios | `email: ""` e `password: ""` | Erro de validação | 400 Bad Request, `validation_failed`, "missing email or phone" | Sucesso | Validação ocorre antes da checagem de credenciais |
| CT05 | Credenciais inválidas | `email: "abc"` e `password: "123"` | Mensagem de erro | 400 Bad Request, `invalid_credentials`, "Invalid login credentials" | Sucesso | E-mail em formato inválido não gerou erro de validação; a API respondeu com a mensagem genérica |

### Print da planilha preenchida

![Planilha preenchida](prints/19-planilha.png)

# Resultados Obtidos

- **5 de 5 cenários** executados, todos com status **OK**.
- O login válido retornou **200 OK** com `access_token`, `refresh_token`, `expires_in: 3600` e os dados do usuário.
- Os 4 cenários inválidos retornaram **400 Bad Request**, sem nenhum token.
- A API diferencia erro de **validação** (campos vazios) de erro de **credenciais** (demais casos).
- A coleção e o environment exportados estão na pasta [`postman/`](postman/). O environment foi exportado com os valores em branco de propósito, para não publicar chave e senha no repositório. Para reproduzir, basta importar os dois arquivos e preencher `base_url`, `api_key`, `email` e `password`.

# Conclusão

**Os testes foram executados corretamente?** Sim. Os 5 cenários obrigatórios foram executados por mim, no Postman, com evidências reais.

**A autenticação funcionou?** Sim. O login válido retornou 200 e um `access_token`, e todas as tentativas inválidas foram rejeitadas com 400.

**Dificuldades encontradas:** localizar a opção "Confirm email" no painel do Supabase, que fica em *Sign In / Providers*, e não dentro do provider Email; entender a diferença entre as chaves novas (publishable/secret) e as antigas; e adaptar comandos para o shell fish no Linux.

**Falhas identificadas:** nenhuma falha de funcionamento. Apenas um ponto de atenção: o e-mail malformado (`abc`) não gerou erro de validação, e sim a mensagem genérica de credenciais inválidas.

**Melhorias que poderiam ser implementadas:** mensagens de erro mais específicas para formato de e-mail inválido; testes automatizados no Postman (scripts de assert para status e campos); cenários adicionais (e-mail em maiúsculas, senha muito longa, limite de tentativas, token expirado, refresh token); e uso de variáveis para o token nos testes seguintes.

**Importância dos testes de caixa cinza em APIs:** conhecendo endpoint, headers e formato das respostas, o testador consegue validar o contrato e a segurança da API (como a resposta genérica que evita enumeração de usuários) sem precisar do código-fonte, o que torna os testes mais realistas e direcionados que os de caixa preta.
