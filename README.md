# Desafio_Stored_Procedure

 Esta stored procedure foi desenvolvida para fornecer uma visão completa do extrato bancário de um cliente. Ao receber como parâmetro o ID do cliente, a rotina retorna:

* O saldo atual do cliente

* A lista das últimas 10 transações, incluindo:

    * ID da transação
    * Tipo da transação (depósito ou retirada)
    * Breve descrição
    * Valor da transação
    * Data da operação

 O objetivo é tornar o acompanhamento financeiro mais simples e rápido, reunindo em uma única chamada as informações mais relevantes do histórico bancário do cliente.

    * Código do Desafio:

    ```sql
    CREATE OR REPLACE PROCEDURE ver_extrato(
        IN p_cliente_id INTEGER
    )
    LANGUAGE plpgsql
    AS $$
    DECLARE
        saldo_atual INTEGER;
        transacao RECORD;
        contador INTEGER := 0;
    BEGIN
        SELECT saldo INTO saldo_atual
        FROM clients
        WHERE id = p_cliente_id;

        RAISE NOTICE 'Saldo atual do cliente: %', saldo_atual;

        RAISE NOTICE 'Últimas 10 transações do cliente: ';
        FOR transacao IN
            SELECT *
            FROM transactions
            WHERE cliente_id = p_cliente_id 
            ORDER BY realizada_em DESC
            LIMIT 10
        LOOP
            contador := contador + 1;
            RAISE NOTICE 'ID: %, Tipo: %, Descrição: %, Valor: %, Data: %', transacao.id, transacao.tipo, transacao.descricao, transacao.valor, transacao.realizada_em;
            EXIT WHEN contador >= 10;
        END LOOP;
    END;
    $$;
    ```