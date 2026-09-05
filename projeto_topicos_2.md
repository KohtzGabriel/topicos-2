# Decisões de tecnologias que serão usadas:

* Front-end: Angular
* Backend: Java + Quarkus
* Database: Postgres

# Descrição básica do projeto:
    Sistema ERP de gestão de uma loja de carros. Esta empresa possui loja física e online. O sistema deve conciliar o funcionamento dos 2 tipos de negócio.

    Para a loja online, existe somente venda de carros. Não existe compra nem negociação de valores.

    Para a loja física, funcionamento do negócio é uma garagem com compra/venda de carros novos, semi-novos e usados. Além de permitir negociação, envolvendo a troca de carros + compensação na compra/venda.

    Para negociações, além da troca de carros, pode existir negociação referente à dividas bancárias, documentos atrasados, sinistros no carro e tals. Isso tem de ser detalhado como receita/despesa extra. 
        Ex.1: Cliente quer vender o carro por 10.000 + loja assume pagar 500 reais em multas do carro.
        Neste caso, o valor do carro deve ser 10.000 e deve existir uma entrada de custos adicionais, de 500. Total da negociação fica 10.500.

        Ex.2:Cliente quer vender o carro por 10.000. Loja anão ceita assumir 500 reais de multa. Lança o carro como 10.000 + entrada de receita adicional de 500. Total da negociação ficaria 9.500

    É importante detalhar os custos adicionais e receitas para avaliação do desempenho do vendedor e compliance em caso de disputa judicial quanto à venda. 
    

# Estrutura de pastas 
meu-projeto/
├── .github/
│   └── workflows/
│       ├── ci-backend.yml                    # Pipeline de build e testes do Quarkus
│       └── ci-frontend.yml                   # Pipeline de lint, build e testes do Angular
│
├── backend/                                  # API Java 21+ com Quarkus
│   ├── src/
│   │   ├── main/
│   │   │   ├── docker/                       # Configurações de container para JVM e Native
│   │   │   │   ├── Dockerfile.jvm
│   │   │   │   └── Dockerfile.native
│   │   │   ├── java/com/empresa/projeto/
│   │   │   │   ├── config/                   # Producers CDI, CORS, OpenAPI/Swagger config
│   │   │   │   │   └── OpenApiConfig.java
│   │   │   │   ├── domain/                   # Camada de domínio (isolada de frameworks web)
│   │   │   │   │   ├── model/                # Entidades JPA (ex: PanacheEntity / Hibernate)
│   │   │   │   │   │   └── Usuario.java
│   │   │   │   │   └── service/              # Regras de negócio e transações (@Transactional)
│   │   │   │   │       └── UsuarioService.java
│   │   │   │   ├── dto/                      # Objetos de transferência de dados (Java Records)
│   │   │   │   │   ├── request/              # Payloads de entrada validados com Bean Validation
│   │   │   │   │   │   ├── UsuarioCreateRequest.java
│   │   │   │   │   │   └── UsuarioUpdateRequest.java
│   │   │   │   │   └── response/             # Payloads serializados para o cliente
│   │   │   │   │       ├── UsuarioResponse.java
│   │   │   │   │       └── UsuarioSummaryResponse.java
│   │   │   │   ├── exception/                # Tratamento de exceções centralizado
│   │   │   │   │   ├── BusinessException.java
│   │   │   │   │   ├── ErrorResponse.java    # Schema padrão para erros (status, timestamp, erros)
│   │   │   │   │   └── GlobalExceptionMapper.java # Implementações de ExceptionMapper<T>
│   │   │   │   ├── mapper/                   # Conversores MapStruct (Entity <-> DTO)
│   │   │   │   │   └── UsuarioMapper.java
│   │   │   │   ├── repository/               # Repositórios Panache (caso adote PanacheRepository)
│   │   │   │   │   └── UsuarioRepository.java
│   │   │   │   └── rest/                     # Controllers / Endpoints HTTP (@Path)
│   │   │   │       └── UsuarioResource.java
│   │   │   └── resources/
│   │   │       ├── db/migration/             # Scripts de versionamento SQL (Flyway: V1__*.sql)
│   │   │       │   ├── V1.0.0__create_table_usuarios.sql
│   │   │       │   └── V1.0.1__add_indexes.sql
│   │   │       ├── META-INF/resources/       # Arquivos estáticos servidos pelo backend
│   │   │       ├── application.properties    # Propriedades padrão e perfis (%dev, %test, %prod)
│   │   │       └── messages.properties       # Mensagens customizadas de validação
│   │   └── test/java/com/empresa/projeto/    # Testes unitários e de integração
│   │       ├── domain/
│   │       │   └── UsuarioServiceTest.java
│   │       └── rest/
│   │           └── UsuarioResourceTest.java  # Testes com @QuarkusTest e RestAssured
│   ├── .mvn/                                 # Wrapper do Maven
│   ├── mvnw
│   ├── mvnw.cmd
│   └── pom.xml                               # Dependências e plugins (MapStruct, Panache, RESTEasy)
│
├── frontend/                                 # SPA Angular (Arquitetura Standalone)
│   ├── src/
│   │   ├── app/
│   │   │   ├── core/                         # Infraestrutura singleton (carregada apenas na raiz)
│   │   │   │   ├── guards/                   # Proteção de rotas (ex: auth.guard.ts)
│   │   │   │   ├── interceptors/             # auth.interceptor.ts, error-handling.interceptor.ts
│   │   │   │   └── services/                 # auth.service.ts, storage.service.ts, theme.service.ts
│   │   │   ├── features/                     # Módulos funcionais e telas (Lazy Loaded)
│   │   │   │   ├── auth/                     # Autenticação (Login, Esqueci minha senha)
│   │   │   │   │   ├── components/
│   │   │   │   │   ├── auth.routes.ts
│   │   │   │   │   └── auth.service.ts
│   │   │   │   └── usuarios/                 # Gestão de usuários
│   │   │   │       ├── components/           # Componentes de apresentação locais
│   │   │   │       │   ├── usuario-card/
│   │   │   │       │   └── usuario-form/
│   │   │   │       ├── models/               # Tipagens espelhadas do Quarkus
│   │   │   │       │   ├── usuario-request.model.ts   # UsuarioCreateRequest, UsuarioUpdateRequest
│   │   │   │       │   └── usuario-response.model.ts  # UsuarioResponse, UsuarioSummaryResponse
│   │   │   │       ├── pages/                # Componentes que representam telas inteiras
│   │   │   │       │   ├── usuario-detalhe-page/
│   │   │   │       │   └── usuario-lista-page/
│   │   │   │       ├── services/             # Chamadas HTTP exclusivas da feature
│   │   │   │       │   └── usuario.service.ts
│   │   │   │       └── usuarios.routes.ts    # Rotas internas da feature
│   │   │   ├── shared/                       # Elementos reutilizáveis entre múltiplas features
│   │   │   │   ├── components/               # Navbar, sidebar, modais, tabelas genéricas
│   │   │   │   ├── directives/               # Máscaras de input, controle de permissão visual
│   │   │   │   ├── models/                   # Paginação (PageResponse), filtros globais
│   │   │   │   └── pipes/                    # Formatação de datas, moeda brasileira, CPF/CNPJ
│   │   │   ├── app.component.html
│   │   │   ├── app.component.scss
│   │   │   ├── app.component.ts              # Componente base com <router-outlet>
│   │   │   ├── app.config.ts                 # Configuração standalone: provideHttpClient, provideRouter
│   │   │   └── app.routes.ts                 # Roteamento principal com loadChildren para as features
│   │   ├── assets/                           # Arquivos estáticos
│   │   │   ├── icons/
│   │   │   └── images/
│   │   ├── environments/                     # Variáveis de ambiente da SPA
│   │   │   ├── environment.development.ts    # apiUrl: 'http://localhost:8080'
│   │   │   └── environment.ts                # apiUrl: 'https://api.empresa.com'
│   │   ├── index.html
│   │   ├── main.ts                           # Ponto de inicialização (bootstrapApplication)
│   │   └── styles.scss                       # Estilos globais e tokens de CSS
│   ├── angular.json
│   ├── package.json
│   ├── tsconfig.app.json
│   └── tsconfig.json
│
├── docs/                                     # Documentação de projeto
│   ├── architecture/                         # Diagramas C4, esquemas de banco de dados
│   └── api/                                  # Exportações do OpenAPI / Insomnia / Postman
│
├── docker-compose.yml                        # Banco de dados local (PostgreSQL), Redis, Mailpit
├── .dockerignore
├── .gitignore                                # Arquivos ignorados pelo Git (.angular/, target/, .env)
└── README.md                                 # Instruções de setup, execução e padrões de commit



    
# Documentos de levantamento de requisitos e engenharia
<!--[Aqui vamos inserir hyperlink de cada um deles]-->

# Regra para implementação e correção de erros:

    1- Verificar se a branch main local está junto com a main do github.
    2- Criar uma nova branch local, especificando a implementação/correção/refatoração que está sendo feita
    3- Pull request para o git, roda os testes local da nova implementação.
    4- Passando os testes unitários e manuais, merge na main.

