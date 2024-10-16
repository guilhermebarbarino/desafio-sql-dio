# desafio-sql-dio


### Etapa 1:

**Objetivo do Desafio:**
Criar um esquema conceitual para um sistema de gestão de contas e entregas com os seguintes requisitos:

1. **Clientes PJ e PF:**
   - Uma conta pode pertencer a um cliente do tipo Pessoa Física (PF) ou Pessoa Jurídica (PJ), mas **nunca os dois ao mesmo tempo**.
   - O sistema deve permitir que os dados de uma conta sejam associados a um cliente, incluindo informações como nome, CPF/CNPJ, endereço, etc.

2. **Formas de Pagamento:**
   - Cada conta deve ter associada uma ou mais formas de pagamento (exemplo: cartão de crédito, boleto bancário, PIX).
   - A modelagem deve permitir cadastrar múltiplas formas de pagamento para um cliente.

3. **Entrega:**
   - O sistema deve gerenciar o status da entrega de pedidos, com informações sobre o código de rastreio e o status atual (exemplo: "pendente", "em trânsito", "entregue").
   - Cada pedido terá uma única entrega, mas pode ser atualizado conforme a entrega progride.

**Ferramentas Utilizadas:**
- O modelo será implementado usando **SQL** ou **MongoDB** (dependendo da escolha), mas com o foco principal em SQL relacional.
- O projeto deverá ser armazenado em um **repositório GitHub** para avaliação e compartilhamento.

**Esquema Conceitual:**

1. **Tabela `Clientes`:**
   - `id_cliente`: Chave primária
   - `tipo_cliente`: Enum ('PF', 'PJ') para diferenciar Pessoa Física de Jurídica
   - `nome` (ou `razao_social` para PJ)
   - `cpf_cnpj`
   - `endereco`

2. **Tabela `Contas`:**
   - `id_conta`: Chave primária
   - `id_cliente`: Chave estrangeira referenciando a tabela `Clientes`
   - `saldo`: Valor atual da conta
   - `data_criacao`: Data da criação da conta

3. **Tabela `Formas_de_Pagamento`:**
   - `id_pagamento`: Chave primária
   - `id_conta`: Chave estrangeira referenciando a tabela `Contas`
   - `tipo_pagamento`: Enum ('Cartão de Crédito', 'Boleto', 'PIX')
   - `detalhes_pagamento`: Informações adicionais, como número do cartão ou código de boleto

4. **Tabela `Entregas`:**
   - `id_entrega`: Chave primária
   - `id_conta`: Chave estrangeira para a conta que originou o pedido
   - `status`: Enum ('pendente', 'em trânsito', 'entregue')
   - `codigo_rastreio`: Código único para rastreio da entrega

---

### Etapa 2: 

**Passo 1: Criação do Esquema Relacional (SQL)**

```sql
-- Criação da tabela de Clientes (PF ou PJ)
CREATE TABLE Clientes (
    id_cliente INT PRIMARY KEY AUTO_INCREMENT,
    tipo_cliente ENUM('PF', 'PJ') NOT NULL,
    nome VARCHAR(100) NOT NULL,
    cpf_cnpj VARCHAR(20) UNIQUE NOT NULL,
    endereco VARCHAR(255) NOT NULL
);

-- Criação da tabela de Contas associada aos Clientes
CREATE TABLE Contas (
    id_conta INT PRIMARY KEY AUTO_INCREMENT,
    id_cliente INT,
    saldo DECIMAL(10, 2) DEFAULT 0,
    data_criacao DATE NOT NULL,
    FOREIGN KEY (id_cliente) REFERENCES Clientes(id_cliente)
);

-- Criação da tabela de Formas de Pagamento
CREATE TABLE Formas_de_Pagamento (
    id_pagamento INT PRIMARY KEY AUTO_INCREMENT,
    id_conta INT,
    tipo_pagamento ENUM('Cartão de Crédito', 'Boleto', 'PIX') NOT NULL,
    detalhes_pagamento VARCHAR(255),
    FOREIGN KEY (id_conta) REFERENCES Contas(id_conta)
);

-- Criação da tabela de Entregas
CREATE TABLE Entregas (
    id_entrega INT PRIMARY KEY AUTO_INCREMENT,
    id_conta INT,
    status ENUM('pendente', 'em trânsito', 'entregue') NOT NULL,
    codigo_rastreio VARCHAR(100) UNIQUE NOT NULL,
    FOREIGN KEY (id_conta) REFERENCES Contas(id_conta)
);
```

**Passo 2: Inserção de Dados**

Aqui você pode criar alguns dados de exemplo para testar o funcionamento do esquema:

```sql
-- Inserindo Clientes PF e PJ
INSERT INTO Clientes (tipo_cliente, nome, cpf_cnpj, endereco) VALUES 
('PF', 'João Silva', '12345678901', 'Rua A, 123'),
('PJ', 'Empresa X', '98765432000199', 'Av. B, 456');

-- Inserindo Contas
INSERT INTO Contas (id_cliente, saldo, data_criacao) VALUES 
(1, 1000.00, '2024-01-01'), 
(2, 50000.00, '2024-01-02');

-- Inserindo Formas de Pagamento
INSERT INTO Formas_de_Pagamento (id_conta, tipo_pagamento, detalhes_pagamento) VALUES 
(1, 'Cartão de Crédito', '**** **** **** 1234'),
(1, 'PIX', 'Chave PIX 123456'),
(2, 'Boleto', 'Boleto XYZ123');

-- Inserindo Entregas
INSERT INTO Entregas (id_conta, status, codigo_rastreio) VALUES 
(1, 'pendente', 'RA123456BR'),
(2, 'em trânsito', 'RA654321BR');
```

**Passo 3: Consulta dos Dados**

Por fim, faça consultas para verificar os dados e garantir que o esquema funciona corretamente:

```sql
-- Verificar todos os Clientes e suas Contas
SELECT Clientes.nome, Clientes.tipo_cliente, Contas.saldo 
FROM Clientes
INNER JOIN Contas ON Clientes.id_cliente = Contas.id_cliente;

-- Verificar as formas de pagamento associadas a uma conta
SELECT Contas.id_conta, Formas_de_Pagamento.tipo_pagamento, Formas_de_Pagamento.detalhes_pagamento 
FROM Contas
INNER JOIN Formas_de_Pagamento ON Contas.id_conta = Formas_de_Pagamento.id_conta;

-- Verificar status das entregas
SELECT Contas.id_conta, Entregas.status, Entregas.codigo_rastreio 
FROM Contas
INNER JOIN Entregas ON Contas.id_conta = Entregas.id_conta;
```

