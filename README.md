# Sistema de Gestão de Concessionária e Revenda de Veículos (SQL)

<div align="center">

![SQL](https://img.shields.io/badge/SQL-DDL%20%7C%20DML%20%7C%20DQL-0057FF?style=for-the-badge&logo=postgresql&logoColor=white)
![Data Integrity](https://img.shields.io/badge/Integridade-Referencial%20Estrita-161B22?style=for-the-badge&logo=databricks&logoColor=FF3621)
![Views](https://img.shields.io/badge/Views-Otimizadas-161B22?style=for-the-badge&logo=apache&logoColor=D22128)
![Database](https://img.shields.io/badge/Database-db__revenda__miguel-161B22?style=for-the-badge&logo=mysql&logoColor=4479A1)

**Modelagem relacional completa, regras de negócio em nível de banco de dados, constraints de integridade e views analíticas.**

</div>

---

## 📋 Visão Geral

Este projeto apresenta a modelagem e implementação do banco de dados relacional **`db_revenda_miguel`**, projetado para gerenciar as operações de uma concessionária de veículos, abrangendo:
- Cadastro de clientes e vendedores.
- Catálogo de veículos com validações de ano, preço e placas únicas.
- Controle de estoque físico (pátios, quantidades e status de disponibilidade).
- Registro detalhado de vendas com vínculo de itens vendidos (relação N:N) e formas de pagamento.
- Camada de abstração analítica através de **SQL Views**.

---

## 🏛️ Modelo de Entidades e Relacionamentos (ERD)

```mermaid
erDiagram
    CLIENTES ||--o{ VENDAS : "realiza"
    VENDEDORES ||--o{ VENDAS : "intermedeia"
    VENDAS ||--|{ VENDA_VEICULO : "contém"
    VEICULOS ||--|{ VENDA_VEICULO : "composto_por"
    VEICULOS ||--|| ESTOQUE : "alocado_em"

    CLIENTES {
        int cliente_id PK
        string nome
        string cpf UK
        string telefone
        string email UK
        date data_cadastro
    }

    VEICULOS {
        int veiculo_id PK
        string marca
        string modelo
        int ano
        decimal preco
        string placa UK
    }

    VENDEDORES {
        int vendedor_id PK
        string nome
        string telefone
        string email UK
        date data_admissao
    }

    ESTOQUE {
        int veiculo_id PK, FK
        int quantidade
        string local_armazenamento
        string status
        date data_ultima_atualizacao
    }

    VENDAS {
        int venda_id PK
        int cliente_id FK
        int vendedor_id FK
        date data_venda
        decimal valor_total
        string forma_pagamento
    }

    VENDA_VEICULO {
        int venda_id PK, FK
        int veiculo_id PK, FK
        int quantidade
        decimal preco_unitario
    }
```

---

## 🛡️ Regras de Negócio e Constraints Implementadas

O esquema foi desenvolvido aplicando regras de validação diretamente na camada de banco de dados:

1. **Unicidade de Registros:**
   - CPFs e e-mails de clientes são únicos (`UNIQUE`).
   - Placas de veículos são únicas (`UNIQUE`) para evitar duplicidade de ativos.
2. **Restrições de Domínio (Check Constraints):**
   - **Ano do Veículo:** `CHECK (ano >= 1900 AND ano <= YEAR(CURDATE()))` — impede o cadastro de anos inválidos ou no futuro.
   - **Preço do Ativo:** `CHECK (preco > 0)` — assegura que nenhum veículo possua valor nulo ou negativo.
   - **Quantidade em Estoque:** `CHECK (quantidade >= 0)` — evita estoque negativo.
   - **Valor Total de Vendas:** `CHECK (valor_total >= 0)`.
3. **Integridade Referencial:**
   - Relacionamento N:N entre Vendas e Veículos resolvido pela tabela associativa `venda_veiculo`, utilizando chave primária composta `PRIMARY KEY (venda_id, veiculo_id)`.

---

## 📊 Views Analíticas

O projeto implementa views para simplificar relatórios e relatórios de auditoria:

### 1. `view_vendas_detalhes`
Consolida os dados de clientes, vendedores e valores transacionados através de `JOINs` otimizados:
```sql
SELECT * FROM view_vendas_detalhes;
```
*Campos retornados:* `venda_id`, `cliente_nome`, `cpf`, `vendedor_nome`, `data_venda`, `valor_total`, `forma_pagamento`.

### 2. `view_estoque_veiculos`
Cruza o cadastro de veículos com seu posicionamento físico nos pátios de estocagem:
```sql
SELECT * FROM view_estoque_veiculos;
```
*Campos retornados:* `veiculo_id`, `marca`, `modelo`, `ano`, `quantidade`, `status`, `local_armazenamento`, `placa`.

---

## 🚀 Como Executar o Script

O script SQL (`Miguel.sql`) é compatível com os principais SGBDs relacionais (MySQL, MariaDB ou PostgreSQL com pequenos ajustes de funções de data).

### No MySQL / MariaDB Workbench ou CLI:
```bash
# Conectar ao banco
mysql -u seu_usuario -p

# Executar o script completo
source caminho/para/Miguel.sql;
```

---

## 📁 Estrutura do Repositório
```text
.
├── README.md        # Documentação técnica do modelo de dados
└── Miguel.sql       # Script DDL, DML (dados de teste), Constraints e Views
```

---

<div align="center">
Desenvolvido por <strong>Miguel Campião Arraes</strong>
</div>
