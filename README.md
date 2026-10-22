# RECALL Advisor 1.1

![Capa ilustrada do RECALL Advisor: retome de onde parou](recall-advisor-cover.png)

**Menos recomeços. Mais progresso.**

Um gerenciador pessoal de projetos para lembrar onde você parou, registrar avanços e escolher uma próxima ação. O RECALL combina uma sugestão diária por critérios locais com um Advisor que usa IA para ajudar na priorização.

> Projeto em estágio de MVP. A capa acima é uma ilustração conceitual gerada com IA, não uma captura da interface.

## Recursos

- Cadastro e edição de projetos com descrição, contexto e próximo passo.
- Estados de projeto: ativo, pausado e concluído.
- Prioridade, impacto, esforço e prazo para apoiar decisões.
- Registro de progresso e histórico de atividades.
- Sugestão diária calculada no navegador para projetos ativos.
- Advisor em botão flutuante: clique para abrir, feche pelo **×** ou pela tecla **Esc**. A pergunta e a resposta permanecem ao reabrir durante a mesma página.
- Exportação e importação de backup em JSON.
- Layout com adaptação para telas menores.

## Como funciona

1. Cadastre um projeto e defina uma próxima ação pequena e concreta.
2. Consulte a sugestão do dia ou abra o Advisor para pedir ajuda.
3. Registre o progresso e o contexto necessário para retomar depois.
4. Exporte backups para preservar seus dados ou transferi-los para outro aparelho.

A sugestão diária usa uma regra local que considera impacto, prioridade, prazo, tempo sem atividade e esforço. O Advisor seleciona até dez projetos não concluídos e envia esse contexto ao Gemini quando você solicita uma análise. Os dois caminhos atualmente usam critérios de seleção diferentes.

## Armazenamento e privacidade

Os projetos e o histórico ficam no **localStorage do navegador**, na chave `recall-mvp-v1`. Não há banco de dados, cadastro de contas ou sincronização automática.

- Um navegador novo começa vazio. O catálogo pessoal do autor não é incluído na página.
- Dados locais existentes são preservados ao atualizar a página no mesmo endereço.
- Outro aparelho, navegador, perfil ou endereço do site pode ter um armazenamento diferente.
- Pessoas que usam o mesmo perfil de navegador no mesmo endereço compartilham os dados locais. Não existe isolamento por conta ou senha.
- Limpar os dados do site pode apagar seus projetos. A navegação privada também não é um armazenamento durável.
- A importação de backup **substitui** os projetos e o histórico atuais após confirmação.

**Armazenamento local não significa processamento exclusivamente local:** ao clicar em **Analisar**, a pergunta e até dez projetos são enviados à função de servidor e ao serviço Gemini. A chave da API é configurada no servidor e não deve ser inserida no HTML. O código da função não grava os projetos em um banco de dados; o tratamento de dados pelo provedor de IA segue as condições desse serviço.

## Tecnologias

- HTML, CSS e JavaScript sem framework no frontend.
- localStorage para persistência no navegador.
- Netlify Functions para o endpoint do Advisor.
- API do Google Gemini para gerar as recomendações com IA.

## Estrutura

```text
RECALL-Advisor/
├── public/
│   └── index.html
├── netlify/
│   └── functions/
│       └── advisor.mjs
├── docs/
│   └── images/
│       └── recall-advisor-cover.png
├── netlify.toml
├── README.md
├── README.txt
└── .gitignore
```

Backups pessoais eventualmente presentes na pasta local não fazem parte da estrutura para publicação.

## Executar e publicar

### Interface local

Abra `public/index.html` no navegador para explorar a interface. Para um endereço local estável, sirva a pasta `public` com um servidor HTTP de sua preferência. Mantenha o mesmo endereço ao retornar para acessar o mesmo armazenamento.

Um servidor estático ou a abertura direta do HTML **não executa a função do Advisor**. Nesses ambientes, a análise remota pode falhar e a interface oferece uma sugestão local quando há projetos disponíveis. Para testar também a função, use um ambiente compatível com Netlify Functions.

### Netlify

1. Importe o projeto por um repositório Git ou publique pela Netlify CLI com suporte às Functions. Preserve a raiz do projeto.
2. Use as configurações de `netlify.toml`: diretório publicado `public` e funções em `netlify/functions`.
3. Cadastre a variável `GEMINI_API_KEY` no ambiente das Functions para o contexto de publicação utilizado.
4. Faça um novo deploy após configurar a variável.
5. Crie um projeto de teste e valide o Advisor no site publicado.

Publicar apenas os arquivos estáticos não disponibiliza o endpoint `/.netlify/functions/advisor`. Nunca coloque a chave da API no frontend, no README ou em arquivos versionados.

## Limitações atuais

- Sem sincronização entre dispositivos ou recuperação automática de dados apagados.
- Histórico da conversa com a IA não persiste após recarregar a página.
- O Advisor analisa no máximo dez projetos por solicitação.
- A análise com IA depende da chave, da disponibilidade do modelo e dos limites do provedor.
- O endpoint valida método, origem e entrada, mas não implementa autenticação nem limitação de requisições. Antes de uma divulgação pública ampla, adicione controles para evitar consumo indevido da chave.
- Recomendações são apoio à decisão: revise prioridades, prazos e contexto antes de agir.

## Antes de compartilhar o código

Mantenha backups pessoais fora do repositório e da pasta `public`. O `.gitignore` incluído exclui os nomes usuais de backup e arquivos de ambiente, mas não remove arquivos já versionados. Revise também o histórico do repositório antes de torná-lo público: versões antigas podem conter o catálogo pessoal anteriormente embutido.

## Capa

Imagem criada com o gerador de imagens integrado, com o conceito: “Capa horizontal em azul-marinho escuro e verde-lima, seta de retorno envolvendo cartões abstratos de projetos, uma linha do tempo e uma etapa concluída; textos RECALL Advisor e Retome de onde parou.”
