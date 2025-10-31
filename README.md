#  Laboratório: AWS CloudFormation

## Objetivo
Implementar a **primeira Stack** utilizando o **AWS CloudFormation**, compreendendo o funcionamento da **infraestrutura como código (IaC)** e o ciclo de vida das stacks na AWS.  

---

## Entregável
O entregável deste laboratório é **um repositório organizado contendo anotações e insights adquiridos durante a prática**, servindo como **material de apoio para estudos e futuras implementações**.



---


## Objetivos do Laboratório

- Entender o conceito de **Infraestrutura como Código (IaC)**.  
- Criar a **primeira Stack** no CloudFormation.  
- Utilizar **templates em YAML** para provisionar recursos AWS.  
- Explorar o **ciclo de vida** das Stacks (criação, atualização e exclusão).  
- Analisar a **estrutura e sintaxe** de um template CloudFormation.  

---

##  Exemplo de Template Simples (YAML)

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Description: "Stack com EC2, Security Group e parâmetros personalizados."

Parameters:
  KeyName:
    Description: Nome da chave EC2 para acesso SSH
    Type: AWS::EC2::KeyPair::KeyName
    ConstraintDescription: Deve ser o nome de uma chave EC2 existente.
  InstanceType:
    Description: Tipo da instância EC2
    Type: String
    Default: t2.micro
    AllowedValues:
      - t2.micro
      - t3.micro
      - t3.small
    ConstraintDescription: Deve ser um tipo de instância válido.

Resources:
  SecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: "Permitir SSH e HTTP"
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0

  EC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-0c02fb55956c7d316
      InstanceType: !Ref InstanceType
      KeyName: !Ref KeyName
      SecurityGroups:
        - !Ref SecurityGroup
      Tags:
        - Key: Name
          Value: "InstanciaCompleta-CloudFormation"

Outputs:
  InstanceId:
    Description: ID da instância criada
    Value: !Ref EC2Instance
  PublicDNS:
    Description: Endereço público da instância
    Value: !GetAtt EC2Instance.PublicDnsName


```

| Seção                      | Descrição                                                     |
| -------------------------- | ------------------------------------------------------------- |
| `AWSTemplateFormatVersion` | Define a versão do formato YAML/JSON.                         |
| `Description`              | Texto descritivo da Stack.                                    |
| `Parameters`               | Valores variáveis que podem ser passados na criação da Stack. |
| `Resources`                | Recursos AWS a serem criados.                                 |
| `Outputs`                  | Informações retornadas após a criação (ex: ID, DNS, etc).     |


### Criar uma Stack

```bash
aws cloudformation create-stack \
  --stack-name minha-primeira-stack \
  --template-body file://templates/instancia.yaml
```
### Listar stacks existentes
```bash
aws cloudformation list-stacks
```
### Descrever uma stack específica
```bash
aws cloudformation describe-stacks --stack-name minha-primeira-stack
```
### Atualizar stack
```bash
aws cloudformation update-stack \
  --stack-name minha-primeira-stack \
  --template-body file://templates/instancia.yaml
```
### Excluir uma stack
```bash
aws cloudformation delete-stack --stack-name minha-primeira-stack
```
