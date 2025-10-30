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
- **Disponibilidade**: realizando trade-offs de outras características do sistema em detrimento dessa, se necessário.
- **Escalabilidade**: para permitir que o sistema se expanda juntamente com o crescimento do  _"Rapidão Delivery LTDA"_.
- **Observabilidade**: para validar bugs, erros e riscos em potencial mais rapidamente.
- **Consistência**: para que as funcionalidades chave do sistema apresentem comportamento esperado, garantindo a qualidade na experiência do cliente.

## 2. Projeto do sistema

### 2.1 Estilo arquitetural
O estilo arquitetural escolhido, levando em consideração as demandas mapeadas, foi o de microserviços. Essa escolha se dá, principalmente, pela ênfase em **alta disponibilidade**, pois, ao segregar o sistema em serviços menores, isso permite com que, mesmo que haja um problema em um, os outros podem seguir operando de modo independente, garantindo a estabilidade do sistema.

Dado isso, os serviços ficam definidos como:
- Serviço de Pagamentos
- Serviço de Autenticação
- Serviço de Notificação
- Serviço de Pedidos
- Serviço de Entregas

A implementação desse estilo sugere a necessidade de um API gateway. Ele será responsável pelo routing, balanceamento de carga e por subir novas instâncias dos serviços caso um deles esteja sob muita demanda.

### 2.2 Comunicação
Levando em consideração o caráter independente dos serviços, o sistema deverá ser capaz de realizar comunicação de modo assíncrono também. Nesse caso será utilizado um serviço de mensageria, que terá como foco publicar dados de maneira generalizada, ativando serviços de modo assíncrono. Isso será muito útil no exemplo de pedidos, onde, quando um for realizado, múltiplos serviços e funcionalidades poderão ser ativadas de modo simultâneo.

### 2.3 Tecnologias
| Contexto | Tecnologia | Justificativa |
|---|---|---|
| Backend | Java + Spring Boot | Linguagem madura, ecossistema robusto, tooling abrangente e ampla disponibilidade de desenvolvedores |
| Segurança | Spring Security + JWT | Proteção de dados sensíveis (LGPD), autenticação e autorização robustas |
| Mobile | Swift e Kotlin | Tecnologias nativas para iOS e Android visando qualidade e performance |
| Observabilidade | Datadog | Plataforma completa para métricas, logs, traces e alertas |
| Monitoramento | Uptime Kuma | Monitoramento de disponibilidade e checks de saúde para APIs |
| Mensageria | RabbitMQ | Mensageria consolidada, simples e confiável |

## 3. Fluxos de Processo

### 3.1 Perfis de Usuário
- Cliente
- Entregador
- Administrador de Unidade (Loja)
- Suporte/Backoffice (opcional, para mediação de problemas)

### 3.2 Fluxo do Cliente
1. Onboarding e Cadastro
   - Acesso ao app → Permissões (localização, notificações) → Cadastro com e-mail/telefone ou social login → Verificação (OTP ou link mágico).
   - Definição de endereço(s) padrão e preferências (sem glúten, sem lactose etc.).
2. Descoberta e Navegação
   - Geolocalização identifica a unidade mais próxima.
   - Exibição de cardápio com categorias, promoções e itens recomendados.
   - Filtros: preço, tempo de entrega estimado, avaliação, preferências alimentares.
3. Montagem do Pedido
   - Seleção de itens → customizações (adicionais, remoções) → quantidades.
   - Cálculo dinâmico de subtotal, taxas, frete e estimativa de entrega.
   - Aplicação de cupons/promos, validações de elegibilidade e limite.
4. Checkout
   - Escolha de endereço e observações de entrega.
   - Método de pagamento: cartão salvo, novo cartão, PIX, dinheiro (se aplicável).
   - Validação antifraude (quando necessário).
   - Confirmação do pedido → criação do Pedido com status “Recebido”.
5. Acompanhamento
   - Status: Recebido → Em preparo → Pronto para retirada → Em entrega → Entregue.
   - Tracking do entregador em tempo real quando “Em entrega”.
   - Push notifications e e-mails conforme transições de status.
6. Pós-Entrega
   - Avaliação (estrela + comentário), gorjeta adicional, reembolso parcial/total (workflow de suporte).
   - Recompra rápida (reorder) baseado no histórico.
   - Emissão de NF-e (link/consulta).

Regras e Exceções:
- Pedido mínimo por unidade; horários de funcionamento; indisponibilidade de itens.
- Cupons não cumulativos (configurável); expiração e segmentação por região.
- Falha no pagamento → instruir nova tentativa ou trocar método → reserva de estoque expira após X minutos.

### 3.3 Fluxo do Entregador
1. Cadastro e Verificação
   - Dados pessoais, CNH, foto do veículo (se aplicável), conta bancária.
   - Verificação documental (KYC) e aprovação manual/automática.
2. Disponibilidade
   - Alterar status: Online/Offline.
   - Definir raio de atuação e preferências de entrega (moto/bike/carro).
3. Matching de Entregas
   - Recebe propostas com valor estimado, distância, tipo de rota.
   - Aceitar/Recusar dentro de um SLA curto (ex.: 30–60s).
   - Alocação otimizada por proximidade e SLA do pedido.
4. Execução
   - Navegação até a unidade → confirmar retirada (QR code ou código).
   - Navegação até o cliente → confirmar entrega (foto, assinatura ou código).
5. Pós-Entrega
   - Recebe gorjeta; consolidação de ganhos; extrato semanal.
   - Sistema de reputação/nota impacta priorização de novas corridas.

Regras e Exceções:
- Cancelamentos: penalidades por cancelamento fora de política; reatribuição automática do pedido.
- No-show do cliente: fluxo de contato e reentrega/devolução conforme política da unidade.

### 3.4 Fluxo do Administrador de Unidade
1. Cadastro e Configuração
   - Horários de funcionamento, áreas de entrega, taxas, tempo médio.
   - Cardápio: categorias, itens, variações, estoque, fotos, preço.
2. Operação de Pedidos
   - Painel em tempo real: novos pedidos, status, SLAs, filas de preparo.
   - Atualização de status e priorização; integração com impressora/cozinha (KDS).
3. Promoções e Cupons
   - Criação/edição de campanhas: período, elegibilidade, quantidade limitada, combos.
4. Relatórios
   - Vendas, ticket médio, tempo de preparo, NPS, cancelamentos, desperdício.
5. Suporte
   - Mediação de reclamações, reembolsos, ajuste de pedidos.

Regras e Exceções:
- Ruptura de estoque: substituição sugerida ou ocultar item automaticamente.
- Picos de demanda: ativar “modo alta demanda” ajustando ETA e taxas.

### 3.5 Processos Transversais

- Pagamentos
  - Integração com adquirentes/PSPs; tokenização de cartão; PIX com QR dinâmico.
  - Webhooks para confirmação; reconciliação D+1/D+2; antifraude.
  - Política de estorno e split de pagamento (unidade, entregador – se aplicável).
- Notificações
  - Push, e-mail, SMS; templates por evento; reentrega de notificação em falha.
- Observabilidade
  - Logs estruturados, métricas de latência/erro, tracing distribuído, dashboards por serviço.
- LGPD e Segurança
  - Consentimento, minimização de dados, retenção, direito de exclusão.
  - Criptografia em repouso e em trânsito, segregação por domínio, RBAC.

Estados de Pedido (FSM simplificada):
- Recebido → Em Preparo → Pronto → Alocado Entregador → Em Entrega → Entregue → Finalizado
- Cancelado (cliente/unidade/sistema) pode ocorrer até “Em Preparo” (regras configuráveis).

## 4. Mapeamento de Identidade Visual

### 4.1 Paleta de Cores
- Primária: `#E61E24` (Vermelho Rapidão) — ações principais, CTA, destaques críticos.
- Primária Escura: `#B0171B` — hover/estado pressionado do CTA primário.
- Secundária: `#FFC107` (Amarelo Acento) — badges, promoções, elementos de destaque.
- Neutro 900: `#121212` — títulos, textos de alto contraste.
- Neutro 700: `#424242` — textos secundários.
- Neutro 500: `#9E9E9E` — bordas, placeholders.
- Neutro 100: `#F5F5F5` — fundos de seções, cards.
- Superfície: `#FFFFFF` — fundo principal.
- Sucesso: `#2E7D32` — confirmações, status “Entregue”.
- Aviso: `#ED6C02` — atrasos, atenção.
- Erro: `#D32F2F` — validações, falhas de pagamento.
- Info: `#0288D1` — mensagens informativas.

Uso e Acessibilidade:
- Contraste mínimo AA: 4.5:1 para corpo de texto; 3:1 para títulos > 24px.
- CTAs: texto sempre em `#FFFFFF`; tamanhos mínimos de toque 44x44dp.

### 4.2 Tipografia
- Família primária: Inter (fallback: Roboto, system-ui, Arial).
- Hierarquia:
  - H1: 28–32 px, semibold
  - H2: 22–24 px, semibold
  - H3: 18–20 px, medium
  - Corpo: 16 px, regular
  - Legenda/Caption: 12–14 px, regular
- Espaçamento: linha 1.4–1.6; entre seções 16–24px.

Consistência:
- Use preenchidos para ações primárias e contornados para secundárias.
- Estados: default, hover, active, disabled (opacidade 38–60%).

### 4.4 Componentes de UI (mobile)
- App Bar: título curto e ação primária à direita (ex.: carrinho).
- Bottom Navigation (4–5 itens): Início, Buscar, Pedidos, Perfil.
- Cards de Produto: imagem 1:1, nome, preço, rating, CTA “Adicionar”.
- Banner de Promoções: carrossel com indicador de página.
- Sheet de Customização: bottom sheet para adicionais e observações.
- Checkout Stepper: Endereço → Pagamento → Revisão.
- Rastreamento: mapa com rota e ETA; status chips coloridos.
- Tabelas simples: extrato do entregador com filtros por período.

---

## 5. Escalabilidade

### 5.1 Estratégias de Escala por Serviço
- Escala horizontal stateless:
  - Serviços de API (Pedidos, Entregas, Notificação, Autenticação) empacotados em contêineres e orquestrados (Kubernetes/ECS) com auto-scaling baseado em CPU, memória e latência p95/p99.
  - Sessões stateless; dados de sessão e rate limits em Redis/KeyDB.
- Escala orientada a filas:
  - Picos de criação de pedidos, cálculo de ETA e envio de notificações amortecidos via RabbitMQ (work queues).
  - Consumidores com auto-scaling por profundidade da fila e tempo em fila.
- Escala de leitura:
  - Cache por recurso “quente” (cardápio, promoções e configurações de unidade) em Redis com invalidação por evento.
  - Read replicas nos bancos transacionais para relatórios operacionais e consultas de alto volume.
- Geodistribuição:
  - Regiões múltiplas para reduzir latência de cliente e aumentar resiliência.
  - Roteamento geográfico no gateway e em DNS (GeoDNS) para direcionar ao POP/cluster mais próximo.

### 5.2 Particionamento e Consistência
- Sharding lógico por “unidade” (loja) e/ou por região:
  - Tabelas de pedidos e entregas particionadas por `store_id` e `created_at`.
  - Serviço de Entregas com partição por célula geográfica (geohash) para matchmaking local.
- Estratégia de consistência:
  - Consistência forte para operações críticas (cobrança, antifraude, confirmação de pagamento).
  - Consistência eventual para catálogos, promoções, tracking em tempo real e contadores.
  - Idempotência em endpoints de criação de pedido e processamento de webhooks.
- Sagas e transações distribuídas:
  - Coreografias com eventos para orquestrar Pedido → Pagamento → Preparo → Entrega.
  - Compensações definidas para cancelamentos e falhas parciais.

### 5.3 Performance e Custo
- Tuning de performance:
  - Circuit breakers, timeouts e retries exponenciais com jitter.
  - Bulkheads para isolar pools de conexão por serviço/cliente.
  - N+1 queries: ORMs configurados com `select`/`join fetch` e paginação estrita.
- Otimização de custo:
  - Autoscaling com “floor” noturno e “burst” em janelas de pico (almoço/jantar).
  - Tiering de armazenamento: dados frios para data lake; TTL para logs e métricas.
  - Orquestração de jobs batch fora de horários de pico.

### 5.4 Resiliência e Alta Disponibilidade
- Multi-AZ por padrão; Multi-Region ativo-ativo para APIs de leitura e ativo-passivo para escrita sensível.
- Degradação graciosa:
  - Queda temporária de recomendações/ratings não bloqueia checkout.
  - Modo “alta demanda” ajusta ETA e aplica fila virtual.
- DR e RPO/RTO:
  - Backups automáticos com testes de restore frequentes.
  - Objetivos: RPO ≤ 5 min; RTO ≤ 30 min para serviços core.

---

## 6. Observabilidade

### 6.1 Pilares e Ferramentas
- Métricas (Datadog):
  - Latência p50/p95/p99 por endpoint e por serviço.
  - Taxa de erro por tipo (4xx, 5xx), saturação (CPU, memória, fila).
  - Negócio: pedidos/min, conversão checkout, tempo médio de preparo, SLA de entrega.
- Logs estruturados (JSON):
  - Correlação com `trace_id`, `span_id`, `order_id`, `store_id`, `driver_id`.
  - Política de retenção por criticidade e mascaramento de PII.
- Tracing distribuído:
  - OpenTelemetry nas bordas e serviços; amostragem dinâmica (mais alta em falhas).
  - Spans para chamadas externas (PSPs, geocoding, push).
- Monitoramento sintético:
  - Uptime Kuma para health checks e fluxos críticos (checkout, login, webhook).
  - Testes de ponta-a-ponta periódicos em produção em release canário.

### 6.2 Dashboards e Alertas
- Dashboards por domínio:
  - Checkout: taxa de sucesso, latência de pagamento, erros por adquirente.
  - Entregas: tempo de alocação, tempo em rota, taxa de reatribuição.
  - Operação de unidade: pedidos em fila, tempo de preparo, indisponibilidades de item.
- Alertas inteligentes:
  - Limiares dinâmicos com detecção de anomalias em p95 e erro 5xx.
  - SLOs e SLIs:
    - Disponibilidade API core ≥ 99.9%.
    - Latência p95 de criação de pedido ≤ 600 ms.
    - Tempo de alocação de entregador p95 ≤ 90 s.
- Runbooks e SRE:
  - Playbooks versionados por tipo de incidente (fila crescendo, saturação DB, PSP fora).
  - Postmortems sem culpa, com ações corretivas rastreáveis.

### 6.3 Boas Práticas Operacionais
- Feature flags com observabilidade:
  - Rollout gradual e “kill switch” visível em dashboards.
- Logging eficiente:
  - Amostragem de logs de sucesso; logs completos em erros.
  - PII/PCI redacted por padrão, com campos “safe” para análise.
- Auditoria:
  - Trilha de auditoria para ações administrativas e mudanças de catálogo/preço.

---

## 7. Segurança

### 7.1 Autenticação, Autorização e Gestão de Segredos
- Autenticação:
  - Spring Security + JWT de curta duração, com refresh tokens rotativos.
  - Suporte a OAuth 2.0/social login; MFA opcional para administradores e suporte.
- Autorização:
  - RBAC por perfil (Cliente, Entregador, Admin de Unidade, Suporte).
  - Escopos por serviço; autorização contextual por `store_id` e região.
- Segredos e chaves:
  - Vault/KMS para segredos, rotação automática e acesso mínimo necessário.
  - Assinatura de webhooks com verificação de tempo e nonce (replay protection).

### 7.2 Proteção de Dados e Conformidade (LGPD)
- Dados em trânsito e em repouso:
  - TLS 1.2+ em todas as comunicações; HSTS no gateway.
  - Criptografia em repouso (AES-256) para dados sensíveis; tokenização de PAN (cartão) via PSP.
- Privacidade e LGPD:
  - Minimização de coleta; bases legais registradas.
  - Consentimento granular para notificações e marketing; registro de consentimentos.
  - Retenção e descarte: políticas por tipo de dado, com anonimização de históricos.
  - Atendimentos a direitos do titular (acesso, correção, exclusão) com SLA.

