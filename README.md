# Atualizações do Sistema Bancário em Python (V2)

Esta atualização traz melhorias significativas para o sistema bancário, focando em modularização do código e adição de novas funcionalidades essenciais para o gerenciamento de usuários e contas.

## Melhorias na Modularização

As operações existentes de depósito, saque e extrato foram refatoradas em funções dedicadas, seguindo regras específicas de passagem de argumentos para demonstrar diferentes abordagens de design de funções em Python:

-   **`depositar(saldo, valor, extrato, /)`**: Esta função agora aceita argumentos **apenas por posição**. Isso significa que `saldo`, `valor` e `extrato` devem ser passados na ordem correta, sem o uso de palavras-chave.
    -   **Retorno**: Retorna o `saldo` atualizado e o `extrato` da conta.

-   **`sacar(*, saldo, valor, extrato, limite, numero_saques, limite_saques)`**: Esta função agora aceita argumentos **apenas por nome (keyword-only)**. Todos os argumentos devem ser passados utilizando suas palavras-chave (`saldo=...`, `valor=...`, etc.), o que aumenta a clareza e evita erros de ordem.
    -   **Retorno**: Retorna o `saldo` atualizado, o `extrato` e o `numero_saques` realizados.

-   **`exibir_extrato(saldo, /, *, extrato)`**: Esta função utiliza uma combinação de argumentos **por posição e por nome**. `saldo` deve ser passado por posição, enquanto `extrato` deve ser passado por nome.

## Novas Funcionalidades

Foram adicionadas duas novas funcionalidades cruciais para expandir as capacidades do sistema:

### 1. Cadastro de Usuário (`criar_usuario`)

-   Permite o cadastro de novos clientes no sistema.
-   Cada usuário é composto por: `nome`, `data de nascimento`, `cpf` e `endereço`.
-   O `CPF` é armazenado apenas com números e é utilizado como identificador único; o sistema impede o cadastro de usuários com CPFs duplicados.
-   O `endereço` é uma string formatada como: `logradouro, nro - bairro - cidade/sigla estado`.

### 2. Cadastro de Conta Corrente (`criar_conta_corrente`)

-   Permite a criação de novas contas bancárias e as vincula a um usuário existente.
-   Cada conta é composta por: `agência`, `número da conta` e `usuário` (referência ao objeto do usuário).
-   O `número da conta` é gerado sequencialmente, iniciando em 1.
-   O `número da agência` é fixo: `"0001"`.
-   Um usuário pode ter múltiplas contas, mas cada conta pertence a apenas um usuário.

### 3. Listagem de Contas (`listar_contas`)

-   Uma nova função foi adicionada para listar todas as contas cadastradas no sistema, exibindo a agência, número da conta e o nome do titular.

## Estrutura do Código

O código foi refatorado para incluir uma função `main()` que orquestra o fluxo principal do programa, gerenciando as variáveis de estado (saldo, extrato, usuários, contas) e integrando todas as novas funções através de um menu interativo no terminal.



# Atualizações do Sistema Bancário em Python (V3) - Modelagem Orientada a Objetos

Esta versão representa uma refatoração significativa do sistema bancário, introduzindo a **Programação Orientada a Objetos (POO)** para melhorar a organização, modularidade e manutenibilidade do código. As estruturas de dados baseadas em dicionários foram substituídas por classes e objetos, representando de forma mais fiel as entidades do mundo real.

## Principais Mudanças e Novas Classes:

### 1. Classe `Cliente` e `PessoaFisica`

-   **`Cliente`**: Classe base que representa um cliente do banco, contendo atributos como `endereço` e uma lista de `contas` associadas. Inclui métodos para `realizar_transacao` e `adicionar_conta`.
-   **`PessoaFisica`**: Subclasse de `Cliente`, adicionando atributos específicos para pessoas físicas, como `nome`, `data_nascimento` e `cpf`.

### 2. Classe `Conta`

-   Representa uma conta bancária, encapsulando `saldo`, `número` da conta, `agência`, o `cliente` associado e um `histórico` de transações.
-   Métodos como `sacar` e `depositar` foram movidos para esta classe, operando diretamente no saldo da conta.
-   Inclui um método de classe `nova_conta` para facilitar a criação de novas instâncias de conta.

### 3. Classes de Transação (`Transacao`, `Saque`, `Deposito`)

-   **`Historico`**: Nova classe para gerenciar o histórico de transações de uma conta, armazenando detalhes como tipo, valor e data da transação.
-   **`Transacao`**: Classe base abstrata para todas as operações financeiras, com um atributo `valor`.
-   **`Saque`**: Subclasse de `Transacao`, implementa a lógica específica para registrar um saque no histórico da conta.
-   **`Deposito`**: Subclasse de `Transacao`, implementa a lógica específica para registrar um depósito no histórico da conta.

## Refatoração das Funções Existentes:

As funções de alto nível (`depositar_operacao`, `sacar_operacao`, `exibir_extrato_operacao`, `criar_cliente`, `criar_conta`, `listar_contas`) foram adaptadas para interagir com os novos objetos e métodos das classes, garantindo que a lógica de negócios esteja encapsulada dentro das respectivas classes.

## Benefícios da Modelagem POO:

-   **Modularidade**: O código está mais organizado e dividido em unidades lógicas (classes), facilitando a compreensão e manutenção.
-   **Reusabilidade**: As classes podem ser reutilizadas em diferentes partes do sistema ou em projetos futuros.
-   **Extensibilidade**: Adicionar novas funcionalidades ou tipos de conta/cliente é mais simples, pois o design permite estender as classes existentes sem modificar o código base.
-   **Encapsulamento**: Os dados e o comportamento relacionado a uma entidade (como uma conta ou cliente) estão agrupados, protegendo a integridade dos dados.

Esta atualização estabelece uma base sólida para futuras expansões e melhorias no sistema bancário, seguindo as melhores práticas de desenvolvimento de software com POO.




# Atualizações do Sistema Bancário em Python (V4) - Limite de Transações e Data/Hora

Esta versão do sistema bancário introduz importantes funcionalidades relacionadas ao controle de transações e à exibição de informações detalhadas no extrato, utilizando os conhecimentos adquiridos sobre manipulação de data e hora.

## Novas Funcionalidades e Melhorias:

### 1. Limite de Transações Diárias por Conta

-   **Controle de Saques Diários**: A classe `Conta` agora gerencia um contador de saques diários (`_saques_hoje`) e a data do último saque (`_data_ultimo_saque`).
-   **Restrição de 10 Transações**: Foi implementado um limite de 10 transações (saques) por conta por dia. Se o usuário tentar realizar um saque após atingir esse limite, uma mensagem informativa será exibida.
-   **Reinicialização Diária**: O contador de saques diários é reinicializado automaticamente a cada novo dia, garantindo que o limite seja aplicado corretamente por período de 24 horas.

### 2. Exibição de Data e Hora no Extrato

-   **Timestamp nas Transações**: A classe `Historico` agora armazena a data e a hora exatas de cada transação (depósito ou saque) no formato `dd-mm-aaaa HH:MM:SS`.
-   **Extrato Detalhado**: A função `exibir_extrato_operacao` foi atualizada para incluir a data e a hora de cada movimentação, proporcionando um extrato mais completo e transparente para o usuário.

## Impacto no Código:

-   **Classe `Conta`**: Adição dos atributos `_saques_hoje` e `_data_ultimo_saque`, e modificação do método `sacar` para incluir a lógica de verificação e contagem de transações diárias.
-   **Classe `Historico`**: O método `adicionar_transacao` foi ajustado para incluir o timestamp atual ao registrar cada operação.
-   **Função `exibir_extrato_operacao`**: A formatação da saída do extrato foi alterada para exibir a data e hora de cada transação, além do tipo e valor.

Essas melhorias tornam o sistema bancário mais robusto em termos de controle de transações e fornecem informações mais detalhadas e úteis para o usuário, aprimorando a experiência geral do sistema.
