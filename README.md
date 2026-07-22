# AWS - Serviços Avançados e Intermediários

## 📚 Bootcamp DIO - AWS Intermediário

Este repositório documenta os conhecimentos adquiridos durante o bootcamp de AWS Intermediário e Avançado da DIO, com foco em serviços de computação, integração de mensagens e orquestração de workflows.

---

## 🎯 Objetivo

Apresentar uma visão abrangente dos principais serviços AWS para construção de arquiteturas escaláveis, resilientes e de baixo acoplamento.

---

## 📋 Índice

- [AWS Lambda](#aws-lambda)
- [ECS - Elastic Container Service](#ecs---elastic-container-service)
- [EKS - Elastic Kubernetes Service](#eks---elastic-kubernetes-service)
- [SNS - Simple Notification Service](#sns---simple-notification-service)
- [SQS - Simple Queue Service](#sqs---simple-queue-service)
- [AWS Step Functions](#aws-step-functions)
- [Arquitetura Integrada](#arquitetura-integrada)
- [Casos de Uso](#casos-de-uso)

---

## AWS Lambda

### 📌 O que é?

AWS Lambda é um serviço de computação serverless que permite executar código sem provisionar ou gerenciar servidores. Você paga apenas pelo tempo de execução do seu código.

### 🔑 Características Principais

- **Serverless**: Sem necessidade de gerenciar infraestrutura
- **Escalabilidade Automática**: Escalação automática baseada em demanda
- **Multi-linguagem**: Suporte para Python, Node.js, Java, Go, C#, Ruby e mais
- **Integração com AWS**: Funciona com S3, DynamoDB, API Gateway, SNS, SQS, etc.
- **Modelo de Preço**: Pague por execução (por 100ms de execução)
- **Limite de Duração**: Máximo de 15 minutos por execução

### 💡 Casos de Uso

- Processamento de eventos (upload de arquivos, mudanças em banco de dados)
- APIs RESTful com API Gateway
- Processamento de dados em tempo real
- Agendamento de tarefas (com EventBridge)
- Processamento de imagens/vídeos

### 📊 Diagrama Conceitual

![Lambda Architecture](/images/lambda-architecture.gif)

### 📝 Exemplo de Código

```python
import json

def lambda_handler(event, context):
    """
    Função Lambda simples que processa um evento
    """
    try:
        # Extrai dados do evento
        body = json.loads(event.get('body', '{}'))
        nome = body.get('nome', 'Mundo')
        
        return {
            'statusCode': 200,
            'body': json.dumps({
                'mensagem': f'Olá, {nome}!',
                'timestamp': context.invoked_function_arn
            })
        }
    except Exception as e:
        return {
            'statusCode': 500,
            'body': json.dumps({'erro': str(e)})
        }
```

---

## ECS - Elastic Container Service

### 📌 O que é?

ECS é um serviço de orquestração de containers altamente escalável e de alto desempenho que oferece suporte para Docker. Permite executar aplicações em containers sem gerenciar a infraestrutura Kubernetes.

### 🔑 Características Principais

- **Suporte a Docker**: Compatível com containers Docker
- **Dois Modelos de Lançamento**:
  - **EC2**: Você gerencia instâncias EC2
  - **Fargate**: Serverless (AWS gerencia a infraestrutura)
- **Auto Scaling**: Escalação automática de tasks baseada em métricas
- **Load Balancing**: Integração com ALB/NLB
- **Integração com ECR**: Amazon Elastic Container Registry
- **Segurança**: IAM roles, Security Groups, VPC

### 💡 Casos de Uso

- Aplicações web containerizadas
- Microserviços
- Jobs em lote
- Processamento de dados
- Aplicações que requerem alta disponibilidade

### 📊 Diagrama Conceitual

![ECS Architecture](/images/ecs-architecture.png)

### 📋 Estrutura de Tarefa (Task Definition)

```json
{
  "family": "minha-aplicacao",
  "taskRoleArn": "arn:aws:iam::ACCOUNT:role/ECSTaskRole",
  "executionRoleArn": "arn:aws:iam::ACCOUNT:role/ECSTaskExecutionRole",
  "networkMode": "awsvpc",
  "containerDefinitions": [
    {
      "name": "app",
      "image": "123456789.dkr.ecr.us-east-1.amazonaws.com/minha-app:latest",
      "portMappings": [
        {
          "containerPort": 8080,
          "hostPort": 8080,
          "protocol": "tcp"
        }
      ],
      "memory": 512,
      "cpu": 256,
      "environment": [
        {
          "name": "ENVIRONMENT",
          "value": "production"
        }
      ]
    }
  ],
  "requiresCompatibilities": ["FARGATE"],
  "cpu": "256",
  "memory": "512"
}
```

---

## EKS - Elastic Kubernetes Service

### 📌 O que é?

EKS é um serviço gerenciado de Kubernetes na AWS. Oferece controle total sobre o Kubernetes com a conveniência de um serviço AWS gerenciado.

### 🔑 Características Principais

- **Kubernetes Gerenciado**: AWS gerencia o control plane do Kubernetes
- **Alta Disponibilidade**: Control plane distribuído em múltiplas AZs
- **Integração AWS**: IAM, VPC, ELB, CloudWatch
- **ECR Integration**: Suporte nativo para Amazon ECR
- **Auto Scaling**: Com Cluster Autoscaler e Karpenter
- **Networking Avançado**: CNI plugins, segurança de rede

### 💡 Casos de Uso

- Aplicações complexas com múltiplos microserviços
- Necessidade de controle total sobre orquestração
- Migração de clusters Kubernetes existentes
- Aplicações com requisitos de máquinas heterogêneas
- Arquiteturas híbridas

### 📊 Diagrama Conceitual

![EKS Architecture](/images/eks-architecture.png)

### 📋 Exemplo de Deployment Kubernetes

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: minha-aplicacao
  namespace: default
spec:
  replicas: 3
  selector:
    matchLabels:
      app: minha-app
  template:
    metadata:
      labels:
        app: minha-app
    spec:
      containers:
      - name: app
        image: 123456789.dkr.ecr.us-east-1.amazonaws.com/minha-app:latest
        ports:
        - containerPort: 8080
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        env:
        - name: ENVIRONMENT
          value: "production"
---
apiVersion: v1
kind: Service
metadata:
  name: minha-aplicacao-service
spec:
  type: LoadBalancer
  selector:
    app: minha-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
```

---

## SNS - Simple Notification Service

### 📌 O que é?

SNS é um serviço de publicação/subscrição totalmente gerenciado que permite enviar mensagens para múltiplos destinos de forma rápida e confiável.

### 🔑 Características Principais

- **Modelo Pub/Sub**: Publicadores enviam mensagens para tópicos
- **Múltiplos Protocolos**: Email, SMS, HTTP, Lambda, SQS, etc.
- **Entrega Confiável**: Tentativas de reentrega automáticas
- **Filtragem de Mensagens**: Subscrições podem filtrar mensagens
- **FIFO Topics**: Para caso de uso onde ordem importa
- **Criptografia**: Dados em repouso e em trânsito

### 💡 Casos de Uso

- Notificações por email ou SMS
- Alertas de sistema
- Pub/Sub para microserviços
- Fan-out para processamento paralelo
- Integrações com sistemas externos

### 📊 Diagrama Conceitual

![SNS Architecture](/images/sns-architecture.png)

### 📋 Exemplo de Uso (Python)

```python
import boto3
import json

sns_client = boto3.client('sns')

# Publicar mensagem em um tópico
def publicar_notificacao(topico_arn, mensagem, assunto=None):
    """
    Publica uma mensagem em um tópico SNS
    
    Args:
        topico_arn: ARN do tópico SNS
        mensagem: Conteúdo da mensagem
        assunto: Assunto (opcional, usado em email)
    """
    try:
        resposta = sns_client.publish(
            TopicArn=topico_arn,
            Message=json.dumps(mensagem),
            Subject=assunto or 'Notificação AWS'
        )
        print(f"Mensagem publicada: {resposta['MessageId']}")
        return resposta
    except Exception as e:
        print(f"Erro ao publicar: {e}")
        raise

# Exemplo de uso
if __name__ == "__main__":
    topico_arn = "arn:aws:sns:us-east-1:123456789:meu-topico"
    mensagem = {
        "tipo": "alerta",
        "severidade": "alta",
        "descricao": "Erro crítico detectado"
    }
    publicar_notificacao(topico_arn, mensagem, "Alerta do Sistema")
```

---

## SQS - Simple Queue Service

### 📌 O que é?

SQS é um serviço de fila de mensagens totalmente gerenciado que desacopla componentes de uma aplicação, permitindo processamento assíncrono e escalável.

### 🔑 Características Principais

- **Armazenamento de Mensagens**: Até 256 KB por mensagem
- **Retenção de Mensagens**: Configurável (1 minuto a 14 dias)
- **Visibility Timeout**: Controle de quando mensagens são reprocessadas
- **Dois Tipos**:
  - **Standard**: Melhor esforço para entrega, sem garantia de ordem
  - **FIFO**: Entrega em ordem garantida, sem duplicatas
- **Long Polling**: Reduz o consumo de recursos
- **Integração com Lambda, ECS, EC2**: Fácil consumo de mensagens

### 💡 Casos de Uso

- Desacoplamento de componentes
- Processamento assíncrono de tarefas
- Buffering de requisições
- Trabalhos em lote
- Integração entre microsserviços

### 📊 Diagrama Conceitual

![SQS Architecture](/images/sqs-architecture.jpg)

### 📋 Exemplo de Uso (Python)

```python
import boto3
import json
from datetime import datetime

sqs_client = boto3.client('sqs')

# URLs das filas
FILA_PADRAO_URL = "https://sqs.us-east-1.amazonaws.com/123456789/minha-fila"
FILA_FIFO_URL = "https://sqs.us-east-1.amazonaws.com/123456789/minha-fila.fifo"

def enviar_mensagem(fila_url, corpo, atributos=None, grupo_id=None):
    """
    Envia uma mensagem para uma fila SQS
    
    Args:
        fila_url: URL da fila
        corpo: Conteúdo da mensagem
        atributos: Atributos customizados (opcional)
        grupo_id: ID do grupo (obrigatório para FIFO)
    """
    kwargs = {
        'QueueUrl': fila_url,
        'MessageBody': json.dumps(corpo)
    }
    
    if grupo_id:
        kwargs['MessageGroupId'] = grupo_id
    
    if atributos:
        kwargs['MessageAttributes'] = atributos
    
    resposta = sqs_client.send_message(**kwargs)
    print(f"Mensagem enviada: {resposta['MessageId']}")
    return resposta

def processar_mensagens(fila_url, max_mensagens=10):
    """
    Processa mensagens de uma fila SQS
    
    Args:
        fila_url: URL da fila
        max_mensagens: Número máximo de mensagens a processar
    """
    resposta = sqs_client.receive_message(
        QueueUrl=fila_url,
        MaxNumberOfMessages=max_mensagens,
        WaitTimeSeconds=20,  # Long polling
        MessageAttributeNames=['All']
    )
    
    mensagens = resposta.get('Messages', [])
    
    for mensagem in mensagens:
        try:
            corpo = json.loads(mensagem['Body'])
            print(f"Processando: {corpo}")sqs
            
            # Deletar mensagem após processamento bem-sucedido
            sqs_client.delete_message(
                QueueUrl=fila_url,
                ReceiptHandle=mensagem['ReceiptHandle']
            )
        except Exception as e:
            print(f"Erro ao processar mensagem: {e}")

# Exemplo de uso
if __name__ == "__main__":
    # Enviar mensagem padrão
    enviar_mensagem(
        FILA_PADRAO_URL,
        {"tipo": "pedido", "id": "12345", "timestamp": datetime.now().isoformat()}
    )
    
    # Processar mensagens
    processar_mensagens(FILA_PADRAO_URL)
```

---

## AWS Step Functions

### 📌 O que é?

AWS Step Functions é um serviço serverless que permite orquestrar workflows complexos usando máquinas de estado. Coordena vários serviços AWS em sequências, paralelos e escolhas condicionais.

### 🔑 Características Principais

- **Máquinas de Estado**: Defina workflows visuais e estruturados
- **Serverless**: Sem necessidade de gerenciar infraestrutura
- **Integração Nativa**: Lambda, ECS, SNS, SQS, DynamoDB, etc.
- **Tratamento de Erros**: Retry e catch de exceções
- **Execução Paralela**: Processar múltiplas tarefas simultaneamente
- **Histórico Completo**: Auditoria e debugging de execuções
- **Precificação por Transição**: Pague pelo número de transições de estado

### 💡 Casos de Uso

- Orquestração de pipelines de dados
- Processamento de pedidos
- Aprovações e workflows de negócio
- Processamento de imagens/vídeos
- Coordenação de microserviços

### 📊 Diagrama Conceitual

![Step Functions Architecture](/images/stepfunctions-architecture.jpeg)

### 📋 Exemplo de Definição (JSON)

```json
{
  "Comment": "Workflow de processamento de pedidos",
  "StartAt": "ValidarPedido",
  "States": {
    "ValidarPedido": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:us-east-1:123456789:function:validar-pedido",
      "Next": "PedidoValido?",
      "Catch": [
        {
          "ErrorEquals": ["ValidationError"],
          "Next": "PedidoInvalido"
        }
      ]
    },
    "PedidoValido?": {
      "Type": "Choice",
      "Choices": [
        {
          "Variable": "$.isValid",
          "BooleanEquals": true,
          "Next": "ProcessarPagamento"
        }
      ],
      "Default": "PedidoInvalido"
    },
    "ProcessarPagamento": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:us-east-1:123456789:function:processar-pagamento",
      "Next": "NotificarSucesso",
      "Retry": [
        {
          "ErrorEquals": ["TimeoutError"],
          "IntervalSeconds": 2,
          "MaxAttempts": 3,
          "BackoffRate": 2.0
        }
      ]
    },
    "NotificarSucesso": {
      "Type": "Task",
      "Resource": "arn:aws:sns:us-east-1:123456789:notify",
      "End": true
    },
    "PedidoInvalido": {
      "Type": "Task",
      "Resource": "arn:aws:sns:us-east-1:123456789:notify-error",
      "End": true
    }
  }
}
```

---

## Arquitetura Integrada



### 🔄 Fluxo de Exemplo: Processamento de Pedidos

```
1. Cliente faz pedido via API Gateway → Lambda
2. Lambda valida e envia para SQS
3. Workers (ECS/Lambda) processam mensagens da SQS
4. SNS notifica clientes sobre mudanças de status
5. Step Functions orquestra todo o workflow
6. Resultados armazenados em DynamoDB
7. CloudWatch monitora tudo
```

### 📋 Arquitetura com Step Functions

```
[Usuário]
   ↓
[API Gateway + Lambda] → Valida requisição
   ↓
[Step Functions] → Orquestra workflow
   ├→ [Lambda] → Processa pagamento
   ├→ [ECS] → Prepara pedido
   ├→ [SQS] → Fila para envio
   └→ [SNS] → Notifica cliente
   ↓
[DynamoDB] → Persiste dados
```

---

## Casos de Uso

### 🛒 E-commerce

- **Lambda**: Processar uploads de imagens, gerar thumbnails
- **ECS/EKS**: Executar aplicação web containerizada
- **SQS**: Fila de pedidos para processamento assíncrono
- **SNS**: Notificações de pedidos e envios
- **Step Functions**: Orquestra todo fluxo do pedido
- **DynamoDB**: Catálogo de produtos

### 📊 Data Pipeline

- **Lambda**: Triggers para processar novos arquivos no S3
- **ECS**: Processamento paralelo de dados
- **SQS**: Fila de jobs de processamento
- **Step Functions**: Orquestra ETL
- **SNS**: Alertas sobre falhas

### 🏥 Sistema de Monitoramento

- **Lambda**: Coleta de métricas
- **SQS**: Fila de alertas
- **SNS**: Notificações para administradores
- **ECS**: Dashboard de visualização
- **Step Functions**: Processamento de alertas

---

## 📚 Referências e Recursos

### Documentação Oficial AWS
- [AWS Lambda Documentation](https://docs.aws.amazon.com/lambda/)
- [ECS Documentation](https://docs.aws.amazon.com/ecs/)
- [EKS Documentation](https://docs.aws.amazon.com/eks/)
- [SNS Documentation](https://docs.aws.amazon.com/sns/)
- [SQS Documentation](https://docs.aws.amazon.com/sqs/)
- [Step Functions Documentation](https://docs.aws.amazon.com/stepfunctions/)

### Certificações Relacionadas
- AWS Certified Solutions Architect - Associate
- AWS Certified Developer - Associate
- AWS Certified Solutions Architect - Professional

### Ferramentas Úteis
- AWS CLI
- AWS CDK (Cloud Development Kit)
- AWS SAM (Serverless Application Model)
- Terraform
- CloudFormation

---

## 📝 Anotações Finais

Este repositório documenta os serviços avançados e intermediários da AWS aprendidos no bootcamp da DIO. A compreensão e prática com esses serviços é fundamental para construir arquiteturas escaláveis, resilientes e cost-effective na AWS.

### Princípios-Chave Aprendidos

✅ **Desacoplamento**: Usar SQS/SNS para desacoplar componentes  
✅ **Escalabilidade**: Aproveitar serverless (Lambda, Fargate)  
✅ **Confiabilidade**: Usar Step Functions para orquestração resiliente  
✅ **Monitoramento**: CloudWatch e X-Ray para observabilidade  
✅ **Segurança**: IAM roles, VPC, criptografia  

---

## 👨‍💻 Autor

Desenvolvido durante o Bootcamp AWS Intermediário - DIO

**Data**: 2026  
**Status**: ✅ Concluído

---

## 📄 Licença

Este repositório é fornecido como material educacional do bootcamp da DIO.

---

**Última atualização**: 2026-07-22
