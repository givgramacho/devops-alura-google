# Tutorial DevOps para Iniciantes: Docker e Google Cloud Run

---

Olá, futuros especialistas em DevOps! Este tutorial foi criado para guiar você, passo a passo, desde a criação de um contêiner Docker de uma aplicação simples até o deploy na Google Cloud Platform (GCP) usando o Google Cloud Run. Prepare-se para colocar as mãos na massa e ver sua aplicação rodando na nuvem!

## Sumário
1. [Instalar o Docker](#1-instalar-o-docker)
2. [Criar uma Aplicação Simples (Python Flask)](#2-criar-uma-aplicação-simples)
3. [Criar o Dockerfile](#3-criar-o-dockerfile)
4. [Construir a Imagem Docker](#4-construir-a-imagem-docker)
5. [Testar a Aplicação Localmente](#5-testar-a-aplicação-localmente)
6. [Configurar o Google Cloud](#6-configurar-o-google-cloud)
7. [Enviar a Imagem para o Google Container Registry (ou Artifact Registry)](#7-enviar-a-imagem-para-o-google-container-registry-ou-artifact-registry)
8. [Fazer o Deploy no Cloud Run](#8-fazer-o-deploy-no-cloud-run)
9. [Acessar a Aplicação](#9-acessar-a-aplicação)

---

## 1. Instalar o Docker

O Docker é a ferramenta que nos permitirá empacotar nossa aplicação e suas dependências em um contêiner leve e portátil.

* **Baixe e instale o Docker Desktop:**
    Acesse o [Docker Desktop](https://www.docker.com/products/docker-desktop/) e siga as instruções de instalação para o seu sistema operacional (Windows, macOS ou Linux). Para usuários de **Windows**, certifique-se de que o **WSL 2** esteja ativado, pois é o backend recomendado para o Docker Desktop.

* **Verifique a instalação:**
    Abra o terminal (PowerShell, CMD, ou terminal WSL) e execute:
    ```bash
    docker --version
    docker compose version
    ```
    Você deve ver as versões instaladas do Docker Engine e Docker Compose.

* **Dica Essencial (WSL):** Se você usa **WSL (Windows Subsystem for Linux)**, pode instalar o Docker Desktop no Windows e configurar para que os comandos `docker` funcionem nativamente dentro do seu terminal WSL. Isso é feito nas configurações do Docker Desktop (`Settings > Resources > WSL Integration`). Selecione sua distribuição Ubuntu para habilitar.

---

## 2. Criar uma Aplicação Simples

Vamos criar uma aplicação web básica usando Flask, um microframework Python.

* **Crie um diretório para o projeto:**
    ```bash
    mkdir meu-projeto-docker
    cd meu-projeto-docker
    ```

* **Crie um arquivo chamado `app.py`:**
    ```python
    from flask import Flask

    app = Flask(__name__)

    @app.route('/')
    def hello():
        return "Hello, World!"

    if __name__ == '__main__':
        # Rodar a aplicação na porta 8000 e permitir acesso de qualquer IP
        app.run(host='0.0.0.0', port=8000)
    ```
    **Nota Importante:** `host='0.0.0.0'` é crucial para que a aplicação dentro do contêiner seja acessível de fora. Se você usar `127.0.0.1` ou `localhost`, ela só estará acessível dentro do contêiner.

* **Crie um arquivo `requirements.txt`:**
    ```ini
    Flask==2.0.1
    ```
    Este arquivo lista as dependências do nosso projeto Python.

---

## 3. Criar o Dockerfile

O Dockerfile é um conjunto de instruções que o Docker usa para construir a imagem do nosso contêiner.

* **Crie um arquivo chamado `Dockerfile` (sem extensão):**
    ```dockerfile
    # Usar a imagem base oficial do Python, versão 3.9 slim (menor tamanho)
    FROM python:3.9-slim

    # Definir o diretório de trabalho dentro do contêiner
    WORKDIR /app

    # Copiar o arquivo de requisitos para o diretório de trabalho
    COPY requirements.txt .
    RUN pip install --no-cache-dir -r requirements.txt

    # Copiar o resto dos arquivos da aplicação para o diretório de trabalho
    COPY . .

    # Expor a porta em que a aplicação Flask irá escutar
    EXPOSE 8000

    # Comando para rodar a aplicação quando o contêiner for iniciado
    CMD ["python", "app.py"]
    ```
    **Melhoria Essencial:** A ordem das instruções `COPY` e `RUN pip install` foi otimizada para aproveitar o cache de camadas do Docker. Se apenas o código da aplicação mudar (`app.py`), o `pip install` não precisará ser executado novamente, agilizando as builds futuras. Adicionei `--no-cache-dir` ao `pip install` para economizar espaço na imagem final.

---

## 4. Construir a Imagem Docker

Agora, vamos transformar nosso Dockerfile em uma imagem Docker executável.

* No terminal, no diretório `meu-projeto-docker`, execute o seguinte comando:
    ```bash
    docker build -t meu-app .
    ```
    * `-t meu-app`: Define uma tag (nome) para a sua imagem, facilitando a referência.
    * `.`: Indica que o Dockerfile está no diretório atual.

* **Verifique se a imagem foi criada:**
    ```bash
    docker images
    ```
    Você deve ver `meu-app` listado.

---

## 5. Testar a Aplicação Localmente

É fundamental testar sua aplicação no contêiner localmente antes de enviá-la para a nuvem.

* **Execute o contêiner:**
    ```bash
    docker run -p 8000:8000 meu-app
    ```
    * `-p 8000:8000`: Mapeia a porta 8000 do seu host (sua máquina) para a porta 8000 dentro do contêiner. Isso permite que você acesse a aplicação do seu navegador.

* **Acesse a aplicação:**
    Abra seu navegador e vá para `http://localhost:8000`. Você deve ver "Hello, World!".

---

## 6. Configurar o Google Cloud

Para fazer o deploy na GCP, precisamos de um projeto e do Google Cloud SDK configurado.

* **Crie uma conta no Google Cloud** (se ainda não tiver) e faça login. Acesse [cloud.google.com](https://cloud.google.com/).

* **Crie um novo projeto:**
    No Console do Google Cloud, clique em "Selecionar um projeto" (no cabeçalho, ao lado do logo do Google Cloud) e depois em "Novo Projeto". Dê um nome significativo ao seu projeto, por exemplo, `meu-app-devops`. Anote o **ID do Projeto** (será algo como `meu-app-devops-xxxxxx`).

* **Ative a API do Cloud Run:**
    No menu de navegação lateral do Console do Google Cloud, vá para "APIs e serviços" > "Biblioteca". Procure por "Cloud Run Admin API" e "Cloud Build API" (será útil mais tarde, mas por agora focamos na do Cloud Run) e certifique-se de que ambas estejam **ativadas**.

* **Instale o Google Cloud SDK (gcloud CLI):**
    Este é um passo **crucial e muitas vezes negligenciado em tutoriais de Docker/Cloud Run**. O `gcloud CLI` é a ferramenta de linha de comando para interagir com a GCP.
    Siga as instruções oficiais para o seu sistema operacional: [Instalar Google Cloud SDK](https://cloud.google.com/sdk/docs/install).

    * **Para usuários WSL (Ubuntu):** A melhor prática é instalar o `gcloud CLI` **dentro do seu ambiente WSL Ubuntu**. Isso garante compatibilidade total com os comandos Linux e variáveis de ambiente. Siga as instruções para Debian/Ubuntu.

* **Autentique-se no Google Cloud:**
    Abra seu terminal (PowerShell/CMD se instalou no Windows, ou terminal WSL se instalou no Ubuntu) e execute:
    ```bash
    gcloud auth login
    ```
    Este comando abrirá uma página no seu navegador para você fazer login com sua conta Google. Permita o acesso.

* **Defina o projeto que você criou:**
    Substitua `[PROJECT_ID]` pelo ID real do seu projeto GCP.
    ```bash
    gcloud config set project [PROJECT_ID]
    ```
    **Verifique a configuração:**
    ```bash
    gcloud config list
    ```
    Confirme se o `project` está definido corretamente.

---

## 7. Enviar a Imagem para o Google Container Registry (ou Artifact Registry)

Para o Cloud Run acessar sua imagem Docker, ela precisa estar hospedada em um registro de contêiner. O Google Container Registry (GCR) ou o Artifact Registry são as opções nativas e preferenciais na GCP. **O Artifact Registry é o serviço mais recente e recomendado pela Google.**

* **Escolha seu Registro (Recomendação: Artifact Registry):**

    * **Opção A: Google Container Registry (GCR - Legado)**
        Tag a imagem:
        ```bash
        docker tag meu-app gcr.io/[PROJECT_ID]/meu-app
        ```
        Faça o login no GCR (apenas uma vez):
        ```bash
        gcloud auth configure-docker
        ```
        Envie a imagem:
        ```bash
        docker push gcr.io/[PROJECT_ID]/meu-app
        ```
        **Verifique a imagem no GCR:** No Console do Google Cloud, navegue até "Container Registry".

    * **Opção B: Google Artifact Registry (Recomendado)**
        1.  **Crie um repositório no Artifact Registry:**
            ```bash
            gcloud artifacts repositories create my-python-repo --repository-format=docker --location=us-central1 --description="Docker repository for Python apps"
            ```
            Substitua `us-central1` pela região que você pretende usar para o Cloud Run.

        2.  **Tag a imagem:**
            ```bash
            docker tag meu-app us-central1-docker.pkg.dev/[PROJECT_ID]/my-python-repo/meu-app
            ```
            **ATENÇÃO:** O formato da tag muda para incluir a região e o nome do repositório.

        3.  **Configure o Docker para autenticar com o Artifact Registry:**
            ```bash
            gcloud auth configure-docker us-central1-docker.pkg.dev
            ```
            **ATENÇÃO:** O domínio de autenticação é específico do Artifact Registry.

        4.  **Envie a imagem:**
            ```bash
            docker push us-central1-docker.pkg.dev/[PROJECT_ID]/my-python-repo/meu-app
            ```
            **Verifique a imagem no Artifact Registry:** No Console do Google Cloud, navegue até "Artifact Registry".

---

## 8. Fazer o Deploy no Cloud Run

Com a imagem no registro, podemos implantá-la no Cloud Run, um serviço de computação serverless que escala automaticamente.

* **Realize o deploy da aplicação:**
    ```bash
    gcloud run deploy meu-app --image [ENDERECO_DA_IMAGEM] --platform managed --region us-central1 --allow-unauthenticated
    ```
    * Substitua `[ENDERECO_DA_IMAGEM]` pela tag completa da sua imagem (Ex: `gcr.io/[PROJECT_ID]/meu-app` ou `us-central1-docker.pkg.dev/[PROJECT_ID]/my-python-repo/meu-app`).
    * `--platform managed`: Indica que você está usando o serviço gerenciado do Cloud Run (não Cloud Run for Anthos).
    * `--region us-central1`: Escolha a região da GCP onde sua aplicação será implantada. Mantenha a consistência com a região do seu Artifact Registry, se aplicável.
    * `--allow-unauthenticated`: Permite que a aplicação seja acessada publicamente, sem necessidade de autenticação. **Para aplicações de produção, considere remover esta flag e configurar autenticação adequada.**

    Siga as instruções no terminal. Ele perguntará sobre a região, permitir invocações não autenticadas, etc. Confirme as opções. Quando o deploy for concluído, você receberá uma URL para acessar sua aplicação.

---

## 9. Acessar a Aplicação

* Abra a URL fornecida no terminal pelo comando `gcloud run deploy`.
    Você deve ver "Hello, World!" na sua aplicação hospedada no Google Cloud.

--- 