RECALL Advisor - armazenamento local por navegador

Publique a raiz deste projeto no Netlify via Git ou Netlify CLI.
A pasta publicada deve ser public; as Functions estão em netlify/functions.
Configure GEMINI_API_KEY no escopo Functions para Production.

PRIVACIDADE E DADOS
- Navegadores novos começam sem projetos e sem histórico.
- Não existe catálogo pessoal embutido nem importação automática.
- Projetos já salvos na chave recall-mvp-v1 são preservados.
- Não há conta, banco de dados ou sincronização entre aparelhos.
- Exportar/importar backup permite transferência manual. Importar substitui os dados atuais.
- Limpar os dados do site pode apagar os projetos. Mantenha backups.
- Quem usa o mesmo perfil de navegador e endereço compartilha os mesmos dados locais.
- O Advisor envia a pergunta e até dez projetos à Function e ao Gemini quando solicitado.
- Não publique backups pessoais na pasta public ou em repositórios compartilhados.

ATUALIZAÇÃO
Substitua os arquivos publicados por esta versão. Dispositivos que já receberam
o catálogo anterior conservam os dados locais; a atualização não os apaga.
A remoção do código não retira cópias já baixadas de versões anteriores.

SEGURANÇA — atualização de 26/10/2026
- O Advisor permanece público: não exige conta ou código de acesso.
- A Function declara rateLimit nativo do Netlify: 5 requisições por 60 segundos,
  por combinação de IP e domínio, no caminho /.netlify/functions/advisor.
- Esse limite é aplicado pelo Netlify, não por um contador em memória da Function.
  Não é simulado pelos testes locais: confirme a regra no resumo do deploy.
- A aplicação do bloqueio pode demorar até 10 segundos. IPs diferentes possuem
  limites separados. Portanto, isso NÃO representa teto global de gastos.
- Configure também as cotas disponíveis para o modelo no projeto Google/Gemini
  e acompanhe o consumo. Alertas de orçamento não devem ser tratados como bloqueio.
- Origin ausente, nulo, malformado ou diferente da origem da requisição é recusado
  com 403. Isso restringe uso por navegadores, mas NÃO autentica scripts: um cliente
  externo pode copiar esse cabeçalho. A proteção contra volume vem do rateLimit.
- O corpo exige application/json e aceita no máximo 25.000 bytes, pergunta de
  até 1.000 caracteres e até 10 objetos de projeto.
- Erros do provedor não repassam mensagens internas ou credenciais ao navegador.
- A interface trata HTTP 429 mesmo quando o Netlify não retorna JSON.
- Nenhuma chave foi incluída no HTML. Mantenha GEMINI_API_KEY somente no ambiente
  de Functions do Netlify; o nome do modelo existente foi preservado.

PUBLICAÇÃO E CONFERÊNCIA
1. Publique a raiz deste projeto via Git conectado ao Netlify ou Netlify CLI.
   Não envie apenas public por arrastar e soltar: a API precisa ser implantada
   junto com netlify/functions/advisor.mjs.
2. Mantenha publish=public e Functions=netlify/functions conforme netlify.toml.
3. Verifique no resumo/logs do deploy se a regra de rate limiting foi reconhecida.
4. Teste uma pergunta normal no site e confirme o retorno da IA.
5. Repita o teste com Origin: null: deve retornar 403 sem stack trace.
6. Para validar o limite sem consumir Gemini, envie somente corpos {} inválidos
   ao endpoint com Origin igual ao domínio publicado. Eles devem retornar 400;
   depois de ultrapassar o limite e aguardar a propagação, deve ocorrer 429.
   Não transforme essa conferência em teste de carga.

TESTES LOCAIS (Node.js 20 ou superior, sem dependências adicionais)
  node --test tests/advisor.test.mjs tests/advisor-ui.test.mjs
O provedor Gemini é simulado: esses testes não enviam dados nem geram consumo.
Eles verificam a Function e o tratamento de 429 na interface, mas não comprovam
a implantação, a chave Gemini nem a aplicação do limite na infraestrutura Netlify.

Referência: https://docs.netlify.com/manage/security/secure-access-to-sites/rate-limiting/

SEGURANÇA DOS BACKUPS — segunda atualização de 26/10/2026
- Importação valida a estrutura completa antes de substituir os dados atuais.
  Objetos nulos, IDs inválidos/repetidos e histórico malformado são recusados.
- Limites: 2 MB por arquivo, 2.000 projetos, 10.000 registros de histórico.
  Campos de texto acima dos limites são recusados na importação: nome 120,
  descrição/contexto 20.000, próximo passo 10.000, histórico 50.000 caracteres.
- Prioridade, impacto e esforço são números inteiros de 1 a 5. Valores fora da
  faixa são ajustados; tipos inválidos recebem 3. Datas inválidas são removidas.
- Links permitem somente HTTP/HTTPS absoluto, sem usuário/senha; outros são
  removidos. A renderização repete essa validação e protege os campos numéricos.
- Dados do localStorage também são normalizados ao abrir: registros inválidos
  ou repetidos são ignorados e textos excessivos são limitados em memória.
  A abertura não grava automaticamente por cima do armazenamento existente.
  Uma edição posterior salva os dados normalizados. Mantenha seu backup original.
- A interface e os dados válidos continuam locais, sem login ou banco de dados.
- Para executar toda a suíte:
  node --test tests/advisor.test.mjs tests/advisor-ui.test.mjs tests/backup-security.test.mjs
- Testes de segurança usam o código real de importação/renderização em ambiente
  isolado; a API externa é simulada. Publicar e retestar o site continua necessário.
