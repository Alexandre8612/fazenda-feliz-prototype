# Fazenda Feliz - Monetização e Compliance

## Objetivo
Este documento define a estrutura para monetização de forma transparente, com foco em:
- jogo de habilidade e progresso, não em sorte;
- pagamentos por compra de itens/assinaturas (quando aplicável);
- processamento de saques por PIX com validação de dados;
- registro de auditoria para prevenir fraudes;
- uso de gateways pagos e compliance mínimo para operação no Brasil.

## Importante
Não é aconselhável anunciar que o jogador "ganha dinheiro real" sem uma estrutura legal, tributária e operacional completa. O caminho mais seguro é:
- usar uma plataforma de pagamento legalizada;
- validar CPF e chave PIX antes do saque;
- limitar saques e manter regime de auditoria;
- consultar contador/advogado para adequação ao seu modelo de negócio.

## Modelo sugerido
### 1) Modelo de jogo
- O usuário precisa obter moedas no jogo por progresso, plantio e colheita.
- O saldo em moeda do jogo pode ser convertido em um saldo real apenas mediante regras explícitas.
- A lógica deve ser clara: não é cassino, é jogo de habilidade e gestão.

### 2) Monetização permissível
- compras de itens premium, passes de temporada, pacotes de moedas;
- anúncios de terceiros;
- assinatura opcional;
- taxa de saque real, se o sistema for aprovado e regulamentado.

### 3) Cuidados legais
- manter política de privacidade;
- manter termos de uso;
- validar maioridade;
- armazenar logs de transações;
- permitir revisão manual em casos suspeitos.

## Estrutura de fluxos
### Fluxo de compra
- usuário faz login;
- entra em loja;
- escolhe pacote ou item;
- processa pagamento via Stripe / Mercado Pago / PayPal;
- credita moedas ou benefícios no jogo.

### Fluxo de saque
- usuário solicita retirada do saldo;
- valida CPF e chave PIX;
- calcula taxa de processamento (ex.: 2,5%);
- gera registro de saque em fila;
- processa via gateway ou conta financeira;
- notifica usuário por e-mail/SMS;
- registra log de auditoria.

## Regras mínimas recomendadas
- valor mínimo de saque: R$ 10,00;
- taxa de saque: 2,5% ou outra definida;
- CPF obrigatório para saque;
- chave PIX obrigatória e confirmada;
- contas novas devem aguardar 48h para primeiro saque;
- limitação de saques por dia/mês;
- auditoria de múltiplos saques e IP suspeitos;
- bloqueio manual em caso de risco.

## Estrutura sugerida de tabela
```sql
CREATE TABLE usuarios (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  nome TEXT NOT NULL,
  email TEXT UNIQUE NOT NULL,
  cpf TEXT,
  cpf_validado INTEGER DEFAULT 0,
  pix TEXT,
  pix_validada INTEGER DEFAULT 0,
  saldo_real REAL DEFAULT 0,
  saldo_jogo REAL DEFAULT 0,
  maioridade INTEGER DEFAULT 0,
  bloqueado INTEGER DEFAULT 0,
  criado_em TEXT DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE saques (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  usuario_id INTEGER,
  valor_solicitado REAL,
  taxa REAL,
  valor_liquido REAL,
  status TEXT DEFAULT 'pendente',
  cpf_validado INTEGER DEFAULT 0,
  pix_validada INTEGER DEFAULT 0,
  criado_em TEXT DEFAULT CURRENT_TIMESTAMP,
  processado_em TEXT
);

CREATE TABLE pagamentos (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  usuario_id INTEGER,
  metodo TEXT,
  valor REAL,
  status TEXT,
  provider TEXT,
  provider_id TEXT,
  criado_em TEXT DEFAULT CURRENT_TIMESTAMP
);
```

## Exemplos de gateways
- Stripe: para cartão e checkout seguro
- Mercado Pago: para boleto, cartão e PIX
- PayPal: para pagamentos internacionais ou carteira
- banco/conta digital para repasse financeiro ao dono do jogo

## Recomendação de operação
Antes de abrir uso real:
- formalizar o negócio (MEI ou empresa pela estrutura adequada);
- contratar advogado especialista em direito digital/consumo;
- contratar contador para tributação e repasses;
- revisar termos de uso e política de privacidade;
- garantir autorização legal do gateway de pagamento;
- separar contas do jogo e da empresa;
- manter escopo de responsabilidade clara para cada ação.

## Conclusão
O jogo pode ter uma estrutura realista e legal para monetização, mas a operação precisa ser tratada como negócio financeiro e não como "simulação" pura. O mais seguro é deixar o sistema preparado para:
- autenticação forte;
- regras de saque transparentes;
- validação de CPF e PIX;
- logs e auditoria;
- gateways de pagamento reais;
- revisão jurídica antes do lançamento definitivo.
