# SQL Guide ⚡
> Guia completo para Structured Query Language

## Sumário
1. [Fundamentos](#fundamentos)
2. [SELECT Statements](#select-statements)
3. [Data Manipulation](#data-manipulation)
4. [Joins](#joins)
5. [Funções Agregadas](#funções-agregadas)
6. [Subqueries](#subqueries)
7. [Performance](#performance)
8. [Boas Práticas](#boas-práticas)

## Fundamentos

### Tipos de Comandos
- **DDL** (Data Definition Language)
  - CREATE, ALTER, DROP, TRUNCATE
- **DML** (Data Manipulation Language)
  - SELECT, INSERT, UPDATE, DELETE
- **DCL** (Data Control Language)
  - GRANT, REVOKE
- **TCL** (Transaction Control Language)
  - COMMIT, ROLLBACK, SAVEPOINT

### Estrutura Básica
```sql
-- Criação de tabela
CREATE TABLE usuarios (
    id INT PRIMARY KEY,
    nome VARCHAR(100),
    email VARCHAR(255) UNIQUE,
    data_criacao TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## SELECT Statements

### Queries Básicas
```sql
-- Seleção simples
SELECT nome, email FROM usuarios;

-- Condições WHERE
SELECT * FROM usuarios 
WHERE data_criacao >= '2024-01-01';

-- Ordenação
SELECT * FROM usuarios 
ORDER BY nome ASC, data_criacao DESC;

-- Limitar resultados
SELECT * FROM usuarios 
LIMIT 10 OFFSET 20;
```

### Filtros Avançados
```sql
-- Operadores lógicos
SELECT * FROM produtos 
WHERE (preco >= 100 AND categoria = 'eletrônicos')
   OR (preco >= 50 AND categoria = 'livros');

-- Pattern Matching
SELECT * FROM usuarios 
WHERE email LIKE '%@gmail.com'
  AND nome LIKE 'A%';

-- IN e NOT IN
SELECT * FROM produtos 
WHERE categoria IN ('eletrônicos', 'informática', 'games')
  AND id NOT IN (SELECT produto_id FROM produtos_inativos);
```

## Data Manipulation

### INSERT
```sql
-- Inserção única
INSERT INTO usuarios (nome, email) 
VALUES ('Ana Silva', 'ana@email.com');

-- Múltiplas inserções
INSERT INTO usuarios (nome, email) 
VALUES 
    ('Carlos', 'carlos@email.com'),
    ('Maria', 'maria@email.com');

-- INSERT com SELECT
INSERT INTO usuarios_backup
SELECT * FROM usuarios 
WHERE data_criacao < '2024-01-01';
```

### UPDATE
```sql
-- Atualização simples
UPDATE usuarios 
SET status = 'ativo'
WHERE id = 1;

-- Atualização com JOIN
UPDATE produtos p
JOIN categorias c ON p.categoria_id = c.id
SET p.preco = p.preco * 1.1
WHERE c.nome = 'eletrônicos';
```

### DELETE
```sql
-- Deleção com condição
DELETE FROM usuarios 
WHERE status = 'inativo' 
  AND data_criacao < '2023-01-01';

-- Delete com subquery
DELETE FROM pedidos 
WHERE cliente_id IN (
    SELECT id FROM clientes 
    WHERE status = 'bloqueado'
);
```

## Joins

### Tipos de JOIN
```sql
-- INNER JOIN
SELECT p.nome, c.nome as categoria
FROM produtos p
INNER JOIN categorias c ON p.categoria_id = c.id;

-- LEFT JOIN
SELECT u.nome, p.id as pedido_id
FROM usuarios u
LEFT JOIN pedidos p ON u.id = p.usuario_id;

-- Multiple JOINs
SELECT 
    p.id,
    u.nome as usuario,
    pr.nome as produto,
    e.status as status_entrega
FROM pedidos p
JOIN usuarios u ON p.usuario_id = u.id
JOIN produtos pr ON p.produto_id = pr.id
LEFT JOIN entregas e ON p.id = e.pedido_id;
```

## Funções Agregadas

### Agregações Básicas
```sql
-- Contagem e totais
SELECT 
    categoria_id,
    COUNT(*) as total_produtos,
    AVG(preco) as preco_medio,
    SUM(estoque) as estoque_total
FROM produtos
GROUP BY categoria_id
HAVING COUNT(*) > 5;

-- Análises temporais
SELECT 
    DATE_TRUNC('month', data_pedido) as mes,
    COUNT(*) as total_pedidos,
    SUM(valor) as receita_total
FROM pedidos
GROUP BY DATE_TRUNC('month', data_pedido)
ORDER BY mes;
```

## Subqueries

### Tipos de Subqueries
```sql
-- Subquery no WHERE
SELECT nome, preco
FROM produtos
WHERE preco > (
    SELECT AVG(preco) FROM produtos
);

-- Subquery com EXISTS
SELECT nome
FROM clientes c
WHERE EXISTS (
    SELECT 1 
    FROM pedidos p 
    WHERE p.cliente_id = c.id
    AND p.valor > 1000
);

-- Subquery no FROM
SELECT dept_name, avg_salary
FROM (
    SELECT 
        d.nome as dept_name,
        AVG(f.salario) as avg_salary
    FROM funcionarios f
    JOIN departamentos d ON f.dept_id = d.id
    GROUP BY d.nome
) as dept_stats
WHERE avg_salary > 5000;
```

## Performance

### Índices
```sql
-- Índice simples
CREATE INDEX idx_usuarios_email 
ON usuarios(email);

-- Índice composto
CREATE INDEX idx_pedidos_usuario_data 
ON pedidos(usuario_id, data_pedido);

-- Índice parcial
CREATE INDEX idx_produtos_ativos 
ON produtos(nome) 
WHERE status = 'ativo';
```

### Otimização de Queries
- Use EXPLAIN ANALYZE para entender o plano de execução
- Evite SELECT *
- Prefira JOINs a subqueries quando possível
- Use índices adequadamente
- Limite resultados com LIMIT
- Evite funções em colunas indexadas no WHERE

## Boas Práticas

### Nomenclatura
- Use nomes descritivos para tabelas e colunas
- Mantenha um padrão (snake_case é comum em SQL)
- Evite palavras reservadas como nomes

### Organização
- Indente suas queries adequadamente
- Use comentários para explicar lógicas complexas
- Quebre queries longas em múltiplas linhas

### Segurança
- Sempre use prepared statements
- Implemente controle de acesso adequado
- Faça backup regularmente
- Valide inputs antes de usar em queries

### Manutenção
- Documente mudanças no schema
- Mantenha um histórico de migrações
- Use transações para operações críticas
- Implemente logs de alterações importantes

---

## Recursos Adicionais
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [MySQL Documentation](https://dev.mysql.com/doc/)
- [SQL Style Guide](https://www.sqlstyle.guide/)
