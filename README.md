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






# Exemplo Docker + Node.js

Este projeto serve como referência simples para entender a diferença e a relação entre **Dockerfile** e **docker-compose.yaml**.

## Dockerfile vs docker-compose.yaml

| | Dockerfile | docker-compose.yaml |
|---|---|---|
| **O que é** | Receita para *construir* uma imagem | Orquestrador para *executar* um ou mais containers |
| **Responde a** | "Como montar o ambiente dentro do container?" | "Como rodar, conectar e configurar o(s) container(s)?" |
| **Comando associado** | `docker build` | `docker compose up` |
| **Obrigatório?** | Só se você precisa de uma imagem customizada | Só se quiser orquestrar execução (portas, volumes, redes, múltiplos serviços) |

### Dockerfile — construção da imagem

Define o sistema base, dependências, código copiado e comando de entrada:

```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "index.js"]
```

### docker-compose.yaml — execução do ambiente

Define como a imagem (buildada ou já pronta) vai rodar:

```yaml
services:
  app:
    build: .              # aciona o Dockerfile acima
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
    volumes:
      - .:/app
    depends_on:
      - db

  db:
    image: postgres:16     # imagem pronta, sem Dockerfile
    environment:
      - POSTGRES_PASSWORD=senha
      - POSTGRES_USER=app
      - POSTGRES_DB=appdb
    ports:
      - "5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

## Como um afeta o outro

- **Mudou o Dockerfile** (nova dependência, novo `COPY`, etc.) → precisa rebuildar a imagem:
  ```bash
  docker compose up --build
  ```
  O compose sozinho não detecta mudanças no Dockerfile automaticamente; se só rodar `up`, ele reaproveita a imagem em cache.

- **Mudou o compose** (nova porta, nova env var, novo volume) → não precisa rebuild, é config de runtime. Basta recriar o container:
  ```bash
  docker compose up -d
  ```

- **`EXPOSE` no Dockerfile** é só documentação/metadado. Quem de fato publica a porta para o host é o `ports:` do compose.

- **`ENV` no Dockerfile** define um valor padrão; `environment:` no compose sobrescreve em runtime.

## Quando eu preciso de cada um?

- **Só docker-compose.yaml**: quando a imagem que você precisa já existe pronta (ex: `postgres:16`, `redis:7`, `nginx:alpine`).
- **Dockerfile + docker-compose.yaml**: quando você precisa construir sua própria imagem (ex: sua aplicação Node) e também orquestrar como ela roda (portas, volumes, redes, dependência de outros serviços).

## Comandos úteis

```bash
# Builda as imagens e sobe os containers
docker compose up --build

# Sobe em background
docker compose up -d

# Para e remove os containers
docker compose down

# Para e remove containers + volumes
docker compose down -v

# Vê logs
docker compose logs -f
```