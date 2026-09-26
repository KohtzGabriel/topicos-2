# Referência verificada em 26/09/2026

## Fontes e escopo

Leitura das árvores completas pela API do GitHub e dos manifests e exemplos pelo conteúdo raw, fixados nos commits abaixo:

- Backend: [sga-tp2-2026](https://github.com/janiojunior/sga-tp2-2026/tree/05f57fc514b75ff9f970d1d4d658c9070bf1aae1), commit `05f57fc514b75ff9f970d1d4d658c9070bf1aae1`.
- Frontend: [hello-world-angular/src/app](https://github.com/janiojunior/hello-world-angular/tree/5ce382bef1d0cf409bfb1da96e628ccc6a84ac3c/src/app), commit `5ce382bef1d0cf409bfb1da96e628ccc6a84ac3c`.

As árvores e dependências são observações. O exemplo `Curso` da skill é uma extrapolação dos nomes existentes, não uma entidade encontrada no professor. As regras são locais a este projeto. Não se presume que arquivos de orientação presentes no repositório remoto governem este workspace.

## Backend: árvore observada

```text
pom.xml
mvnw / mvnw.cmd
.mvn/wrapper/maven-wrapper.properties
src/
  main/
    docker/                       # Dockerfile.jvm, .legacy-jar, .native, .native-micro
    java/br/unitins/tp2/
      dto/                        # Estado, Municipio, Paciente, Psicologo, Telefone
                                  # RequestDTO e ResponseDTO; PageResponse.java
      exception/                  # Problem, ValidationException, ResourceNotFoundException
                                  # e ExceptionMappers, incluindo NotFound e Uncaught
      mapper/                     # EstadoResponseMapper, MunicipioResponseMapper,
                                  # PacienteResponseMapper, PsicologoResponseMapper, TelefoneMapper
      model/                      # DefaultEntity, Estado, Municipio, Pessoa,
                                  # Paciente, Psicologo, Regiao, Telefone
        converterjpa/             # RegiaoConverter.java
      repository/                 # Estado, Municipio, Paciente, Pessoa, Psicologo + Repository
      resource/                   # Estado, Municipio, Paciente, Psicologo, Regiao, Root + Resource
      service/                    # Estado, Municipio, Paciente, Psicologo
                                  # Service e ServiceImpl no mesmo pacote
    resources/
      application.properties
      import.sql
  test/java/br/unitins/tp2/
    GreetingResourceTest.java
    GreetingResourceIT.java
```

### Responsabilidades observadas

No [CRUD de Estado](https://github.com/janiojunior/sga-tp2-2026/blob/05f57fc514b75ff9f970d1d4d658c9070bf1aae1/src/main/java/br/unitins/tp2/resource/EstadoResource.java), Resource injeta a interface do serviço, recebe RequestDTO e usa mapper para produzir ResponseDTO. ServiceImpl é `@ApplicationScoped`, injeta repository e usa `@Transactional` em mutações. Repository implementa `PanacheRepository<Estado>`. Entidade estende `DefaultEntity`, que contém ID e datas de cadastro/alteração. DTOs são records; validação de entrada usa Jakarta Validation e Hibernate Validator. Mapper de resposta é classe final com método estático `toResponse`; `TelefoneMapper` também possui `toModel`.

`PageResponse` representa paginação. `exception/` concentra o formato `Problem`, exceções de domínio e tradução para respostas HTTP. Não criar uma camada controller ou um subpacote service/impl para substituir os nomes observados.

### Extensões Quarkus declaradas

Fonte: [pom.xml](https://github.com/janiojunior/sga-tp2-2026/blob/05f57fc514b75ff9f970d1d4d658c9070bf1aae1/pom.xml). Java release **25**; plataforma Quarkus **3.38.3**. Todos os artefatos abaixo usam o grupo `io.quarkus` e versões gerenciadas pelo BOM.

| Artefato | Finalidade |
|---|---|
| `quarkus-rest` | Endpoints Jakarta REST |
| `quarkus-rest-jackson` | JSON com Jackson |
| `quarkus-smallrye-openapi` | OpenAPI e Swagger UI |
| `quarkus-hibernate-orm-panache` | Persistência com Panache |
| `quarkus-hibernate-orm` | ORM, também declarado explicitamente |
| `quarkus-jdbc-postgresql` | Driver PostgreSQL |
| `quarkus-arc` | Injeção CDI |
| `quarkus-hibernate-validator` | Bean Validation |
| `quarkus-smallrye-jwt` | Suporte a verificação JWT |
| `quarkus-smallrye-jwt-build` | Construção/assinatura de JWT |
| `quarkus-junit` | Testes; escopo `test` |

Também declarado: `io.rest-assured:rest-assured`, escopo `test`; não é extensão Quarkus. Plugin de build: `io.quarkus.platform:quarkus-maven-plugin`; não confundir com extensão da aplicação ou do VS Code.

`application.properties` configura PostgreSQL e CORS para `http://localhost:4200`. As propriedades de chaves/issuer JWT estão comentadas: dependências presentes não comprovam autenticação implementada. Comentários sobre SeaweedFS não correspondem a uma dependência declarada no POM. Configurações locais de banco, credenciais e recriação de schema não são convenções de organização a copiar automaticamente.

## Frontend: árvore observada

```text
package.json / package-lock.json / angular.json
public/favicon.ico
src/
  main.ts
  index.html
  styles.css
  material-theme.scss
  app/
    app.ts / app.html / app.css / app.spec.ts
    app.config.ts
    app.routes.ts
    components/
      estados/estado-form/         # estado-form.ts, .html, .css
      estados/estado-list/         # estado-list.ts, .html, .css
      municipios/municipio-form/   # mesmo trio de arquivos
      municipios/municipio-list/
      pacientes/paciente-form/
      pacientes/paciente-list/
      psicologos/psicologo-form/
      psicologos/psicologo-list/
    models/                       # estado, municipio, paciente, psicologo,
                                  # regiao, telefone, api-problem, page-response + .model.ts
    services/                     # estado, municipio, paciente, psicologo, regiao + .service.ts
    resolvers/                    # estado, municipio, paciente, psicologo + -resolver.ts
```

### Bibliotecas declaradas

Fonte: [package.json](https://github.com/janiojunior/hello-world-angular/blob/5ce382bef1d0cf409bfb1da96e628ccc6a84ac3c/package.json). Estes são intervalos declarados, não versões resolvidas do lockfile.

| Dependências | Versão declarada |
|---|---|
| `@angular/common`, `compiler`, `core`, `forms`, `platform-browser`, `router` | `^22.1.0` |
| `@angular/material`, `@angular/cdk` | `^22.1.4` |
| `rxjs` | `~7.8.0` |
| `tslib` | `^2.3.0` |
| `@angular/build`, `@angular/cli` (desenvolvimento) | `^22.1.5` |
| `@angular/compiler-cli` (desenvolvimento) | `^22.1.0` |
| `typescript` (desenvolvimento) | `~6.0.2` |
| `vitest`, `jsdom`, `prettier` (desenvolvimento) | `^4.0.8`, `^28.0.0`, `^3.8.1` |

Gerenciador declarado: `npm@11.17.0`. Scripts: `start` (`ng serve`), `build`, `watch`, `test` (`ng test`).

### Funcionalidades efetivamente observadas

- Componentes standalone com `imports` no decorator, `templateUrl` e `styleUrl`; classes sem sufixo Component (`EstadoForm`, `EstadoList`, `App`). Não há AppModule na árvore.
- [app.config.ts](https://github.com/janiojunior/hello-world-angular/blob/5ce382bef1d0cf409bfb1da96e628ccc6a84ac3c/src/app/app.config.ts): `provideRouter`, `provideHttpClient`, `provideBrowserGlobalErrorListeners` e `MatPaginatorIntl` em português.
- [app.routes.ts](https://github.com/janiojunior/hello-world-angular/blob/5ce382bef1d0cf409bfb1da96e628ccc6a84ac3c/src/app/app.routes.ts): listagem, `/new`, `/edit/:id`, títulos e `resolve` na edição; imports diretos dos componentes.
- Resolvers funcionais `ResolveFn<T>`, `inject(Service)` e `route.paramMap.get('id')`; formulários recebem dados via `ActivatedRoute.snapshot.data`.
- Reactive Forms com `FormBuilder`, `FormGroup`, `Validators`; formulários de paciente/psicólogo usam `FormArray` para telefones. Templates usam `@if` e `@for` onde pertinentes.
- Angular Material: toolbar, card, form-field, input, button, icon, select, snack-bar, table e paginator nos exemplos lidos. Imports devem acompanhar o template utilizado.
- Listas usam `MatTableDataSource`, `PageEvent`, `pageIndex`, `pageSize` e `totalItems`. Estado/Município filtram localmente os itens carregados; Psicólogo consulta por nome no servidor. Esses comportamentos não são equivalentes; escolher conforme o requisito.
- Serviços `@Injectable({providedIn: 'root'})` usam `HttpClient` e `Observable`, endpoints locais na porta 8080, métodos CRUD e consultas específicas de domínio.
- Models incluem classes de entidade, tipos de request junto ao model, `PageResponse<T>` e interfaces `ApiProblem`/`ApiFieldError`. Estado/Município ainda declaram seus tipos de paginação nos services; Paciente/Psicólogo reutilizam `models/page-response.model.ts`.
- Município/Paciente/Psicólogo apresentam erros de API por campo e feedback `MatSnackBar`; paciente/psicólogo incluem busca por CPF e telefones dinâmicos.
- `signal` aparece no título de `App`; isso não estabelece uma arquitetura de estado global com signals.
- [angular.json](https://github.com/janiojunior/hello-world-angular/blob/5ce382bef1d0cf409bfb1da96e628ccc6a84ac3c/angular.json) ativa `styles.css` e o tema Material `indigo-pink.css`. A entrada de `material-theme.scss` está comentada.

### Limites da referência

Preservar estrutura e bibliotecas não significa copiar inconsistências: por exemplo, o PUT de Estado no backend retorna `void`, mas o service Angular declara `Observable<Estado>`. Conferir o contrato real antes de implementar. Os testes existentes foram localizados, não executados; nenhuma alegação de build aprovado dos repositórios é feita. Dependências e versões devem ser reavaliadas somente quando a tarefa exigir instalação ou atualização.
