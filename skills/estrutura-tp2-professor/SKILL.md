---
name: estrutura-tp2-professor
description: Use when creating, moving, naming, or reviewing files and folders in the Topicos 2 project, including Quarkus backend and Angular frontend based on professor Janio Junior's sga-tp2-2026 and hello-world-angular repositories.
---

# Estrutura TP2 do professor

Preserve neste projeto a divisão observada nos repositórios do professor. Esta skill é específica de Tópicos 2; instruções explícitas posteriores do usuário prevalecem.

## Antes de criar arquivos

1. Leia o `AGENTS.md` local e identifique as raízes existentes de backend e frontend pelos respectivos `pom.xml` e `angular.json`. Os caminhos abaixo são relativos a cada aplicação; os repositórios de referência são separados e não definem uma estrutura de monorepo.
2. Consulte [a referência verificada](references/estrutura-e-dependencias.md), na seção pertinente, e um arquivo equivalente existente.
3. Classifique cada arquivo na tabela. Crie somente os arquivos necessários à solicitação. Se ainda não houver aplicação, não gere um scaffold em uma tarefa apenas de estudo.
4. Preserve o padrão registrado. Mudanças futuras no upstream não alteram automaticamente a convenção; confira e documente a revisão quando o usuário solicitar atualização.

## Backend

Base: `src/main/java/br/unitins/tp2/`. Pacotes por responsabilidade; classes em PascalCase.

| Responsabilidade | Caminho para uma entidade `Curso` |
|---|---|
| Entidade JPA | `model/Curso.java` |
| Entrada e saída, records separados | `dto/CursoRequestDTO.java`, `dto/CursoResponseDTO.java` |
| Persistência Panache | `repository/CursoRepository.java` |
| Endpoint Jakarta REST | `resource/CursoResource.java` |
| Contrato e implementação | `service/CursoService.java`, `service/CursoServiceImpl.java` |
| Conversão para resposta | `mapper/CursoResponseMapper.java` |
| Conversor JPA, quando necessário | `model/converterjpa/CursoConverter.java` |
| Exceções e mapeadores HTTP | `exception/` |

Fluxo: Resource → Service → Repository. DTOs e mappers delimitam a API; implementação do serviço concentra regras e transações. Reutilize `DefaultEntity`, `PageResponse` e `Problem` quando pertinentes. Mappers observados são manuais. Configuração e SQL ficam em `src/main/resources/`; Dockerfiles em `src/main/docker/`; testes em `src/test/java/br/unitins/tp2/`.

## Frontend

Base: `src/app/`. Para `Curso`, use:

```text
components/cursos/curso-form/curso-form.ts
components/cursos/curso-form/curso-form.html
components/cursos/curso-form/curso-form.css
components/cursos/curso-list/curso-list.ts
components/cursos/curso-list/curso-list.html
components/cursos/curso-list/curso-list.css
models/curso.model.ts
services/curso.service.ts
resolvers/curso-resolver.ts
```

Classes `CursoForm` e `CursoList`; componentes standalone com imports próprios, template e CSS separados. Rotas em `app.routes.ts`: `cursos`, `cursos/new`, `cursos/edit/:id`; edição usa `cursoResolver`. Providers globais em `app.config.ts`. Use Angular Material, Reactive Forms e serviços HttpClient com Observable conforme os exemplos e a necessidade da tarefa.

## Erros comuns e conferência

- Entidades pertencem a `model/`; interface e implementação ficam juntas em `service/`.
- A pasta de telas usa plural (`cursos`); arquivos usam singular e hífens. Componentes seguem `curso-form.ts`; resolver segue `curso-resolver.ts`.
- Não inferir MapStruct, Lombok, NgRx, Bootstrap ou outra dependência a partir de hábitos pessoais. Conferir manifests e referência.
- Antes de concluir, conferir caminhos, packages, imports, rotas, contratos HTTP e dependências. Preservar organização não exige reproduzir defeitos dos exemplos nem instalar todas as bibliotecas em uma tarefa documental.
