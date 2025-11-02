#  Desafio DIO - Implementando sua Primeira Stack com AWS CloudFormation

##  Objetivo do Desafio
Este desafio faz parte do curso **AWS Cloud Foundations - DIO**, ministrado por **Alexsandro Lechner**, e tem como propósito aplicar os conceitos de **Infraestrutura como Código (IaC)** utilizando **AWS CloudFormation**.

O objetivo principal é **implementar uma Stack completa** que cria recursos essenciais na nuvem AWS, documentar o processo e demonstrar o entendimento técnico adquirido ao longo das aulas.

---

##  O que é o AWS CloudFormation?
O **AWS CloudFormation** é um serviço que permite criar e gerenciar recursos da AWS através de modelos (templates) em formato **YAML** ou **JSON**.  
Esses modelos descrevem, de forma automatizada, toda a infraestrutura que será provisionada, garantindo **padronização, reprodutibilidade e controle** do ambiente em nuvem.

Com o CloudFormation, é possível implementar **infraestrutura como código (IaC)**, reduzindo erros humanos e acelerando a entrega de ambientes prontos para uso.

---

##  Recursos Criados na Stack
O template criado neste desafio realiza o provisionamento automático dos seguintes recursos:

-  **Instância EC2:** máquina virtual configurável, usada para hospedar aplicações ou scripts;  
-  **Bucket S3:** repositório para armazenamento de objetos e arquivos;  
-  **Usuário IAM:** usuário com permissões gerenciadas para acesso à AWS;  
-  **Grupo IAM:** grupo de permissões vinculado ao usuário IAM;  
-  **Security Group:** grupo de segurança que permite o acesso SSH (porta 22) à instância EC2.

---

##  Template CloudFormation (YAML)

Abaixo está o conteúdo completo do template utilizado neste desafio:

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: CloudFormation Template to create an EC2 instance, IAM user, and S3 bucket

Parameters:
  InstanceType:
    Type: String
    Default: t2.micro
    Description: EC2 instance type
    AllowedValues:
      - t2.micro
      - t3.micro
    ConstraintDescription: Must be a valid EC2 instance type.

Resources:
  # Criação do bucket S3
  S3Bucket:
    Type: 'AWS::S3::Bucket'
    Properties:
      BucketName: S3-FOUNDATION

  # Criação do grupo IAM
  IAMGroup:
    Type: 'AWS::IAM::Group'
    Properties:
      GroupName: GPO-ADMIN-LAB

  # Criação do usuário IAM
  IAMUser:
    Type: 'AWS::IAM::User'
    Properties:
      UserName: alexsandro.lechner
      Groups:
        - !Ref IAMGroup

  # Criação da instância EC2
  EC2Instance:
    Type: 'AWS::EC2::Instance'
    Properties:
      InstanceType: !Ref InstanceType
      ImageId: !FindInMap [UbuntuMap, !Ref "AWS::Region", UbuntuAMI]
      KeyName: your-key-pair-name  # Substitua pelo seu nome de par de chaves
      SecurityGroupIds:
        - !Ref EC2SecurityGroup
      UserData:
        Fn::Base64: !Sub |
          #!/bin/bash
          apt-get update
          apt-get install -y python3-pip

  # Criação do grupo de segurança para a instância EC2
  EC2SecurityGroup:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupDescription: Enable SSH access
      VpcId: vpc-040a4ffd0374c4cf3
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: '22'
          ToPort: '22'
          CidrIp: 0.0.0.0/0  # Permitir acesso SSH de qualquer IP

Mappings:
  # Mapeamento para AMIs do Ubuntu
  UbuntuMap:
    us-east-1:
      UbuntuAMI: ami-0c55b159cbfafe1f0  # Exemplo de AMI para Ubuntu 20.04 na região us-east-1
    us-west-2:
      UbuntuAMI: ami-0dba2cbf4e3c2e7b1  # Exemplo de AMI para Ubuntu 20.04 na região us-west-2

Outputs:
  InstanceId:
    Description: The Instance ID
    Value: !Ref EC2Instance
  S3BucketName:
    Description: The name of the S3 bucket
    Value: !Ref S3Bucket
  IAMUserName:
    Description: The IAM User Name
    Value: !Ref IAMUser

```

##  Aprendizados e Insights

Durante este desafio, pude compreender de forma prática os seguintes pontos:

- A importância da **Infraestrutura como Código (IaC)** para automação de ambientes na nuvem;  
- O funcionamento dos blocos **Parameters**, **Resources**, **Mappings** e **Outputs** no CloudFormation;  
- Como o CloudFormation gerencia dependências entre recursos, criando-os na ordem correta;  
- Como parametrizar valores (como tipo da instância EC2) para permitir reutilização do template;  
- O uso de **UserData** para inicializar instâncias EC2 automaticamente com scripts de configuração;  
- A facilidade de documentar e versionar toda a infraestrutura através do **GitHub**.  

---

##  Ferramentas e Tecnologias Utilizadas

- **AWS CloudFormation**  
- **Amazon EC2**  
- **Amazon S3**  
- **AWS IAM**  
- **AWS Management Console**  
- **GitHub** (para versionamento e documentação)  

---

##  Autora

**Silvia Toledo**  
 


---

## 🔗 Referências

- [Documentação Oficial do AWS CloudFormation](https://docs.aws.amazon.com/cloudformation/index.html)  
- [Documentação do Amazon EC2](https://docs.aws.amazon.com/ec2/index.html)  
- [Documentação do Amazon S3](https://docs.aws.amazon.com/s3/index.html)  
- [Documentação do AWS IAM](https://docs.aws.amazon.com/iam/index.html)




