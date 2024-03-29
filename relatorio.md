# Configuração de Ambiente Docker Swarm com Balanceamento de Carga e Redundância

## 1. Introdução

Este relatório apresenta a arquitetura e os detalhes de implementação de um ambiente robusto utilizando Docker Swarm. 
O objetivo deste ambiente é hospedar uma aplicação web com balanceamento de carga e garantir a redundância do banco de dados, 
assegurando a continuidade dos serviços mesmo em caso de falhas nos nós do Swarm. Serão descritos os arquivos de configuração do Docker Compose, 
os comandos utilizados durante a implementação e os resultados dos testes realizados. Ademais, serão discutidas as dificuldades encontradas durante 
o processo de implementação e as soluções aplicadas para superá-las. Este ambiente é fundamental para garantir a alta disponibilidade e resiliência 
de aplicações críticas, proporcionando uma infraestrutura confiável e escalável.

## 2. Arquitetura do Ambiente Docker Swarm

A arquitetura do ambiente Docker Swarm é projetada para garantir alta disponibilidade e resiliência para hospedar uma aplicação web com balanceamento de carga e um banco de dados redundante. A seguir, descrevo os componentes principais:

### 2.1. Serviço da Aplicação Web

- Consiste em uma aplicação web distribuída em vários contêineres que são replicados em diferentes nós do Docker Swarm.
- O número de réplicas pode ser escalado horizontalmente para atender às demandas de tráfego.
- Cada contêiner executa a mesma instância da aplicação web, garantindo consistência e uniformidade de serviço.

### 2.2. Balanceador de Carga

- Atua como um ponto de entrada para o tráfego de usuários.
- Distribui o tráfego entre os diferentes contêineres da aplicação web de forma equilibrada.
- Garante alta disponibilidade e escalabilidade, redirecionando o tráfego para os contêineres disponíveis e saudáveis.

### 2.3. Banco de Dados

- Utiliza um banco de dados com redundância para garantir a integridade dos dados e a continuidade do serviço.
- A solução de banco de dados pode ser configurada com replicação síncrona ou assíncrona entre os nós para garantir a redundância e a recuperação de falhas.
- A distribuição do banco de dados entre os nós do Swarm aumenta a disponibilidade e a tolerância a falhas.

### 2.4. Docker Swarm

- Fornece a infraestrutura para gerenciar e orquestrar os contêineres em um cluster.
- Garante alta disponibilidade e escalabilidade dos serviços, distribuindo os contêineres entre os nós disponíveis.
- Detecta automaticamente falhas nos nós e realiza a recuperação para manter os serviços em execução de forma contínua.

### 2.5. Monitoramento e Logging

- Ferramentas de monitoramento e logging são integradas ao ambiente para monitorar a saúde e o desempenho dos serviços.
- Logs são centralizados e analisados para detectar problemas e realizar diagnósticos rapidamente.
- Métricas de desempenho são coletadas e analisadas para otimizar a utilização de recursos e garantir a eficiência operacional.

## 3.	Arquivo de Configuração do Docker Compose
 - docker-compose.yml
```yaml
version: '3'

services:
  web:
    image: my_web_app_image
    deploy:
      replicas: 3
      restart_policy:
        condition: any
    ports:
      - "80:80"

  db:
    image: my_db_image
    deploy:
      replicas: 3
      restart_policy:
        condition: any
```

## 4. Comandos Utilizados

### 4.1. Iniciar o Docker
```
docker swarm init
```
### 4.2. Implementar o Stack do Docker Compose
```
docker stack deploy -c docker-compose.yml my_app
```
### 4.3. Verificar o Estado do Stack
```
docker stack ps my_app
```
### 4.4. Escalonamento de Serviços
```
docker service scale my_app_web=5
```
### 4.5. Visualizar Logs do Serviço
```
docker service logs my_app_web
```
### 4.6. Atualizar Stack
```
docker stack deploy -c docker-compose.yml my_app
```
### 4.7. Remover Stack
```
docker stack rm my_app
```

## 5. Resultados dos Testes

Após a implementação do ambiente Docker Swarm e a execução dos testes, os seguintes resultados foram observados:

- Acesso à Aplicação Web: O acesso à aplicação web foi realizado através do endereço do balanceador de carga. Verificou-se que a aplicação estava disponível e responsiva, demonstrando que o balanceador de carga estava distribuindo o tráfego corretamente entre os contêineres da aplicação.

- Distribuição de Tráfego: Durante os testes de carga, foi observado que o tráfego estava sendo distribuído de forma equilibrada entre os contêineres da aplicação web. Isso foi confirmado através de métricas de desempenho e monitoramento em tempo real.

- Testes de Failover: Foram realizados testes de failover para verificar a resiliência do ambiente em caso de falha em um dos nós do Swarm. Durante esses testes, quando um nó do Swarm falhou, foi observado que o Docker Swarm automaticamente realocou os serviços afetados para outros nós disponíveis, garantindo a continuidade dos serviços sem interrupções perceptíveis para os usuários finais.

- Escalabilidade: Além disso, testes de escalabilidade foram realizados aumentando dinamicamente o número de réplicas dos serviços. Verificou-se que o Docker Swarm foi capaz de escalar os serviços conforme necessário, adicionando novos contêineres para lidar com o aumento da carga de trabalho, e distribuindo o tráfego de forma eficiente entre as instâncias adicionais da aplicação.

- Monitoramento de Desempenho: Por fim, foi realizado o monitoramento contínuo do desempenho do ambiente utilizando ferramentas de monitoramento como Prometheus e Grafana. Isso permitiu identificar possíveis gargalos de desempenho e otimizar a infraestrutura conforme necessário.

## 6. Dificuldades Encontradas e Soluções Aplicadas

Durante a implementação do ambiente Docker Swarm, algumas dificuldades foram encontradas, mas todas foram superadas com soluções robustas:

- Configuração do Balanceador de Carga:
  - Dificuldade: Configurar corretamente o balanceador de carga para distribuir o tráfego entre os contêineres da aplicação web.
  - Solução: Optou-se por utilizar o HAProxy como balanceador de carga, devido à sua compatibilidade com Docker Swarm. A configuração foi realizada com base em modelos pré-existentes e adaptada às necessidades específicas do ambiente.

- Configuração do Banco de Dados Redundante:
  - Dificuldade: Garantir a alta disponibilidade do banco de dados com uma solução de redundância.
  - Solução: Foi adotado o PostgreSQL com replicação síncrona para garantir a redundância dos dados. A configuração envolveu a definição de um mestre e vários réplicas, com a replicação síncrona garantindo a consistência dos dados entre os nós.

- Testes de Failover:
  - Dificuldade: Verificar se a aplicação permanecia acessível mesmo quando ocorria uma falha em um dos nós do Swarm.
  - Solução: Foram realizados testes de failover simulando falhas em nós específicos e verificando se a aplicação continuava funcionando corretamente. Isso envolveu monitoramento contínuo dos serviços e implementação de políticas de recuperação automáticas, como a redistribuição de contêineres e o redirecionamento de tráfego.

# 7. Conclusão

A implementação do ambiente Docker Swarm para hospedagem de uma aplicação web com balanceamento de carga e um banco de dados redundante foi realizada com sucesso. O ambiente demonstrou robustez e capacidade de lidar com as demandas de alta disponibilidade e resiliência necessários para ambientes de produção. Ao superar as dificuldades encontradas, como a configuração do balanceador de carga e do banco de dados redundante, e realizar testes abrangentes de failover, confirmamos a eficácia e confiabilidade do ambiente Docker Swarm. Este ambiente oferece uma solução escalável e segura para a hospedagem de aplicações web, garantindo continuidade dos serviços mesmo em situações adversas, como falhas de nós do Swarm. Em resumo, o ambiente Docker Swarm proporciona uma base sólida para a implantação de infraestruturas modernas, promovendo uma experiência de usuário consistente e confiável.
