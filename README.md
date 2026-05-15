# Projeto de deploy de aplicação HTML estática utilizando Docker, Nginx e AWS EC2 com armazenamento da imagem no Amazon ECR.

## 📋 Tecnologias utilizadas
- Oracle VirtualBox
- Docker
- Nginx
- AWS EC2
- AWS ECR
- Linux Ubuntu
- Git
- Terraform
- GitHub Actions (CI/CD)


## 🔧 Pré-requisitos:

### Máquina Virtual com Ubuntu com 25 GB de armazenamento, 8 GB de RAM e 6 CPUs

<img width="1312" height="788" alt="image" src="https://github.com/user-attachments/assets/d567b878-f14b-4ba3-9135-87a29771140d" />



### 1. Docker Desktop

Windows/Mac: Baixe em docker.com/products/docker-desktop

Linux: Instale via terminal:
```bash
curl -fsSL https://get.docker.com -o get-docker.sh
```
OU
```bash
sudo apt update
sudo apt install docker.io -y
```

Para verificar a instalação:
```bash
docker --version
```

### 2. AWS CLI

```bash
sudo apt install awscli -y
```

Para verificar:
```bash
aws --version
aws configure
```

### 3. Conta AWS
Crie uma conta gratuita em aws.amazon.com

Conectar na EC2: 
```bash
ssh -i chave.pem ubuntu@IP_PUBLICO
```

⚠️ Importante: Alguns recursos podem gerar custos. Use o Free Tier quando possível

### 4. Editor de Código
Recomendado: Visual Studio Code
```bash
sudo apt update
sudo apt install code
```





### Estrutura do Projeto

```
meu-projeto/
├── website/
│   ├── index.html
│   ├── styles.css
│   ├── script.js
│   └── assets/
│       └── (imagens, fontes, etc.)
└── Dockerfile (vamos criar)
```

### 🏗️ Arquitetura do Projeto

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  Código Local   │────▶│   Docker Image  │────▶│    Amazon ECR   │
│  (HTML/CSS/JS)  │     │   (Container)   │     │   (Registry)    │
└─────────────────┘     └─────────────────┘     └─────────────────┘
                                                          │
                                                          ▼
                        ┌─────────────────┐     ┌─────────────────┐
                        │    Browser      │◀────│    Amazon EC2   │
                        │  (User Access)  │     │   (Container)   │
                        └─────────────────┘     └─────────────────┘
```

