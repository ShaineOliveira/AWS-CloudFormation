```
AWSTemplateFormatVersion: "2010-09-09"
Description: >
  Stack automatizada com uma instância EC2, Security Group configurado
  e parâmetros personalizados para acesso SSH e tipo de instância.

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
      GroupDescription: "Permitir tráfego SSH (22) e HTTP (80)"
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0
      Tags:
        - Key: Name
          Value: "SecurityGroup-CloudFormation"

  EC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-0c02fb55956c7d316  # Amazon Linux 2 (us-east-1)
      InstanceType: !Ref InstanceType
      KeyName: !Ref KeyName
      SecurityGroups:
        - !Ref SecurityGroup
      Tags:
        - Key: Name
          Value: "InstanciaCompleta-CloudFormation"

Outputs:
  InstanceId:
    Description: ID da instância EC2 criada
    Value: !Ref EC2Instance

  PublicDNS:
    Description: DNS público da instância EC2
    Value: !GetAtt EC2Instance.PublicDnsName

  PublicIP:
    Description: Endereço IP público da instância EC2
    Value: !GetAtt EC2Instance.PublicIp
