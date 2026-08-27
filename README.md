# Docker Hello World - Node.js

Projeto de exemplo com Node.js, Express e Redis rodando via Docker Compose.

## Pré-requisitos

- Docker
- Docker Compose (plugin v2, comando `docker compose`, sem hífen)

## Como rodar

```bash
docker compose up -d
```

## Como testar

Abra o navegador em:

```
http://localhost:8080
```

Você deve ver a mensagem `Hello World`.

## Como parar

```bash
docker compose down
```

## Estrutura

- `app.js` - servidor Express
- `Dockerfile` - imagem da aplicação Node
- `docker-compose.yaml` - orquestração dos serviços (app + redis)
- `package.json` - dependências do projeto
