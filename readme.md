# Imersão DevOps - Alura Google Cloud

## Sumário
- [Imersão DevOps - Alura Google Cloud](#imersão-devops---alura-google-cloud)
  - [Sumário](#sumário)
  - [Sobre o Projeto](#sobre-o-projeto)
  - [Pré-requisitos](#pré-requisitos)
  - [Como rodar localmente](#como-rodar-localmente)
  - [Usando Docker](#usando-docker)
  - [Usando Docker Compose](#usando-docker-compose)
  - [Como versionar seu projeto com Git e enviar para o GitHub](#como-versionar-seu-projeto-com-git-e-enviar-para-o-github)
  - [Autenticando no Google Cloud](#autenticando-no-google-cloud)
  - [Estrutura do Projeto](#estrutura-do-projeto)
  - [Autenticação no Google Cloud Avançado](#autenticação-no-google-cloud-avançado)
  - [Deploy no Cloud Run](#deploy-no-cloud-run)
  - [Mais tutoriais DevOps](#mais-tutoriais-devops)

---

## Sobre o Projeto

Este projeto é uma API desenvolvida com FastAPI para gerenciar alunos, cursos e matrículas em uma instituição de ensino.

## Pré-requisitos

- [Python 3.10 ou superior instalado](https://www.python.org/downloads/)
- [Git](https://git-scm.com/downloads)
- [Docker](https://www.docker.com/get-started/)

## Como rodar localmente

1. Clone o repositório:
   ```sh
   git clone https://github.com/givgramacho/devops-alura-google.git
   cd devops-alura-google
   ```
2. Crie e ative o ambiente virtual:
   ```sh
   python3 -m venv venv
   source venv/bin/activate  # Linux/Mac
   # ou
   venv\Scripts\activate    # Windows
   ```
3. Instale as dependências:
   ```sh
   pip install -r requirements.txt
   ```
4. Execute a aplicação:
   ```sh
   uvicorn app:app --reload
   ```
5. Acesse a documentação interativa em [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs)

## Usando Docker

1. Construa a imagem:
   ```sh
   docker build -t fastapi-app .
   ```
2. Rode o container:
   ```sh
   docker run -p 8000:8000 fastapi-app
   ```
3. Acesse: [http://localhost:8000/docs](http://localhost:8000/docs)

## Usando Docker Compose

1. Construa e suba os containers:
   ```sh
   docker-compose up --build
   ```
2. Acesse: [http://localhost:8000/docs](http://localhost:8000/docs)


## Como versionar seu projeto com Git e enviar para o GitHub

1. **Inicialize o repositório Git no diretório do projeto:**
   ```sh
   git init
   ```
2. **Adicione todos os arquivos do projeto ao controle de versão:**
   ```sh
   git add .
   ```
3. **Faça o primeiro commit:**
   ```sh
   git commit -m "Primeiro commit do projeto FastAPI"
   ```
4. **Crie um repositório no GitHub:**
   - Acesse [https://github.com/new](https://github.com/new) e crie um novo repositório (por exemplo, `imersao-devops`).
5. **Adicione o repositório remoto ao seu projeto local:**
   ```sh
   git remote add origin https://github.com/SEU_USUARIO/imersao-devops.git
   ```
   Substitua `SEU_USUARIO` pelo seu nome de usuário do GitHub.
6. **Envie o código para o GitHub:**
   ```sh
   git branch -M main
   git push -u origin main
   ```

Este projeto é uma API desenvolvida com FastAPI para gerenciar alunos, cursos e matrículas em uma instituição de ensino.
## Autenticando no Google Cloud
```sh
gcloud auth login
## Crie o projeto antes no Google Cloud para obter o Project_ID, mais detalhes na sessão abaixo ## Autenticação no Google Cloud Avançada.
gcloud config set project PROJECT_ID
gcloud run deploy --port=8000
```
## Estrutura do Projeto

- `app.py`: Arquivo principal da aplicação FastAPI.
- `models.py`: Modelos do banco de dados (SQLAlchemy).
- `schemas.py`: Schemas de validação (Pydantic).
- `database.py`: Configuração do banco de dados SQLite.
- `routers/`: Diretório com os arquivos de rotas (alunos, cursos, matrículas).
- `requirements.txt`: Lista de dependências do projeto.

O banco de dados SQLite será criado automaticamente como `escola.db` na primeira execução. Para reiniciar o banco, basta apagar o arquivo `escola.db` (isso apagará todos os dados).

## Autenticação no Google Cloud Avançado

Para utilizar recursos do Google Cloud (como Cloud Run, Cloud SQL, Storage, etc.), é necessário autenticar sua conta localmente. Siga os passos abaixo:

1. Instale o Google Cloud SDK: [https://cloud.google.com/sdk/docs/install](https://cloud.google.com/sdk/docs/install)
2. Faça login na sua conta Google:
   ```sh
   gcloud auth login
   ```
3. Selecione o projeto desejado:
   ```sh
   gcloud config set project SEU_ID_DO_PROJETO
   ```
4. (Opcional) Defina a região padrão:
   ```sh
   gcloud config set compute/region southamerica-east1
   ```
5. (Opcional) Defina a zona padrão:
   ```sh
   gcloud config set compute/zone southamerica-east1-a
   ```
6. Verifique se está autenticado:
   ```sh
   gcloud auth list
   ```
7. (Opcional) Autentique para uso em containers/Docker:
   ```sh
   gcloud auth configure-docker
   ```

## Deploy no Cloud Run

1. Faça o build e push da imagem para o Google Container Registry ou Artifact Registry:
   ```sh
   docker build -t gcr.io/SEU_ID_DO_PROJETO/fastapi-app:latest .
   docker push gcr.io/SEU_ID_DO_PROJETO/fastapi-app:latest
   ```
2. Faça o deploy no Cloud Run:
   ```sh
   gcloud run deploy fastapi-app \
     --image gcr.io/SEU_ID_DO_PROJETO/fastapi-app:latest \
     --platform managed \
     --region southamerica-east1 \
     --port 8000 \
     --allow-unauthenticated
   ```
   - O parâmetro `--port 8000` garante que o serviço será exposto na porta 8000.
   - Altere a região se necessário.
3. Acesse a URL gerada pelo Cloud Run para testar sua API.

## Mais tutoriais DevOps

O tutorial completo de DevOps (incluindo exemplo com Flask, Dockerfile detalhado, push para Artifact Registry, dicas para WSL, etc.) foi movido para o arquivo [TUTORIAL_DEVOPS.md](TUTORIAL_DEVOPS.md). Lá você encontra um passo a passo para iniciantes, cobrindo desde a criação de uma aplicação simples até o deploy na Google Cloud Platform.

---

Se encontrar algum erro ou tiver sugestões, abra uma issue ou envie um pull request!
