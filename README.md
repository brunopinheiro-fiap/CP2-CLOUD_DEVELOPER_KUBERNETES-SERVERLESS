# 📦 CP2 CLOUD_DEVELOPER_KUBERNETES-SERVERLESS: Microserviço com MySQL e Persistência

Este projeto tem como objetivo demonstrar o uso de **Docker** para construir, versionar, persistir e publicar um microserviço com banco de dados **MySQL**.

---

## 📁 Estrutura do Projeto
meu-microservico/
├── app.py
├── requirements.txt
├── Dockerfile

---

## 📦 Parte 1: Persistência com Volumes

### 🔧 Criando um volume Docker nomeado

```bash
docker volume create mysql_data

🔍 Verificando informações do volume
docker volume inspect mysql_data

📌 Justificativa: Docker Volume vs Bind Mount
Escolha: Docker Volume

Motivos:
✅ Gerenciado pelo Docker: Não depende da estrutura do host.
✅ Mais seguro: Menos exposto a erros de permissões ou caminhos inválidos.
✅ Portável: Funciona de forma consistente em qualquer sistema.
✅ Fácil de fazer backup e restaurar.
✅ Melhor performance em Windows/macOS.
✅ Suporte a drivers externos.
Bind mounts são mais indicados para desenvolvimento local com necessidade de edição em tempo real.

🛢️ Parte 2: Container com MySQL
🚀 Criando o container MySQL com volume

docker run -d \
  --name mysql-container \
  -e MYSQL_ROOT_PASSWORD=senha_segura \
  -e MYSQL_DATABASE=meu_banco \
  -p 3306:3306 \
  -v mysql_data:/var/lib/mysql \
  mysql:8.0

  💻 Acessando o MySQL no container

  docker exec -it mysql-container mysql -u root -p

  🧱 Criando tabela e inserindo dados
Acesse o terminal MySQL e execute:

USE meu_banco;

CREATE TABLE usuarios (
  id INT AUTO_INCREMENT PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL,
  data_criacao TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

INSERT INTO usuarios (nome, email)
VALUES ('Usuário Teste', 'teste@exemplo.com');

🧱 Parte 3: Imagem Personalizada
🏗️ Salvando o estado do container como imagem

docker commit mysql-container meu-mysql:v1
docker commit mysql-container meu-mysql:v2

🔍 Verificando as imagens criadas

docker images | grep meu-mysql

☁️ Parte 4: Docker Hub
🔐 Login no Docker Hub

docker login

🏷️ Marcando imagens com seu usuário do Docker Hub

docker tag meu-mysql:v1 brunopinheiro/meu-mysql:v1
docker tag meu-mysql:v2 brunopinheiro/meu-mysql:v2

📤 Enviando imagens para o Docker Hub

docker push brunopinheiro/meu-mysql:v1
docker push brunopinheiro/meu-mysql:v2

🔁 Parte 5: Teste de Persistência
📋 Verificando os dados antes de remover o container

docker exec -it mysql-container \
  mysql -u root -p -e "SELECT * FROM meu_banco.usuarios;"

🧹 Parando e removendo o container

docker stop mysql-container
docker rm mysql-container

🔄 Criando um novo container com o mesmo volume

docker run -d \
  --name mysql-container-novo \
  -e MYSQL_ROOT_PASSWORD=senha_segura \
  -e MYSQL_DATABASE=meu_banco \
  -p 3306:3306 \
  -v mysql_data:/var/lib/mysql \
  mysql:8.0

✅ Verificando que os dados persistiram

docker exec -it mysql-container-novo \
  mysql -u root -p -e "SELECT * FROM meu_banco.usuarios;"

✅ Conclusão
Através do uso de volumes Docker, foi possível garantir a persistência dos dados mesmo após a remoção do container original.

Além disso, foi demonstrada a criação de imagens versionadas, o envio para o Docker Hub e a aplicação de boas práticas de containerização.

Desenvolvido como parte da avaliação prática de CP2
CLOUD_DEVELOPER_KUBERNETES-SERVERLESS 💙🐳