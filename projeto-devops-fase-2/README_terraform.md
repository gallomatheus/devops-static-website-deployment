# 🚀 Laboratório DevOps - Projeto 2: Automatizando a Infraestrutura AWS com Terraform

## 🎯 Visão Geral

### O que vamos construir?
Neste etapa do projeto, temos como objetivo escrever toda a infraestrutura que precisamos por código utilizando o Terraform, e aplicando em produção.

### Conhecimentos e práticas:
✅ Criar e configurar uma instância EC2 com Terraform

✅ Provisionar um ECR para armazenar imagens Docker

✅ Criar Security Groups com regras de entrada e saída

✅ Atrelar um IAM Profile à EC2 de forma segura

✅ Substituir cliques manuais por Infraestrutura como Código (IaC)

✅ Preparar o terreno para a automação completa com CI/CD usando GitHub Actions


### Passo 1: Instalar o Terraform no ambiente
1. Atualizar sistema
```bash
sudo apt update
```

2. Instalar dependências
```bash
sudo apt install -y gnupg software-properties-common curl
```

3. Adicionar chave HashiCorp
```bash
curl -fsSL https://apt.releases.hashicorp.com/gpg | \
sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
```

4. Adicionar repositório
```bash
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com \
$(lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list
```

5. Instalar Terraform
```bash
sudo apt update
sudo apt install terraform -y
```

6. Verificar instalação
```bash
terraform -version
```

### Passo 2: Construir os arquivos do Terraform

- provider.tf
- backend.tf
- ec2.tf
- ecr-tf

### No VSCode, criaremos 4 novos arquivos com extensão .tf:

#### provider.tf 
```bash
provider "aws" {
  region = "us-east-2"
}
```

#### backend.tf
```bash
# state.tf
terraform {
  backend "s3" {
    bucket  = "terraform-state-matheusg"
    key     = "site/terraform.tfstate"
    region  = "us-east-2"
    encrypt = true
  }
}
```

#### ec2.tf
```bash
#ECR Repository
resource "aws_ecr_repository" "ecr_site" {
  name                 = "site_prod"
  image_tag_mutability = "MUTABLE"
}
```

#### ecr-tf.tf
```bash
resource "aws_instance" "website_server" {
  ami                    = "ami-0b016c703b95ecbe4" #Amazon Linux 2 AMI
  instance_type          = "t3.micro"
  key_name               = "chave-site-prod"
  vpc_security_group_ids = [aws_security_group.website_sg.id]
  iam_instance_profile   = "ECR-EC2-Role"

  tags = {
    Name        = "website-server"
    Provisioned = "Terraform"
    Cliente     = "Matheus"
  }
}

## Security Group
resource "aws_security_group" "website_sg" {
  name   = "website-sg"
  vpc_id = "vpc-0ff60a695425883cf"
  tags = {
    Name        = "website-sg"
    Provisioned = "Terraform"
    Cliente     = "Matheus"
  }
}

resource "aws_vpc_security_group_ingress_rule" "allow_ssh" {
  security_group_id = aws_security_group.website_sg.id
  cidr_ipv4         = "seu-ip/32" # DIGITAR IPV4
  from_port         = 22
  ip_protocol       = "tcp"
  to_port           = 22
}

resource "aws_vpc_security_group_ingress_rule" "allow_http" {
  security_group_id = aws_security_group.website_sg.id
  cidr_ipv4         = "0.0.0.0/0"
  from_port         = 80
  ip_protocol       = "tcp"
  to_port           = 80
}

resource "aws_vpc_security_group_ingress_rule" "allow_https" {
  security_group_id = aws_security_group.website_sg.id
  cidr_ipv4         = "0.0.0.0/0"
  from_port         = 443
  ip_protocol       = "tcp"
  to_port           = 443
}

resource "aws_vpc_security_group_egress_rule" "allow_all_outbound" {
  security_group_id = aws_security_group.website_sg.id

  cidr_ipv4   = "0.0.0.0/0"
  ip_protocol = -1
}
```


### Em caso de dúvidas consultar a documentação do Terraform

https://registry.terraform.io/providers/hashicorp/aws/latest/docs



### Passo 3: Criar um bucket S3 na AWS.

Nome: terraform-state-matheusg
Demais campos default, bloqueando somente acesso de outros usuários.

<img width="1536" height="749" alt="image" src="https://github.com/user-attachments/assets/c7d87ccf-5c5a-48b0-a5a5-2f47df5460f0" />


### Passo 4: Inicializar o Terraform a partir do diretório em que estão os arquivos e seguir com os comandos.

<img width="1065" height="658" alt="image" src="https://github.com/user-attachments/assets/ff7225b4-abf8-404b-a794-f6966732506d" />

#### Comando para formatar e identar os arquivos criados:
```bash
terraform fmt
```

#### Comando para validar se há erros nos arquivos criados:
```bash
terraform validate
```

#### Comando para o Terraform "buildar" a configuração parametrizada:
```bash
terraform plan
```
<img width="1460" height="177" alt="image" src="https://github.com/user-attachments/assets/ddbd628b-a5cb-4463-9b82-13a32e22992c" />


#### Logo após verificarmos todos os parâmetros, podemos aplicar essas configurações e o Terraform irá criar a instância:
```bash
terraform apply
```

<img width="1114" height="538" alt="image" src="https://github.com/user-attachments/assets/d3cd8415-2778-4fc3-85d5-ffc2ec2b2c63" />


#### Pronto! O Terraform criou a infraestrutura que codamos via código:

<img width="1271" height="690" alt="image" src="https://github.com/user-attachments/assets/b74bbf07-76e7-4399-b888-87c33e110251" />


#### Para verificar se a instância EC2 está acessível use o comando:
```bash
chmod 400 chave-site-prod.pem
```

```bash
ssh -i chave-site-prod.pem ec2-user@IP_DA_MAQUINA
```


<img width="1101" height="298" alt="image" src="https://github.com/user-attachments/assets/003ba4b5-eaf1-480e-a386-8d8d0d037c2c" />


#### Para deletar todos os recursos criados pelo Terraform na AWS, use o comando:
```bash
terraform destroy
```
<img width="1021" height="581" alt="image" src="https://github.com/user-attachments/assets/faa40f16-44c8-4cdf-b819-2b4ffab81f14" />




