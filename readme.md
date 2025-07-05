# Imersão DevOps - Alura Google Cloud

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

## Pré-requisitos

- [Python 3.10 ou superior instalado](https://www.python.org/downloads/)
- [Git](https://git-scm.com/downloads)
- [Docker](https://www.docker.com/get-started/)

## Passos para subir o projeto

1. **Faça o download do repositório:**
   [Clique aqui para realizar o download](https://github.com/guilhermeonrails/imersao-devops/archive/refs/heads/main.zip)

2. **Crie um ambiente virtual:**
   ```sh
   python3 -m venv ./venv
   ```

3. **Ative o ambiente virtual:**
   - No Linux/Mac:
     ```sh
     source venv/bin/activate
     ```
   - No Windows:
     ```sh
     venv\Scripts\activate
     ```

4. **Instale as dependências:**
   ```sh
   pip install -r requirements.txt
   ```

5. **Execute a aplicação:**
   ```sh
   uvicorn app:app --reload
   ```

6. **Acesse a documentação interativa:**

   Abra o navegador e acesse:  
   [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs)

   Aqui você pode testar todos os endpoints da API de forma interativa.

---

## Estrutura do Projeto

- `app.py`: Arquivo principal da aplicação FastAPI.
- `models.py`: Modelos do banco de dados (SQLAlchemy).
- `schemas.py`: Schemas de validação (Pydantic).
- `database.py`: Configuração do banco de dados SQLite.
- `routers/`: Diretório com os arquivos de rotas (alunos, cursos, matrículas).
- `requirements.txt`: Lista de dependências do projeto.

---

- O banco de dados SQLite será criado automaticamente como `escola.db` na primeira execução.
- Para reiniciar o banco, basta apagar o arquivo `escola.db` (isso apagará todos os dados).

---

## Como rodar a aplicação

### Aula 1. Ambiente local (venv) com variáveis de ambiente

**Arquivos necessários:**
- `app.py`: Código principal da aplicação FastAPI.
- `requirements.txt`: Lista de dependências Python.

Esses arquivos devem estar no diretório raiz do projeto. O `requirements.txt` pode ser criado com:
```sh
pip freeze > requirements.txt
```
O `app.py` deve conter a definição da sua aplicação FastAPI.

**Passos:**
1. Crie um ambiente virtual:
   ```sh
   python3 -m venv venv
   ```
2. Ative o ambiente virtual:
   - No Linux/Mac:
     ```sh
     source venv/bin/activate
     ```
   - No Windows:
     ```sh
     venv\Scripts\activate
     ```
3. Instale as dependências:
   ```sh
   pip install -r requirements.txt
   ```
   ```
4. Rode a aplicação:
   ```sh
   uvicorn app:app --reload
   ```
5. Acesse: [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs)

---

### Aula 1.2. Usando apenas Docker

**Arquivos necessários:**
- `Dockerfile`: Define como a imagem Docker será construída.
- `app.py` e `requirements.txt`: Devem estar no mesmo diretório do `Dockerfile`.

O `Dockerfile` pode ser criado assim:
```Dockerfile
FROM python:3.13.5-alpine3.22
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["uvicorn", "app:app", "--host", "0.0.0.0", "--port", "8000"]
```

**Passos:**
1. Construa a imagem:
   ```sh
   docker build -t fastapi-app .
   ```
2. Rode o container:
   ```sh
   docker run -p 8000:8000 fastapi-app
   ```
3. Acesse: [http://localhost:8000/docs](http://localhost:8000/docs)

---

### Aula 2. Usando Docker Compose

**Arquivos necessários:**
- `docker-compose.yml`: Orquestra o(s) serviço(s) Docker.
- `Dockerfile`, `app.py`, `requirements.txt`: Devem estar no mesmo diretório do `docker-compose.yml`.

O `docker-compose.yml` pode ser criado assim:
```yaml
version: "3.8"
services:
  app:
    build: .
    container_name: fastapi-app
    ports:
      - "8000:8000"
    volumes:
      - .:/app
    restart: unless-stopped
```

**Passos:**
1. (Opcional) Crie um arquivo `.env` para variáveis de ambiente e o Compose irá carregá-las automaticamente.
2. Construa e suba os containers:
   ```sh
   docker-compose up --build
   ```
3. Acesse: [http://localhost:8000/docs](http://localhost:8000/docs)

---
