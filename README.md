# Hangout - Sistema de Microserviços

## Arquitetura do Sistema

### Estilo Arquitetural

O estilo arquitetural escolhido para o projeto **Hangout** é a **Arquitetura em Microserviços**, a qual permite a criação de serviços independentes, escaláveis e de fácil manutenção. Esta abordagem facilita o desenvolvimento distribuído, possibilita a escolha de tecnologias específicas para cada serviço e garante maior resiliência ao sistema como um todo.

O sistema Hangout é composto por **11 componentes principais**, sendo **9 microserviços** de domínio, **1 API Gateway** e **1 Discovery Server**, conforme ilustrado no diagrama de arquitetura acima.

## Padrões Arquiteturais

O projeto Hangout adota diversos padrões arquiteturais que garantem a qualidade, manutenibilidade e escalabilidade do sistema:

### Padrões de Arquitetura de Software

- **MVC (Model-View-Controller)**: Facilita a separação de responsabilidades e mantém o acoplamento baixo, organizando a aplicação em camadas distintas de modelo, visão e controle.

- **Clean Architecture**: Implementado em alguns microserviços, promove a independência de frameworks, testabilidade e separação de conceitos através de camadas bem definidas (entities, use cases, interface adapters, frameworks).

### Padrões de Microserviços

- **API Gateway**: Centraliza e roteia as requisições feitas aos microserviços, evitando a necessidade do cliente conhecer o endereço de cada serviço individualmente. Também atua como ponto único de entrada para políticas de segurança, rate limiting e logging.

- **Service Registry/Discovery (Eureka)**: Permite o registro dinâmico e descoberta automática de serviços, eliminando a necessidade de configuração manual de endpoints e facilitando a escalabilidade horizontal.

- **Database per Service**: Cada microserviço possui seu próprio banco de dados PostgreSQL, garantindo o isolamento de dados e permitindo que cada serviço evolua independentemente.

### Padrões de Segurança

- **Token JWT (JSON Web Token)**: Padrão adotado para autenticação e autorização nas requisições HTTP, integrado com Spring Security, garantindo comunicação segura e stateless entre os serviços.

- **Rate Limiting**: Implementação de limitação de taxa de requisições para proteção contra abuso, ataques DDoS e garantia de disponibilidade do sistema, controlando o número de chamadas que um cliente pode fazer em um determinado período de tempo.

### Padrões de Comunicação

- **API RESTful**: Comunicação entre cliente e servidor através de APIs REST, utilizando os verbos HTTP e convenções padrão da web.

- **Service-to-Service Communication**: Comunicação síncrona entre microserviços através de chamadas HTTP/REST.

### Padrões de Integração

- **Polyglot Programming**: Uso de diferentes linguagens de programação conforme a melhor adequação ao problema (Java/Spring Boot para a maioria dos serviços, Python/Flask para o serviço de QRCode).

## 🔐 Backend

### Requisitos de Segurança

- **OAuth2**: Utilizado para autenticação e autorização segura dos usuários.
- **HTTPS**: Protocolo de comunicação para garantir a segurança dos dados transmitidos.
- **JWT (JSON Web Tokens)**: Para gerenciar sessões de usuários de forma segura, utilizando a biblioteca JJWT (versões 0.11.5 e 0.12.6).
- **Spring Security**: Framework de segurança integrado para proteção de endpoints e controle de acesso.
- **Rate Limiting**: Implementado no API Gateway com suporte do Redis para controle de requisições.

### Protocolo de Comunicação

- **API REST**: Utilização do protocolo HTTP/HTTPS para comunicação entre os serviços e clientes externos.
- **WebFlux**: Programação reativa no API Gateway para melhor performance e escalabilidade.

### Tecnologias Utilizadas

#### Core Framework
- **Java 21**: Versão LTS do Java com recursos modernos e melhorias de performance.
- **Spring Boot 3.x**: Framework principal para desenvolvimento do backend.
- **Spring Cloud 2025.0.0**: Conjunto de ferramentas para construção de sistemas distribuídos.

#### Persistência e Comunicação com Banco de Dados
- **JPA (Java Persistence API)**: Para mapeamento objeto-relacional e comunicação com o banco de dados.
- **PostgreSQL**: Banco de dados relacional utilizado pelos microserviços.
- **Spring Data JPA**: Abstração para operações de banco de dados.

#### Microserviços e Descoberta de Serviços
- **Netflix Eureka Client**: Cliente para registro e descoberta de serviços.
- **Spring Cloud Gateway**: Gateway reativo para roteamento e filtragem de requisições.

#### Observabilidade e Monitoramento
- **Spring Boot Actuator**: Endpoints para monitoramento e gerenciamento da aplicação.
- **Micrometer**: Framework de métricas com integração ao Prometheus.
- **Micrometer Tracing Bridge Brave**: Rastreamento distribuído de requisições.
- **Zipkin Reporter**: Exportação de traces para o Zipkin para análise de latência.

#### Cache e Armazenamento
- **Redis (Reactive)**: Utilizado para cache distribuído e implementação de rate limiting no Gateway.

#### Produtividade e Qualidade de Código
- **Lombok**: Redução de código boilerplate através de anotações.
- **Spring Boot DevTools**: Ferramentas de desenvolvimento para hot reload e melhor experiência de desenvolvimento.
- **Bean Validation**: Validação de dados de entrada através de anotações.

#### Testes
- **Spring Boot Starter Test**: Suite completa de ferramentas para testes unitários e de integração.
- **JUnit 5**: Framework de testes.


### Microserviço QRCode (Python/Flask)

O serviço de geração de QR Code foi desenvolvido em **Python** utilizando o framework **Flask**.

#### Tecnologias Utilizadas
- **Python 3.x**: Linguagem de programação escolhida pela sua simplicidade e vasto ecossistema de bibliotecas.
- **Flask**: Micro-framework web leve e flexível para criação de APIs REST.
- **qrcode**: Biblioteca Python para geração de códigos QR.
- **Pillow (PIL)**: Biblioteca de processamento de imagens, utilizada pelo qrcode para criação das imagens.
- **Base64**: Encoding padrão para conversão de imagens binárias em strings transmissíveis via JSON.

## Diagrama Arquitetural

<img width="1497" height="872" alt="image" src="https://github.com/user-attachments/assets/51da2972-5ed7-4f5c-aeb4-c44ef3f9b637" />


#### Microserviços de Domínio

O sistema é dividido nos seguintes microserviços especializados:

1. **Auth**: Gerenciamento de autenticação e autorização de usuários
2. **Email**: Serviço responsável pelo envio e gerenciamento de e-mails
3. **Mídia**: Processamento e armazenamento de arquivos de mídia (imagens, vídeos, etc.)
4. **QRCode**: Geração e validação de códigos QR (implementado em Flask/Python)
5. **Evento**: Gerenciamento de eventos e suas funcionalidades
6. **Ingresso**: Controle de ingressos e vendas
7. **Usuário**: Gestão de perfis e dados de usuários
8. **Pagamento**: Processamento de transações e pagamentos
9. **Pedido**: Gerenciamento de pedidos e compras

#### Componentes de Infraestrutura

- **Discovery Server (Eureka/Netflix)**: Responsável pelo registro e descoberta de serviços, permitindo que os microserviços se localizem dinamicamente na rede sem necessidade de configurações fixas de endpoints.

- **Spring Cloud Gateway**: Atua como ponto de entrada único para todas as requisições externas, fornecendo roteamento inteligente, balanceamento de carga, segurança e gerenciamento centralizado de requisições.

## 📈 Escalabilidade Futura

A arquitetura de microserviços do Hangout! foi projetada pensando no crescimento sustentável e na capacidade de evoluir conforme a demanda aumenta. Abaixo estão as principais estratégias e possibilidades de escalabilidade:

### Escalabilidade Horizontal

- **Replicação de Microserviços**: Cada microserviço pode ser replicado independentemente conforme a demanda. Por exemplo, durante eventos de grande porte ou períodos de pico (finais de semana, feriados), os serviços de Ingresso, Pagamento e Pedido podem ter múltiplas instâncias rodando simultaneamente.
- **Load Balancing Automático**: O Spring Cloud Gateway e Eureka trabalham em conjunto para distribuir automaticamente as requisições entre as instâncias disponíveis de cada serviço.

### Escalabilidade de Dados

- **Database per Service**: Como cada microserviço possui seu próprio banco de dados, é possível escalar o armazenamento de forma independente conforme a necessidade de cada domínio.
- **Sharding**: Para serviços com grande volume de dados (como Evento e Usuário), pode-se implementar particionamento horizontal dos dados.

## 🎯 Diferencial: Sistema de Recomendação Inteligente

Um dos principais diferenciais competitivos do Hangout! será o **Sistema de Recomendação de Eventos Personalizado**.

### Visão Geral

O sistema analisará múltiplos fatores para criar um feed personalizado de eventos, incluindo:
- **Interesses declarados** pelo usuário (categorias favoritas, tipos de eventos)
- **Histórico de participação** em eventos anteriores
- **Localização geográfica** e preferências de distância
- **Padrões temporais** (dias da semana, horários preferidos)
- **Comportamento de usuários similares** (collaborative filtering)

### Arquitetura do Serviço de Recomendação

Para implementar essa funcionalidade, será desenvolvido um **novo microserviço dedicado**: o **Serviço de Recomendação**, que operará de forma independente e escalável.

<img width="1513" height="871" alt="image" src="https://github.com/user-attachments/assets/ca232911-5817-4895-91fb-edaeddd3265b" />


#### Características Técnicas
- **Processamento Assíncrono**: O serviço calculará recomendações em background, evitando impacto na performance de outras operações.
- **Cache Inteligente**: Recomendações serão pré-calculadas e armazenadas em cache (Redis) para resposta instantânea.
- **Modelo de Machine Learning**: Utilização de algoritmos como Collaborative Filtering, Content-Based Filtering ou modelos híbridos.
- **Pipeline de Dados**: Integração com os dados de Evento, Usuário, Pedido e Ingresso para análise comportamental.

### Desafios e Complexidades

#### Desafios Técnicos
- **Cold Start Problem**: Novos usuários sem histórico de interações dificultam a geração de recomendações precisas inicialmente. *Solução*: Utilizar questionário inicial de interesses e recomendações baseadas em popularidade e localização.
- **Escalabilidade de Dados**: À medida que a base de usuários e eventos cresce, o processamento de recomendações se torna mais complexo. *Solução*: Uso de processamento distribuído e cache inteligente.
- **Qualidade dos Dados**: Dados esparsos ou inconsistentes podem prejudicar a precisão do modelo. *Solução*: Implementar validações e enriquecimento de dados através de múltiplas fontes.


# 📱 Frontend - Design System & Especificações

## 🎨 Identidade Visual

### Paleta de Cores

#### Cores Primárias
- **Vermelho Principal**: `#FF441A` (600)
- **Rosa Vibrante**: `#FF07C2` (600)
- **Roxo**: `#DC54F3` (900) / `#531CB3` (900)
- **Azul Claro**: `#EAF2FF` (900)
- **Cinza Escuro**: `#423E3B` (900)

#### Cores Secundárias
- **Laranja**: `#FF441A` (900)
- **Rosa Claro**: `#FFC0CB`
- **Branco**: `#FFFFFF`
- **Cinza Claro**: `#D3D3D3` / `#EAF2FF`

#### Acessibilidade de Contraste
Todas as combinações de cores seguem os padrões WCAG:
- **AA 4.55**: Contraste mínimo para texto normal
- **AAA 5**: Contraste ideal para melhor legibilidade

## 🔤 Tipografia

### Fonte Principal: **NOTO SANS - Display Medium**

#### Hierarquia Tipográfica

**Título (Display Medium)**
- Uso: Títulos de páginas, seções, nomes de produtos, listas pequenas
- Peso: 600 (Medium)
- Características: Bold, impactante, alta legibilidade

**Corpo (Regular)**
- Uso: Artigos, descrições de produtos, perguntas e respostas
- Peso: 400 (Regular)
- Características: Leitura confortável para textos longos

**Funcional (Bold)**
- Uso: Botões, links, formulários, notificações, etiquetas
- Peso: 900 (Bold)
- Características: Destaque em elementos interativos

### Fallback Fonts
```css
font-family: 'Noto Sans', Arial, Helvetica, sans-serif;
```
<img width="1728" height="1117" alt="Fontes - Style Guide" src="https://github.com/user-attachments/assets/703ab383-e0e9-4dc8-b0a3-b7d8cafcc78e" />
<img width="1728" height="1117" alt="Botões" src="https://github.com/user-attachments/assets/0468c203-a4a7-47bc-b036-cf4bf0533180" />

## Dados (IMPLEMENTAR AINDA)
### Banco de Dados
- **SGBD Utilizado**: PostgreSQL – Banco de dados relacional escolhido para o
projeto.
#### 🔐 Padrão de Nomenclatura
- **Tabelas**:
`TB_NOME_DA_TABELA`
- **Colunas**:
| Prefixo | Significado | Exemplo |
|---------|--------------|-----------------|
| `NR_` | Números | `NR_QUANTIDADE` |
| `VL_` | Valores | `VL_PRECO` |
| `NM_` | Nomes | `NM_NOME` |
| `DS_` | Descrições | `DS_DESCRICAO` |
| `CD_` | Códigos | `CD_CODIGO` |
---

## 🎉 Motivação

O Hangout! nasceu para revolucionar a forma como as pessoas descobrem e participam de experiências sociais. é a plataforma definitiva onde você encontra qualquer tipo de rolê 🎉 desde um almoço casual com amigos no restaurante 🍽️, até os shows e festivais mais animados 🎶.
