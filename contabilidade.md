---
date: "2020-01-18"
title: "Contabilidade"
desc: "Contabilidade for dummies usando planilhas eletrônicas (Excel, Google Sheets)."
tags: [ "draft", "blog" ]
draft: true
---
Meu colégio foi técnico, e foi técnico em contabilidade. Desde então sou fascinado por um conceito chamado de Método das Partidas Dobradas, ou Método Veneziano. Inventado por Luca Pacioli, um padre franciscano de Florença do século 15, é o sistema padrão usado por empresas para registrar transações financeiras. Pode-se dizer que este, sim, foi o criador original da blockchain, se você entender que em um livro contábil as linhas que registram as transações nunca são apagadas; no máximo desfeitas em uma transação inversa.

A premissa básica é que toda condição financeira independente deve estar contida em uma variável chamada conta, e cada transação financeira sempre é registrada na forma de entrada em pelo menos duas contas, nas quais o total de débitos deve ser igual ao total de créditos. Você pode registrar a vida inteira de uma empresa partindo de apenas algumas células de uma planilha:

| yyyy |  mm |  dd | desc | value | from |  to |
| ---- | --- | --- | ---- | ----- | ---- | --- |
| 2020 |  01 |  18 | start | 10000 | capital | bank-account |
| 2020 |  01 |  20 | start | 10000 | bank-account | taxes |

Nesse primeiro exemplo nós iniciamos a contabilidade do ano atualizando o valor financeiro da conta do banco representada por **bank-account** vindo diretamente da conta **capital**. O dinheiro que inicia uma empresa sempre virá de uma conta que se torna credora até o final da empresa. É o dinheiro que foi investido inicialmente na empresa.

Pagamentos de fornecedores, salários, despesas em geral, são lançadas como transações que saem de **bank-account** e entram na respectiva conta.

| yyyy |  mm |  dd | desc | value | from |  to |
| ---- | --- | --- | ---- | ----- | ---- | --- |
| 2020 |  02 |  05 | payday | 1234 | bank-account | employee-jeremias |
| 2020 |  02 |  05 | payday | 2345 | bank-account | employee-senior-joao |
| 2020 |  02 |  10 | adm | 123 | bank-account | provider-office |

Do ponto de vista contábil os valores que saem de uma conta a tornam credora, ou seja, é um saldo devido a ela, e portanto ela fica positiva. No entanto, quando uma conta recebe mais do que transfere ela se torna devedora, e portanto fica negativa. É o contrário do que o leigo está acostumado a ver no extrato do banco.

| saldo | situação | sinal |
| ----- | -------- | ----- |
| negativo | credor | positivo |
| positivo | devedor | negativo |

