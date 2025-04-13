# 📦 CP2 CLOUD_DEVELOPER_KUBERNETES-SERVERLESS: Microserviço com MySQL e Persistência

Este projeto tem como objetivo demonstrar o uso de **Docker** para construir, versionar, persistir e publicar um microserviço com banco de dados **MySQL**.

---

## 📁 Estrutura do Projeto

A estrutura do projeto é a seguinte:

```
meu-microservico/
├── app.py
├── requirements.txt
├── Dockerfile
```

---

## 📦 Parte 1: Persistência com Volumes

### 🔧 Criando um volume Docker nomeado

Execute o seguinte comando para criar um volume Docker nomeado:

```bash
docker volume create mysql_dados
```

Para verificar informações do volume, use:

```bash
docker volume inspect mysql_dados
```

#### 📌 Justificativa: Docker Volume vs Bind Mount

**Escolha:** Docker Volume

**Motivos:**
- ✅ Gerenciado pelo Docker: Não depende da estrutura do host.
- ✅ Mais seguro: Menos exposto a erros de permissões ou caminhos inválidos.
- ✅ Portável: Funciona de forma consistente em qualquer sistema.
- ✅ Fácil de fazer backup e restaurar.
- ✅ Melhor performance em Windows/macOS.
- ✅ Suporte a drivers externos.

**Nota:** Bind mounts são mais indicados para desenvolvimento local com necessidade de edição em tempo real.

---

## 🛢️ Parte 2: Container com MySQL

### 🚀 Criando o container MySQL com volume

Use o comando abaixo para criar o container MySQL com volume:

```bash
docker run -d \
  --name mysql-container \
  -e MYSQL_ROOT_PASSWORD=minha_senha_forte \
  -e MYSQL_DATABASE=my_database \
  -p 3306:3306 \
  -v mysql_data:/var/lib/mysql \
  mysql:8.0
```

### 💻 Acessando o MySQL no container

Para acessar o MySQL no container, execute:

```bash
docker exec -it mysql-container mysql -u root -p
```

### 🧱 Criando tabela e inserindo dados

No terminal MySQL, execute os seguintes comandos:

```sql
USE my_database;

CREATE TABLE usuarios (
  id INT AUTO_INCREMENT PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL,
  data_criacao TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

INSERT INTO usuarios (nome, email)
VALUES ('Usuário Bruno', 'usuario@bruno.com.br');
```

---

## 🧱 Parte 3: Imagem Personalizada

### 🏗️ Salvando o estado do container como imagem

Para salvar o estado do container como imagem, use:

```bash
docker commit mysql-container meu-mysql:v1
docker commit mysql-container meu-mysql:v2
```

### 🔍 Verificando as imagens criadas

Para listar as imagens criadas, execute:

```bash
docker images | grep meu-mysql
```

---

## ☁️ Parte 4: Docker Hub

### 🔐 Login no Docker Hub

Faça login no Docker Hub com o comando:

```bash
docker login
```

### 🏷️ Marcando imagens com seu usuário do Docker Hub

Marque as imagens com seu usuário do Docker Hub:

```bash
docker tag meu-mysql:v1 bpsbrunopinheiro/meu-mysql:v1
docker tag meu-mysql:v2 bpsbrunopinheiro/meu-mysql:v2
```

### 📤 Enviando imagens para o Docker Hub

Envie as imagens para o Docker Hub:

```bash
docker push bpsbrunopinheiro/meu-mysql:v1
docker push bpsbrunopinheiro/meu-mysql:v2
```
Link para as imagens no DockerHub

---

## 🔁 Parte 5: Teste de Persistência

### 📋 Verificando os dados antes de remover o container

Para verificar os dados no container, execute:

```bash
docker exec -it mysql-container \
  mysql -u root -p -e "SELECT * FROM meu_banco.usuarios;"
```

### 🧹 Parando e removendo o container

Pare e remova o container com os comandos:

```bash
docker stop mysql-container
docker rm mysql-container
```

### 🔄 Criando um novo container com o mesmo volume

Crie um novo container utilizando o mesmo volume:

```bash
docker run -d \
  --name novo-container-mysql \
  -e MYSQL_ROOT_PASSWORD=minha_senha_forte \
  -e MYSQL_DATABASE=my_database \
  -p 3306:3306 \
  -v mysql_data:/var/lib/mysql \
  mysql:8.0
```

### ✅ Verificando que os dados persistiram

Verifique se os dados persistiram:

```bash
docker exec -it novo-container-mysql \
  mysql -u root -p -e "SELECT * FROM meu_banco.usuarios;"
```

---

## ✅ Conclusão

Através do uso de volumes Docker, foi possível garantir a persistência dos dados mesmo após a remoção do container original.

Além disso, foi demonstrada a criação de imagens versionadas, o envio para o Docker Hub e a aplicação de boas práticas de containerização.

Desenvolvido como parte da avaliação prática de CP2
CLOUD_DEVELOPER_KUBERNETES-SERVERLESS 💙🐳