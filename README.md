# Guia Completo de GitHub Actions

## Introdução
O **GitHub Actions** é uma plataforma de automação de fluxos de trabalho (CI/CD) integrada ao GitHub.  
Ele permite **compilar, testar e implantar** aplicações diretamente a partir de repositórios, usando arquivos de configuração em YAML.

---

## 1. Conceitos Fundamentais

- **Workflow**: conjunto de jobs que descrevem o processo de automação.  
- **Job**: coleção de *steps* que são executados em sequência no mesmo runner.  
- **Step**: instruções individuais de um job (ex.: rodar script, instalar dependência).  
- **Runner**: máquina que executa os jobs (pode ser hospedada pelo GitHub ou self-hosted).  

---

## 2. Estrutura de um Workflow

Um workflow é definido em um arquivo YAML dentro de `.github/workflows/`.

Exemplo simples:
```yaml
name: CI

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout código
        uses: actions/checkout@v3

      - name: Configurar Java
        uses: actions/setup-java@v3
        with:
          java-version: '17'

      - name: Rodar build com Maven
        run: mvn clean install
```

---

## 3. Eventos

Os workflows são disparados por **eventos**:
- `push`: quando há push em uma branch.  
- `pull_request`: ao abrir/modificar um PR.  
- `schedule`: execução agendada (cron).  
- `workflow_dispatch`: execução manual.  

Exemplo com cron:
```yaml
on:
  schedule:
    - cron: "0 3 * * *" # roda todos os dias às 03:00 UTC
```

---

## 4. Actions

- **Actions** são blocos reutilizáveis de código dentro dos workflows.  
- Podem ser criadas pelo GitHub, comunidade ou você mesmo.  

Exemplo: usar uma action para fazer login no DockerHub:
```yaml
- name: Login no DockerHub
  uses: docker/login-action@v2
  with:
    username: ${{ secrets.DOCKER_USERNAME }}
    password: ${{ secrets.DOCKER_PASSWORD }}
```

---

## 5. Variáveis e Secrets

- **Secrets**: informações sensíveis, configuradas no repositório (ex.: tokens, senhas).  
- **Variáveis**: valores globais reutilizáveis.  

```yaml
env:
  NODE_ENV: production

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Exibir variável
        run: echo "Ambiente: $NODE_ENV"
```

Acessando secrets:
```yaml
- name: Usando secrets
  run: echo "Token = ${{ secrets.MY_SECRET }}"
```

---

## 6. Matrizes (Matrix Builds)

Permite rodar o mesmo job em múltiplas combinações (ex.: várias versões do Java ou Node).

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        java: [8, 11, 17]
    steps:
      - uses: actions/checkout@v3
      - name: Configurar Java ${{ matrix.java }}
        uses: actions/setup-java@v3
        with:
          java-version: ${{ matrix.java }}
      - run: mvn test
```

---

## 7. Exemplo de Deploy

Exemplo de deploy de uma aplicação Node.js no GitHub Pages:
```yaml
name: Deploy

on:
  push:
    branches: [ "main" ]

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      - run: npm install && npm run build
      - name: Deploy para GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist
```

---

## 8. Boas Práticas

- Nomeie workflows de forma clara.  
- Use caching (`actions/cache`) para acelerar builds.  
- Centralize variáveis em `env`.  
- Proteja secrets e nunca exponha em logs.  
- Prefira actions oficiais e confiáveis.  
- Monitore tempo e custo de execução.  

---

## 9. Armadilhas Comuns

- Esquecer de usar `secrets` para dados sensíveis.  
- Executar jobs em branches erradas sem filtros (`on.push.branches`).  
- Falta de cache pode tornar builds lentos.  
- Jobs longos sem paralelização.  

---

## 10. Recursos para Estudo

- Documentação oficial: [GitHub Actions](https://docs.github.com/actions).  
- Marketplace: [GitHub Actions Marketplace](https://github.com/marketplace?type=actions).  
- Curso gratuito: *GitHub Actions for CI/CD* no [GitHub Learning Lab](https://lab.github.com/).  

---

## Conclusão
O **GitHub Actions** é uma ferramenta poderosa de automação que permite implementar pipelines de CI/CD diretamente nos repositórios. Ao dominar workflows, jobs, steps, secrets e matrizes, é possível criar fluxos de trabalho eficientes, seguros e escaláveis.
