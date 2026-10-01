# Fazenda Feliz - Estrutura de Monetização Real (Legal e Segura)

## Objetivo
Implementar um sistema de saldo real para usuários, com fluxo seguro, validação de CPF e PIX, precisão na gestão financeira e redução de risco de fraude.

## Escopo
- Usuário registra conta
- Saldo interno em moedas do jogo
- Conversão para saldo real
- Cadastro de CPF e PIX
- Solicitação de saque
- Taxa de processamento
- Registro de auditoria
- Fluxo de pagamentos para compra de pacotes (opcional)

## Arquitetura da solução

### Frontend (Jogo)
- Login e cadastro
- Tela de saldo e extrato
- Tela de carteira PIX
- Botão de saque
- Loja e compra de pacotes (opcional)

### Backend (API)
- Autenticação JWT
- Rotas de usuário
- Rotas de saque
- Rotas de pagamento
- Banco de dados relacional
- Logs e auditoria

### Gateway de pagamento
- Stripe, Mercado Pago ou PayPal
- Ambiente sandbox ou production

### Banco de dados
- SQLite para protótipo
- Postgres/MySQL em produção

---

## Regras recomendadas

### Conversão da moeda do jogo
- 10.000 moedas = R$ 1,00
- Saldo real apenas após conversão de jogo para real
- Exigência de cadastro do CPF antes de sacar

### Saque mínimo
- R$ 10,00

### Taxa de saque
- 2,5% por operação
- Exemplo: R$ 100,00 -> tarifa R$ 2,50 -> líquido R$ 97,50

### Limites
- saque diário: R$ 5.000
- saque mensal: R$ 25.000
- primeiro saque após 48h de cadastro

### Regras para risco
- bloqueio em IP suspeito
- múltiplas solicitações em curto prazo
- CPF inválido
- chave PIX não verificada
- conta muito recente

---

## Banco de dados sugerido

```sql
CREATE TABLE usuarios (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  nome TEXT NOT NULL,
  email TEXT UNIQUE NOT NULL,
  senha_hash TEXT NOT NULL,
  cpf TEXT,
  cpf_validado INTEGER DEFAULT 0,
  pix TEXT,
  pix_validada INTEGER DEFAULT 0,
  saldo_moedas INTEGER DEFAULT 0,
  saldo_real REAL DEFAULT 0,
  bloqueado INTEGER DEFAULT 0,
  criado_em TEXT DEFAULT CURRENT_TIMESTAMP,
  atualizado_em TEXT DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE saques (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  usuario_id INTEGER NOT NULL,
  valor_solicitado REAL NOT NULL,
  taxa REAL NOT NULL,
  valor_liquido REAL NOT NULL,
  status TEXT DEFAULT 'pendente',
  cpf_validado INTEGER DEFAULT 0,
  pix_validada INTEGER DEFAULT 0,
  criado_em TEXT DEFAULT CURRENT_TIMESTAMP,
  processado_em TEXT
);

CREATE TABLE pagamentos (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  usuario_id INTEGER NOT NULL,
  metodo TEXT NOT NULL,
  valor REAL NOT NULL,
  status TEXT NOT NULL,
  referencia TEXT,
  provider TEXT,
  criado_em TEXT DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE auditoria (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  usuario_id INTEGER,
  tipo TEXT NOT NULL,
  descricao TEXT NOT NULL,
  ip TEXT,
  created_at TEXT DEFAULT CURRENT_TIMESTAMP
);
```

---

## Fluxo de uso real

### 1. Cadastro do usuário
- nome
- email
- senha
- data de nascimento (opcional em ambiente de produção)

### 2. Ganho no jogo
- usuário planta e colhe
- deve receber moedas do jogo

### 3. Conversão para saldo real
- define regra de conversão
- ex.: 10.000 moedas = R$ 1,00

### 4. Cadastro de CPF e PIX
- usuário informa CPF
- valida dígitos verificadores
- usuário informa chave PIX
- chave deve ser validada

### 5. Solicitação de saque
- usuário solicita valor mínimo
- sistema checa saldo real, limites e validações
- cria registro de saque em status pendente
- debita saldo real de forma controlada

### 6. Processamento
- payout real via PIX ou conta bancária
- marca status como concluído
- notifica usuário

---

## Validação de CPF (JavaScript)

```javascript
function validarCPF(cpf) {
  cpf = cpf.replace(/\D/g, '');
  if (cpf.length !== 11) return false;
  if (/^(\d)\1{10}$/.test(cpf)) return false;

  let soma = 0;
  for (let i = 0; i < 9; i++) {
    soma += parseInt(cpf.charAt(i)) * (10 - i);
  }
  let resto = (soma * 10) % 11;
  if (resto === 10 || resto === 11) resto = 0;
  if (resto !== parseInt(cpf.charAt(9))) return false;

  soma = 0;
  for (let i = 0; i < 10; i++) {
    soma += parseInt(cpf.charAt(i)) * (11 - i);
  }
  resto = (soma * 10) % 11;
  if (resto === 10 || resto === 11) resto = 0;
  if (resto !== parseInt(cpf.charAt(10))) return false;

  return true;
}
```

---

## Fluxo de automação (recomendado)

### Backend Node.js
- Express
- SQLite ou Postgres
- bcrypt para senha
- JWT para sessão
- Stripe / Mercado Pago SDK

### Endpoints sugeridos
- `POST /api/auth/register`
- `POST /api/auth/login`
- `GET /api/user/me`
- `POST /api/user/cpf`
- `POST /api/user/pix`
- `POST /api/saldo/converter`
- `POST /api/saque/solicitar`
- `GET /api/saque/historico`
- `POST /api/pagamento/checkout`

---

## Segurança mínima
- JWT com expiração
- hash de senha com bcrypt
- proteção contra brute force
- validação de CPF e PIX
- log de operações financeiras
- limitar IP e múltiplos saques
- rejeitar conta bloqueada

---

## Checklist legal
- termos de uso
- politica de privacidade
- regra de saque e taxa
- política antifraude
- cadastro de CPF e PIX obrigatório
- conta bancária para operação do negócio
- contador/advogado para revisão do modelo

---

## Recomendação de operação
Para operação real no Brasil, o ideal é:
- abrir MEI ou empresa compatível;
- usar conta bancária da empresa;
- usar Stripe/Mercado Pago com conta legalizada;
- manter registro contábil das transações;
- não publicar promessas de rendimento garantido;
- tratar desenvolvimento e pagamento como negócio formal.

---

## Próximo passo
A próxima etapa pode ser:
1. criar backend de autenticação e saldo;
2. criar rotas de CPF/PIX e saque;
3. integrar gateway de pagamento em sandbox;
4. testar fluxo completo em ambiente controlado.

Esse documento é a base para desenvolvimento seguro e orientado à legislação brasileira.
