---
title: Utilizar el CLI de GitHub en los flujos de trabajo
shortTitle: GitHub CLI in workflows
intro: 'Puedes hacer scripts con el {% data variables.product.prodname_cli %} en los flujos de trabajo de {% data variables.product.prodname_actions %}.'
redirect_from:
  - /actions/guides/using-github-cli-in-workflows
  - /actions/advanced-guides/using-github-cli-in-workflows
versions:
  fpt: '*'
  ghes: '*'
  ghae: '*'
  ghec: '*'
topics:
  - CLI
  - Workflows
type: how_to
ms.openlocfilehash: 3455e4f082acf26215ad050c5c381df53c0c2713
ms.sourcegitcommit: 47bd0e48c7dba1dde49baff60bc1eddc91ab10c5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/05/2022
ms.locfileid: '145120898'
---
{% data reusables.cli.cli-learn-more %}

El {% data variables.product.prodname_cli %} está preinstalado en todos los ejecutores hospedados en {% data variables.product.prodname_dotcom %}. Para cada paso en el se use {% data variables.product.prodname_cli %}, debe establecer una variable de entorno llamada `GITHUB_TOKEN` em un token con los ámbitos necesarios.

Puedes ejecutar cualquier comando del {% data variables.product.prodname_cli %}. Por ejemplo, este flujo de trabajo usa el subcomando `gh issue comment` para agregar un comentario cuando se abre una incidencia.

```yaml{:copy}
name: Comment when opened
on:
  issues:
    types:
      - opened
jobs:
  comment:
    runs-on: ubuntu-latest
    steps:
      - run: gh issue comment $ISSUE --body "Thank you for opening this issue!"
        env:
          GITHUB_TOKEN: {% raw %}${{ secrets.GITHUB_TOKEN }}{% endraw %}
          ISSUE: {% raw %}${{ github.event.issue.html_url }}{% endraw %}
```

También puedes ejecutar llamadas de la API a través de {% data variables.product.prodname_cli %}. Por ejemplo, este flujo de trabajo usa primero el subcomando `gh api` para consultar GraphQL API y analizar el resultado. Entonces, almacenará el resultado en una variable de ambiente a la que pueda acceder en un paso posterior. En el segundo paso, usa el subcomando `gh issue create` para crear una incidencia que contiene la información del primer paso.

```yaml{:copy}
name: Report remaining open issues
on: 
  schedule: 
    # Daily at 8:20 UTC
    - cron: '20 8 * * *'
jobs:
  track_pr:
    runs-on: ubuntu-latest
    steps:
      - run: |
          numOpenIssues="$(gh api graphql -F owner=$OWNER -F name=$REPO -f query='
            query($name: String!, $owner: String!) {
              repository(owner: $owner, name: $name) {
                issues(states:OPEN){
                  totalCount
                }
              }
            }
          ' --jq '.data.repository.issues.totalCount')"

          echo 'NUM_OPEN_ISSUES='$numOpenIssues >> $GITHUB_ENV
        env:
          GITHUB_TOKEN: {% raw %}${{ secrets.GITHUB_TOKEN }}{% endraw %}
          OWNER: {% raw %}${{ github.repository_owner }}{% endraw %}
          REPO: {% raw %}${{ github.event.repository.name }}{% endraw %}
      - run: |
          gh issue create --title "Issue report" --body "$NUM_OPEN_ISSUES issues remaining" --repo $GITHUB_REPOSITORY
        env:
          GITHUB_TOKEN: {% raw %}${{ secrets.GITHUB_TOKEN }}{% endraw %}
```
