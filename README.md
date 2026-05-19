# 🐾 PetFlow — Clyvo Vet API Java

**Infraestrutura do futuro da medicina veterinária digital**

Plataforma de saúde animal que transforma a jornada do pet de um modelo episódico e reativo para uma experiência contínua, preventiva, inteligente e integrada. Desenvolvida como parte do Challenge Clyvo Vet — FIAP 2026.

---

## 📋 Índice

- [Descrição do Projeto](#-descrição-do-projeto)
- [Benefícios para o Negócio](#-benefícios-para-o-negócio)
- [Arquitetura Macro](#-arquitetura-macro)
- [Rotas da API](#-rotas-da-api)
- [Instalação da Solução (How to)](#-instalação-da-solução-how-to)
- [Dockerfile](#-dockerfile)
- [Docker Compose](#-docker-compose)
- [Script Azure CLI](#-script-azure-cli)
- [Equipe](#-equipe)

---

## 📖 Descrição do Projeto

O PetFlow é uma API REST desenvolvida em **Java 21** com **Spring Boot 4.0.6**, conectada a um banco **Oracle XE 21c**, ambos conteinerizados com Docker e provisionados na **Microsoft Azure** via scripts automatizados com Azure CLI.

A plataforma conecta tutores de pets, clínicas veterinárias e o ecossistema de saúde animal, oferecendo funcionalidades como:

- Cadastro de pets, tutores, clínicas e veterinários
- Agendamento de consultas e registro de anamneses
- Controle de vacinação e prescrições médicas
- Monitoramento via sensores IoT com alertas inteligentes
- Autenticação JWT (login, refresh token, logout)
- Documentação interativa via Swagger

### Tecnologias utilizadas

| Camada | Tecnologia |
|---|---|
| Backend | Java 21, Spring Boot 4.0.6, JPA/Hibernate, Lombok |
| Banco de Dados | Oracle XE 21c (gvenzl/oracle-xe:21-slim) |
| Autenticação | JWT (access token + refresh token) |
| Documentação | Swagger / OpenAPI 3 |
| Conteinerização | Docker, Docker Compose |
| Infraestrutura | Microsoft Azure, Azure CLI, Ubuntu 22.04 |

---

## 💼 Benefícios para o Negócio

**Para o tutor do pet:** acesso centralizado ao histórico de saúde, lembretes automáticos de vacinas e consultas, triagem inteligente de sintomas com IA, e maior previsibilidade no cuidado preventivo.

**Para o pet:** melhor cuidado preventivo, detecção precoce de problemas via IoT, maior adesão a tratamentos, e acompanhamento longitudinal da saúde.

**Para clínicas e hospitais:** aumento da recorrência e fidelização de clientes, redução do abandono de tratamentos, dashboard com KPIs em tempo real, e maior LTV por paciente.

**Para a Clyvo Vet:** geração de moat competitivo através de dados, escalabilidade via conteinerização em nuvem, e base para monetização com planos de assinatura e marketplace de serviços.

---

## 🔀 Rotas da API

### Rotas públicas (sem autenticação)

| Método | Rota | Descrição |
|---|---|---|
| `POST` | `/clinicas` | Criar clínica |
| `POST` | `/veterinarios` | Criar veterinário |
| `POST` | `/tutores` | Criar tutor |
| `POST` | `/auth/login` | Autenticar (retorna JWT) |
| `POST` | `/auth/refresh` | Renovar token |
| `POST` | `/auth/logout` | Revogar refresh token |
| `GET` | `/swagger-ui.html` | Documentação interativa |
| `GET` | `/v3/api-docs` | OpenAPI JSON |

### Rotas protegidas (requerem header `Authorization: Bearer <token>`)

| Método | Rota | Descrição |
|---|---|---|
| `GET` | `/especies` | Listar espécies (paginado) |
| `GET` | `/especies/{id}` | Buscar espécie por ID |
| `POST` | `/especies` | Criar espécie |
| `PUT` | `/especies/{id}` | Atualizar espécie |
| `DELETE` | `/especies/{id}` | Remover espécie |
| `GET` | `/racas` | Listar raças |
| `GET` | `/pets` | Listar pets |
| `GET` | `/consultas` | Listar consultas |
| `GET` | `/agendamentos` | Listar agendamentos |
| `GET` | `/veterinarios` | Listar veterinários |
| `GET` | `/tutores` | Listar tutores |
| `GET` | `/clinicas` | Listar clínicas |
| `GET` | `/vacinas/tipos` | Listar tipos de vacina |
| `GET` | `/vacinas/aplicacoes` | Listar aplicações de vacina |
| `GET` | `/exames` | Listar exames |
| `GET` | `/prescricoes` | Listar prescrições |
| `GET` | `/sensores-iot` | Listar sensores IoT |
| `GET` | `/leituras-iot` | Listar leituras IoT |
| `GET` | `/alertas-iot` | Listar alertas IoT |

### Exemplo de uso

```bash
# 1. Criar clínica (público)
curl -X POST http://<IP_PUBLICO>:8080/clinicas \
  -H "Content-Type: application/json" \
  -d '{"nome":"CLYVO Vet SP","cnpj":"12345678000199","logradouro":"Av. Paulista 1000","cidade":"Sao Paulo","estado":"SP"}'

# 2. Criar veterinário (público)
curl -X POST http://<IP_PUBLICO>:8080/veterinarios \
  -H "Content-Type: application/json" \
  -d '{"nome":"Dr. Paulo Costa","crmv":"SP-67890","email":"paulo@clyvovet.com","senha":"<SUA_SENHA>","clinicaId":1}'

# 3. Login (obter token)
curl -X POST http://<IP_PUBLICO>:8080/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"paulo@clyvovet.com","senha":"<SUA_SENHA>","tipo":"VETERINARIO"}'

# 4. Usar token nas rotas protegidas
curl http://<IP_PUBLICO>:8080/especies \
  -H "Authorization: Bearer <SEU_TOKEN>"
```

---

## 🚀 Instalação da Solução (How to)

### Pré-requisitos

- Azure CLI instalado e autenticado (`az login`)
- Git Bash ou terminal Unix-like
- Acesso à subscription Azure for Students

### Passo 1 — Provisionar a infraestrutura

```bash
bash setup-clyvovet.sh
```

Este script cria o Resource Group, a VM Ubuntu 22.04 (Standard_B2als_v2, 4GB RAM), abre as portas 8080 e 1521, instala Docker e ferramentas (Git, nano, curl, wget, htop).

### Passo 2 — Validar a infraestrutura (opcional)

```bash
bash validate-clyvovet.sh
```

Verifica 7 itens: Resource Group, VM rodando, IP público, portas 8080 e 1521, Docker instalado, ferramentas.

### Passo 3 — Conectar na VM

```bash
ssh azureuser@<IP_PUBLICO>
```

> As credenciais de acesso são exibidas ao final da execução do `setup-clyvovet.sh`.

### Passo 4 — Clonar o repositório e configurar variáveis de ambiente

```bash
cd ~
git clone https://github.com/olavoneves/clyvo-vet_api-java.git
cd clyvo-vet_api-java

# Copiar o template e preencher com suas credenciais
cp .env.example .env
nano .env
```

> ⚠️ **Nunca commite o arquivo `.env`** — ele contém credenciais sensíveis e está no `.gitignore`.  
> Consulte o `.env.example` para ver as variáveis necessárias.

### Passo 5 — Subir os containers

```bash
docker compose up -d --build
```

O Docker Compose sobe dois containers:
- **oracle-clyvovet** — Oracle XE 21c com healthcheck (~2-3 min para inicializar)
- **api-clyvovet** — API Java Spring Boot (inicia somente após o Oracle estar saudável)

As tabelas são criadas automaticamente pelo JPA (ddl-auto: update).

### Passo 6 — Verificar containers

```bash
docker compose ps                  # Containers rodando em background
docker exec api-clyvovet whoami    # Deve retornar: appuser (não-root)
docker volume ls                   # Deve aparecer: oracle-data
```

### Passo 7 — Testar a API

Acesse o Swagger no navegador:
```
http://<IP_PUBLICO>:8080/swagger-ui.html
```

Ou teste via curl conforme os exemplos da seção de rotas.

### Passo 8 — Remover recursos (obrigatório ao final)

```bash
bash cleanup-clyvovet.sh
```

Ou diretamente via Azure CLI:

```bash
az group delete --name rg-clyvovet-devops --yes --no-wait
```

---

## 🐳 Dockerfile

```dockerfile
# Estagio 1: Build com Maven
FROM maven:3.9.9-eclipse-temurin-21 AS builder
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline
COPY src ./src
RUN mvn clean package -DskipTests

# Estagio 2: Imagem enxuta para execucao
FROM eclipse-temurin:21-jre
RUN groupadd --system appuser && useradd --system --gid appuser appuser
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar
RUN chown appuser:appuser app.jar
USER appuser
EXPOSE 8080
HEALTHCHECK --interval=30s --timeout=10s --start-period=60s --retries=3 \
  CMD curl -f http://localhost:8080/actuator/health || exit 1
ENTRYPOINT ["java", "-jar", "app.jar"]
```

**Destaques:**
- Multi-stage build (Maven para compilar, JRE para executar — imagem final menor)
- Usuário `appuser` não-root (requisito do entregável)
- Credenciais injetadas em runtime via docker-compose / `.env` — nenhuma senha no Dockerfile

---

## 📦 Docker Compose

```yaml
services:
  oracle-db:
    image: gvenzl/oracle-xe:21-slim
    container_name: oracle-clyvovet
    env_file: .env
    environment:
      - ORACLE_PASSWORD=${ORACLE_PASSWORD}
      - APP_USER=${APP_USER}
      - APP_USER_PASSWORD=${APP_USER_PASSWORD}
      - ORACLE_DATABASE=${ORACLE_DATABASE}
    ports:
      - "1521:1521"
    volumes:
      - oracle-data:/opt/oracle/oradata
    networks:
      - clyvovet-network
    mem_limit: 2g
    healthcheck:
      test: ["CMD", "healthcheck.sh"]
      interval: 30s
      timeout: 10s
      retries: 10
      start_period: 120s
    restart: unless-stopped

  api-java:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: api-clyvovet
    env_file: .env
    environment:
      - SPRING_DATASOURCE_URL=jdbc:oracle:thin:@oracle-db:1521/${ORACLE_DATABASE}
      - SPRING_DATASOURCE_USERNAME=${APP_USER}
      - SPRING_DATASOURCE_PASSWORD=${APP_USER_PASSWORD}
      - SPRING_JPA_HIBERNATE_DDL_AUTO=update
      - SPRING_JPA_DATABASE_PLATFORM=org.hibernate.dialect.OracleDialect
      - JWT_SECRET=${JWT_SECRET}
    ports:
      - "8080:8080"
    networks:
      - clyvovet-network
    mem_limit: 1g
    depends_on:
      oracle-db:
        condition: service_healthy
    restart: unless-stopped

volumes:
  oracle-data:

networks:
  clyvovet-network:
```

**Destaques:**
- Todas as credenciais lidas do arquivo `.env` via `env_file` — nenhuma senha hardcoded
- Volume nomeado `oracle-data` para persistência dos dados (requisito do entregável)
- Healthcheck no Oracle — a API só inicia quando o banco estiver pronto
- `mem_limit` definido para evitar consumo excessivo de memória na VM (2GB Oracle, 1GB API)
- Rede `clyvovet-network` isolando a comunicação entre containers

---

## ☁️ Script Azure CLI

O script `setup-clyvovet.sh` provisiona toda a infraestrutura automaticamente:

| Etapa | Comando | Descrição |
|---|---|---|
| 1.1 | `az group create` | Criar Resource Group |
| 1.2 | `az vm create` | Provisionar VM Linux (Ubuntu 22.04, B2als_v2, 4GB) |
| 1.3 | `az vm open-port` | Abrir portas 8080 (Java) e 1521 (Oracle) |
| 1.4 | `az vm run-command invoke` | Instalar Docker Engine + Compose |
| 1.5 | `az vm run-command invoke` | Instalar Git, nano, curl, wget, htop |
| 1.6 | `az vm show` | Exibir IP público e dados de acesso |

**Variáveis de configuração:**

```bash
RESOURCE_GROUP="rg-clyvovet-devops"
LOCATION="northcentralus"
VM_NAME="vm-clyvovet"
VM_SIZE="Standard_B2als_v2"
IMAGE="Ubuntu2204"
ADMIN_USER="azureuser"
```

**Remoção (obrigatório ao final da entrega):**

```bash
az group delete --name rg-clyvovet-devops --yes --no-wait
```

---

## 👥 Equipe

| Nome | RM |
|---|---|
| Altamir Lima | 562906 |
| Felipe Conte | 562248 |
| Luiz Gonçalves | 564495 |
| Olavo Neves | 563558 |
| Pedro França | 561940 |

**Equipe:** PetFlow  
**Curso:** Tecnologia em Desenvolvimento de Sistemas — FIAP  
**Challenge:** Clyvo Vet — 1º e 2º Sprint (2026)