# Sistema de delivery
**Documentação Técnica Completa**  

**Grupo de Arquitetura de Software**  
**Professora**: Angélica Guimarães  
**Curso**: Engenharia de Software – PUC Minas  

---

## 1. Contexto do Sistema

### 1.1 Apresentação

O sistema idealizado nesse trabalho visa atender as necessidades de uma empresa de delivery chama _"Rapidão Delivery LTDA"_. Essa empresa opera somente pelo seu aplicativo para dispositivos móveis, onde o cliente pode se cadastrar, realizar e acompanhar pedidos. Dado esse contexto, o aplicativo deve oferecer alta disponibilidade e suportar grandes volumes de usuários visto que a _"Rapidão Foods LTDA"_ é uma das empresas de fast food mais relevantes do país.

Considerando que _"Rapidão Foods LTDA"_ é uma franquia de enorme porte, tendo unidades que estão distribuídas por todos os cantos do país, o sistema deve ser capaz de, utilizando a localização cadastrada do usuário, identificar a unidade mais próxima para ser responsável por um pedido de um cliente.

O sistema também conta com o cadastro de entregadores, sendo eles usuários de um tipo diferente dos clientes. Esses tem a possibilidade de serem encarregados da entrega de um pedido, desde que estejam buscando entregas e estejam, no momento, próximos do local da entrega ou da unidade do pedido.

Em adição, as múltiplas unidades da  _"Rapidão Foods LTDA"_ também possuem seu cadastro singular no sistema. Nelas podem ser cadastradas promoções e itens especiais no menu.

Por fim, ressalta-se que o projeto tem prazo para conclusão de 1.5 ano. O contratante reforça que a qualidade do produto vêm antes da velocidade para entregá-lo. 

### 1.2 Necessidades

Tendo em vista a situação descrita acima, o projeto tem como finalidade conceber um sistema focado em:
- **Disponibilidade**: realizando _trade-offs_ de outras características do sistema em detrimento dessa, **se necessário**.
- **Escalabidade**: para permitir que o sistema se expanda juntamente com o crescimento do  _"Rapidão Delivery LTDA"_.
- **Observabilidade**: para validar bugs, erros e riscos em potencial mais rapidamente.
- **Consistência**: para que as funcionalidades chave do sistema apresentem comportamento esperado, garantindo a qualidade na experiência do cliente.

## 2. Projeto do sistema

### 2.1 Estilo arquitetural

O estilo arquitetural escolhido, levando em consideração as demandas mepeadas, foi o de microserviços. Essa escolha se dá, principalmente, pela ênfase em **alta disponibilidade**, pois, ao segregar o sistema em serviços menores, isso permite com que, mesmo que haja um problema em um, os outros podem seguir operando de modo independente, garantindo a estabilidade do sistema.

Dado isso, os serviços ficam definidos como:

- Serviço de Pagamentos
- Serviço de Autenticação
- Serviço de Notificação
- Serviço de Pedidos
- Serviço de Entregas

A implementação desse estilo sugere a necessidade de um API gateway. Ele será responsável pelo routing, balanceamento de carga e por subir novas instâncias dos serviços caso um deles esteja sob muita demanda.

### 2.2 Comunicação

Levando em consideração o caráter independente dos serviços, o sistema deverá ser capaz de realizar comunicação de modo assíncrono também. Nesse caso será utilziado um serviço de mensageria, que terá como foco públicar dados de maneira geralizada, ativando serviços de modo assíncrono. Isso será muito útil no exemplo de pedidos, onde, quando um for realizado, múltiplos serviços e funcionalidades poderão ser ativadas de modo simultâneo.

### 2.3 Tecnologias

| Contexto         | Tecnologia                | Justificativa                           |
|------------------|---------------------------|-----------------------------------------|
| Backend          | Java + Springboot         | Java é uma linguagem balaneceada, com bastante maturidade de mercado, várias ferramentas auxiliáres e boa oferta de desenvolvedores qualificados |
| Segurança        | Spring Security + JWT     | Proteção de dados sensíveis do cliente e das unidades do Rapidão (LGPD) |
| Mobile           | Swift e Kotlin            | Dada a larga janela para o desenvolvimento do projeto, foram escolhidas as tecnologias mobile nativas para IOS e Android, a fim de produzir um app com maior qualidade para o usuário final |
| Observabilidade  | Datadog                   | Datadog é uma das ferramentas de observabilidade mais completas no mercado, essencial para o foco estabilidade e consistência do projeto. |
| Monitoramento    | Uptime Kuma               | Está uma boa ferramente para validar a saúde geral do sistema, sendo capaz de realizar chamadas frequentes e validar o funcionamento correto de APIs em produção | 
| Mensageria       | RabbitMQ                  | Ferramenta conhecida e fácil de se utilizar, mas também extremamente consolidada no mercado |




