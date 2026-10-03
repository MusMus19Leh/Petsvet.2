# 🐾 Sistema de Clínica Veterinária

## 👥 Identificação

**Integrantes:**

* Nome do integrante 1
* Nome do integrante 2
* Nome do integrante 3

**Disciplina:** Nome da disciplina
**Professor:** Nome do professor

---

## 📋 Sobre o projeto

O projeto consiste no desenvolvimento de um sistema para **gerenciamento de uma clínica veterinária**.

A aplicação tem como objetivo facilitar o cadastro e o gerenciamento das informações de **clientes, animais e consultas**, permitindo que os dados sejam armazenados e consultados de forma organizada.

O sistema possui funcionalidades de cadastro, consulta, alteração e exclusão de dados (CRUD), além de consultas utilizando recursos do banco de dados, como **JOINs, Views, Functions e Procedures**.

O projeto foi desenvolvido como atividade acadêmica, com o objetivo de aplicar na prática conceitos de **programação, banco de dados e integração entre aplicação e SGBD**.

---

## 💻 Tecnologias utilizadas

* **Python**
* **Tkinter** — interface gráfica
* **PostgreSQL** — banco de dados
* **Supabase** — hospedagem e gerenciamento do banco de dados
* **GitHub** — versionamento e armazenamento do projeto

---

## 🗄️ Banco de Dados

### SGBD utilizado

O projeto utiliza o **PostgreSQL** como Sistema Gerenciador de Banco de Dados (SGBD).

O banco de dados foi utilizado para armazenar e organizar as informações da clínica veterinária.

### Principais tabelas

O sistema possui tabelas relacionadas às principais informações da clínica:

* **Clientes** — armazena os dados dos clientes da clínica;
* **Pets** — armazena os dados dos animais cadastrados;
* **Consultas** — armazena as informações das consultas realizadas.

As tabelas possuem relacionamentos por meio de **chaves primárias (PK)** e **chaves estrangeiras (FK)**.

---

## 👁️ View

O projeto possui uma **View** utilizada para facilitar a consulta de informações relacionadas ao sistema.

A View permite reunir informações de diferentes tabelas através de relacionamentos, facilitando a visualização dos dados sem a necessidade de escrever novamente toda a consulta SQL.

O script da View está disponível em:

```text
CREATE OR REPLACE VIEW vw_consultas_detalhadas AS
SELECT
    c.id AS id_consulta,
    c.data,
    c.descricao,
    c.valor,
    p.id AS id_pet,
    p.nome AS nome_pet,
    p.especie,
    cl.id AS id_cliente,
    cl.nome AS nome_cliente
FROM consultas c
INNER JOIN pet p
    ON c.id_pet = p.id
INNER JOIN clientes cl
    ON p.id_cliente = cl.id;

CREATE OR REPLACE FUNCTION fn_total_gasto_pet(
    id_pet_param BIGINT
)
RETURNS NUMERIC(10,2)
LANGUAGE plpgsql
AS $$
DECLARE
    total NUMERIC(10,2);
BEGIN

    SELECT COALESCE(SUM(valor), 0)
    INTO total
    FROM consultas
    WHERE id_pet = id_pet_param;

    RETURN total;

END;
$$;

SELECT * FROM vw_consultas_detalhadas;
```

---

## ⚙️ Function

O projeto possui uma **Function** desenvolvida em PostgreSQL para realizar uma operação específica no banco de dados.

O script da Function está disponível em:

```text
CREATE OR REPLACE FUNCTION fn_registrar_consulta(
    p_data DATE,
    p_descricao TEXT,
    p_valor NUMERIC(10,2),
    p_id_pet BIGINT
)
RETURNS VOID
LANGUAGE plpgsql
AS $$
BEGIN

    INSERT INTO consultas (
        data,
        descricao,
        valor,
        id_pet
    )
    VALUES (
        p_data,
        p_descricao,
        p_valor,
        p_id_pet
    );

END;
$$;

SELECT fn_total_gasto_pet(1);
```

---

## 🔄 Procedure

O projeto possui uma **Procedure** desenvolvida em PostgreSQL para executar operações no banco de dados.

O script da Procedure está disponível em:

```text
CREATE OR REPLACE PROCEDURE sp_registrar_consulta(
    p_data DATE,
    p_descricao TEXT,
    p_valor NUMERIC(10,2),
    p_id_pet BIGINT
)
LANGUAGE plpgsql
AS $$
BEGIN

    INSERT INTO consultas (
        data,
        descricao,
        valor,
        id_pet
    )
    VALUES (
        p_data,
        p_descricao,
        p_valor,
        p_id_pet
    );

END;
$$;

CALL sp_registrar_consulta(
    '2026-10-05',
    'Retorno',
    50.00,
    1
);

```

---

## 🐶 Funcionalidades

O sistema permite realizar operações relacionadas ao gerenciamento da clínica veterinária, incluindo:

* Cadastro de clientes;
* Visualização de clientes;
* Exclusão de clientes;
* Cadastro de animais;
* Visualização dos animais cadastrados;
* Cadastro de consultas;
* Visualização das consultas;
* Consulta e organização das informações armazenadas no banco de dados;
* Integração entre a aplicação Python e o banco PostgreSQL.

---

## 📚 Objetivo acadêmico

Este projeto foi desenvolvido com o objetivo de colocar em prática os conhecimentos estudados durante a disciplina, envolvendo:

* Modelagem e criação de banco de dados;
* Criação de tabelas;
* Chaves primárias e estrangeiras;
* Relacionamentos entre tabelas;
* Comandos SQL;
* JOINs;
* Views;
* Functions;
* Procedures;
* Operações CRUD;
* Integração entre aplicação e banco de dados;
* Organização e versionamento do projeto no GitHub.

---

## 👨‍💻 Projeto acadêmico

Projeto desenvolvido para fins acadêmicos.
