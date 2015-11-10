


**Table of Contents**

<!-- Ignorem o HTML embaixo. Ele serve para fazer a tabela de conteúdos -->

<ul>
<li><a href="#uiaction">UIAction</a><ul>
<li><a href="#loginaction">LoginAction</a></li>
<li><a href="#logoutaction">LogoutAction</a></li>
<li><a href="#sentaclienteaction">SentaClienteAction</a></li>
<li><a href="#reservarmesaaction">ReservarMesaAction</a></li>
<li><a href="#cancelareservaaction">CancelaReservaAction</a></li>
<li><a href="#visualizamesassujasaction">VisualizaMesasSujasAction</a></li>
<li><a href="#liberamesaaction">LiberaMesaAction</a></li>
<li><a href="#iniciarpreparacaoaction">IniciarPreparacaoAction</a></li>
</ul>
</li>
<li><a href="#atendemesasequence">AtendeMesaSequence</a></li>
<li><a href="#fechamesasequence">FechaMesaSequence</a></li>
<li><a href="#restaurantoperationservice">RestaurantOperationService</a><ul>
<li><a href="#getmesasparapessoas-int">getMesasPara(pessoas: int)</a></li>
<li><a href="#sentaclientemesa-mesa">sentaCliente(mesa: Mesa)</a></li>
<li><a href="#getmesassemreserva">getMesasSemReserva</a></li>
<li><a href="#reservamesamesa-mesa-horario-time">reservaMesa(mesa: Mesa, horario: Time)</a></li>
<li><a href="#cancelareservamesa-mesa">cancelaReserva(mesa: Mesa)</a></li>
<li><a href="#getmesassujas">getMesasSujas</a></li>
<li><a href="#liberamesamesa-mesa">liberaMesa(mesa: Mesa)</a></li>
<li><a href="#getmesasparaatendimentofuncionario-garçom">getMesasParaAtendimento(funcionario: Garçom)</a></li>
<li><a href="#getcardapio">getCardapio</a></li>
<li><a href="#criapedidomesa-mesa-pedido-pedido">criaPedido(mesa: Mesa, pedido: Pedido)</a></li>
<li><a href="#getmesasabertasgarçom-garçom">getMesasAbertas(garçom: Garçom)</a></li>
<li><a href="#getpreçopedidomesa-mesa">getPreçoPedido(mesa: Mesa)</a></li>
<li><a href="#fechamesamesa-mesa-aceita-boolean">fechaMesa(mesa: Mesa, aceita: boolean)</a></li>
</ul>
</li>
<li><a href="#database">Database</a><ul>
<li><a href="#hasturnoativo">hasTurnoAtivo</a></li>
<li><a href="#getturnoativo">getTurnoAtivo</a></li>
<li><a href="#getfuncionarioid-string">getFuncionario(ID: String)</a></li>
<li><a href="#getmesassetor-setor">getMesas(setor: Setor)</a></li>
<li><a href="#getmesas">getMesas</a></li>
<li><a href="#atualizadespensaitensaremover-list-ingrediente">atualizaDespensa(itensARemover: List Ingrediente)</a></li>
<li><a href="#getcardapio-1">getCardapio</a></li>
</ul>
</li>
<li><a href="#turno">Turno</a><ul>
<li><a href="#addpedidopedido-pedido">addPedido(pedido: Pedido)</a></li>
<li><a href="#getpedidos">getPedidos</a></li>
<li><a href="#addcustocusto-double">addCusto(custo: double)</a></li>
<li><a href="#addlucrolucro-double">addLucro(lucro: double)</a></li>
<li><a href="#getsetorgarçom-garçom">getSetor(garçom: Garçom)</a></li>
<li><a href="#addgorjetafuncionario-funcionario-valor-double">addGorjeta(funcionario: Funcionario, valor: double)</a></li>
</ul>
</li>
<li><a href="#pedido">Pedido</a><ul>
<li><a href="#setestadoitensitens-list-item-estado-estado">setEstadoItens(itens: List Item, estado: Estado)</a></li>
<li><a href="#additemspedido-pedido">addItems(pedido: Pedido)</a></li>
<li><a href="#additemitem-item">addItem(item: Item)</a></li>
<li><a href="#getpreço">getPreço</a></li>
</ul>
</li>
<li><a href="#itempedido">ItemPedido</a></li>
<li><a href="#item">Item</a></li>
<li><a href="#receita">Receita</a></li>
<li><a href="#ingrediente">Ingrediente</a></li>
<li><a href="#mesa">Mesa</a><ul>
<li><a href="#desocupa">desocupa</a></li>
<li><a href="#isdisponível">isDisponível</a></li>
</ul>
</li>
</ul>
</li>
</ul>


##UIAction

Cada ação é associada a um *UIAction*, que possui um método abstrato *execute* a ser chamado, sem parâmetros. Todos os dados necessários para efetuar a ação (mesa, item pedido, etc.) devem ser obtidos na chamada do *execute*.

Nenhuma mudança nos dados deve ser feita em *UIAction* ou qualquer de suas subclasses, mas sim apenas chamar um método de *RestaurantOperationService*, que lida com a mudança dos dados. O máximo que pode ocorrer é uma ação em dois passos, na seguinte estrutura:

1. Pede informações de *RestaurantOperationService*;
2. Espera input do usuário sobre essas informações;
3. Manda resposta para *RestaurantOperationService*.

### LoginAction

É a única ação disponível ao abrir o programa, e define quais outas ações estarão disponíveis.

1. Pede ao usuário seu login;
2. Manda as informações de login ao *RestaurantOperationService*, que pede ao banco de dados o funcionário com aquelas informações;
3. Se o funcionário existe, muda o funcionário ativo de *RestaurantInterface* e chama o método adequado para o tipo de funcionário que fez login;
3. Se o funcionário não existe, informa o erro para o usuário e encerra a ação.

### LogoutAction

Provavelmente a ação mais simples, ela informa ao *RestaurantOperationService*, que por sua vez informa a *RestaurantInterface*, que o usuário deseja fazer logout, e o estado da interface é resetado a como é quando o programa começa.

### FinalizaTurnoAction

Utilizada pelo gerente, esta ação verifica se todas as mesas estão fechadas, ou seja, com as contas fechadas. Se sim, faz com que o turno ativo vá para a lista de turnos e o mesmo seja retirado do turno ativo.

O fluxo de dados é:

1. A verificação das mesas das mesas fechadas é confirmada ou não.
2. Se a verificação das mesas é confirmada, é solicitada à base de dados que o atual turno vá para a lista de turnos e o turno ativo receba NULL.


### FazerRelatorioAction

Utilizada apenas pelo gerente, esta ação irá fazer o relatório dos lucros e das despesas de um determinado turno.
Inicialmente, o gerente escolhe o turno que deseja.
Em seguida, ele pega o total de despesas e lucro do turno escolhido e cria um relatório com os valores obtidos.

### SentaClienteAction

Essa *UIAction* se refere à ação de sentar um cliente novo em uma mesa disponível, e deve ser feita pelo atendente. 

O fluxo de dados é bem simples, quando existe um turno ativo:

* Primeiro, o atendente informa quantas pessoas querem ser sentadas, e faz uma chamada para *RestaurantOperationService* para obter as mesas disponíveis que comportam essa quantidade de pessoas.
* Caso não haja um turno ativo no momento, é jogada uma exceção pelo *RestaurantOperationService*, que deve ser tratada por essa classe.
* Depois, as mesas válidas são mostradas, para uma ser escolhida.
* Quando a mesa desejada é escolhida, é informado ao *RestaurantOperationService* que um novo cliente foi sentado na mesa, e eu estado é mudado para ocupada.

### ReservarMesaAction

*ReservarMesaAction* é chamada quando um atendente deseja reservar uma mesa para determinado horário.

O atendente busca todas as mesas que não possuem reserva, que são mostradas e aquela que for escolhida será marcada como "reservada".

### CancelaReservaAction

É chamada pelo atendente para cancelar reservas que já haviam sido feitas.

Todas as mesas são mostradas no sistema e, a partir destas, o atendente cancela a reserva escolhida.

### VisualizaMesasSujasAction

É utilizada pelo auxiliar de limpeza para verificar quais mesas precisam ser limpas.

Recebe todas as mesas sujas e as mostra no sistema.

### LiberaMesaAction

O atendente informa ao sistema as mesas liberadas para uso. 

Inicialmente o atendente pede a lista de mesas sujas e verifica as que ele já limpou.

Após esta verificação, a mesa é marcada como limpa.

### IniciaTurnoAction

Ação efetuada pelo gerente quando deseja iniciar um turno. Ao chamar a ação, ele verifica que todas as mesas estão liberadas (prontas para a recepção de um cliente, ou seja, limpas e vazias), caso contrário não é possível continuar a ação, e então seleciona os garçons para determinados setores.

O fluxo de dados é informado abaixo:

1. A verificação das mesas das mesas liberadas é confirmada ou não.
2. Se a verificação das mesas é confirmada, é pega a lista de garçons e a lista de setores.
3. São definidos pelo gerente, através de um mapa, os garçons de cada setor.

### IniciarPreparacaoAction

Essa ação é usada pelo cozinheiro quando ele quer começar a preparação de novos itens/pedidos. Ele vê no sistema quais são os itens que ainda não foram atendidos, e então decide qual começar. Ele então informa ao sistema qual o pedido iniciado, além de quais itens ele iniciou (não é necessário iniciar a preparação de todos os itens de um pedido de uma vez).

O fluxo de dados é informado abaixo:
1. A lista de pedidos que ainda não foram preparados é pedida e mostrada pelo sistema.
2. O usuário escolhe o pedido desejado, e, dentro desse pedido, quais itens ainda não preparados ele deseja preparar.
3. O sistema é informado sobre essas mudanças, e o pedido e itens são colocados em preparação.

*Sugestão de implementação:* para a escolha dos itens, primeiro mostre todos os pedidos pendentes para o usuário. Depois, quando ele escolher qual pedido vai preparar, mostre quais os itens desse pedido que estão pendentes. Assim, fica fácil criar os parâmetros necessários para a chamada de método de *RestaurantOperationService*. 

## AtendeMesaAction

É usada por um garçom para registrar os pedidos de uma mesa. Ao chamar a ação, ele vê uma lista de todas as mesas que podem ser atendidas por ele, sejam elas as que não têm nenhum pedido e estão em seu setor, seja as que já têm um pedido feito por ele. Com essas mesas visíveis, ele escolhe a mesa que deseja atender e recebe um cardápio para marcar quais itens adicionar ao pedido da mesa. Quando ele confirma os itens adicionados, o pedido da mesa é atualizado.

1. Pede ao *RestaurantOperationService* a lista de mesas disponíveis para atendimento para o garçom, definido pelo usuário de *RestaurantInterface*.
2. Ele escolhe a mesa dentre as da lista.
3. Pede ao *RestaurantOperationService* o cardápio do restaurante.
4. Escolhe os itens dentre os da lista.
5. Cria um novo pedido com os itens especificados, chamando o método *criaPedido* de *
6. 
7. Service* para informar o novo pedido da mesa. (Caso a mesa já possua um pedido, isso será lidado por *criaPedido*)

## FechaMesaAction

É usada pelo garçom responsável por uma mesa para indicar que o cliente fechou a conta. O garçom é mostrado uma lista de mesas que ele está atendendo, e ele escolhe qual mesa fechou a conta. Com isso feito, é mostrado a ele o preço total do pedido, com opção de aceitar ou rejeitar o pagamento. Ele indica se o pagamento foi feito ou não, e então a ação faz as mudanças necessárias no programa.

1. Pede ao *RestaurantOperationService* a lista de mesas abertas para o garçom.
2. Ele escolhe a mesa dentre as da lista.
3. O *RestaurantOperationService* informa qual o preço final da conta.
4. Ele informa se o cliente pagou ou não a conta.
5. Chama o método *fechaMesa* com a mesa e se a conta foi paga ou não. Com isso, a mesa é marcada como desocupada e o pedido é removido da mesa.

## RestaurantOperationService

Gerencia a comunicação entre as ações, que lidam com a obtenção e display de informações ao usuário, e os dados propriamente ditos.

### verificaMesasFechada

Retorna um booleano correspondente à pergunta: Todas as mesas estão fechadas, ou seja, não há nenhuma mesa com a conta aberta?

### retirarTurnoAtivo

Solicita à base de dados que retire o turno atual do turno ativo e o adicione à lista de turnos.

### verificaMesasLiberadas: 

Solicita à base de dados um booleano para a pergunta: Todas as mesas estão liberadas, ou seja, limpas e vazias para acomodar um cliente? 

### getGarcons:

Solicita ao Turno a relação dos garçons daquele turno. Retorna uma lista de garçons.

### getSetores:

Solicita à base de dados a relação dos setores. Retorna uma lista de setores.

### setGarconsSetor(MapSetorGarcons : Map<List<Garcom>,Setor>):

Define os garçons para cada setor a partir de um dado mapa de garçons e setor.

### getMesasPara(pessoas: int)

* Vê com o banco de dados se existe um turno ativo. Se não, joga uma exceção, pois não faz sentido pedir uma mesa para sentar clientes quando o restaurante não está aberto;
* Pega as mesas existentes do banco de dados;
* Filtra as mesas para apenas as que suportam *pessoas* ou mais e que estão disponíveis agora;
* Retorna essa lista de mesas filtradas.

### sentaCliente(mesa: Mesa)

* Pede o turno ativo ao banco de dados;
* Avisa ao turno ativo que *mesa* foi ocupada, e muda o estado de mesa para ocupada.

### getMesasSemReserva

* Obtém uma lista de todas as mesas existentes através banco de dados;
* Retorna todas as mesas que não possuem uma reserva;

### reservaMesa(mesa: Mesa, horario: Time)

* Avisa à *mesa* que ela foi reservada naquele horário.

### cancelaReserva(mesa: Mesa)

* Informa à *mesa* que seu horário de reserva é nulo.

### getMesasSujas

* Pede a lista de mesas ao banco de dados;
* Retorna a lista de mesas filtrada para isSuja() == true

### liberaMesa(mesa: Mesa)

* Informa à *mesa* que ela foi limpa.

### getMesasParaAtendimento(funcionario: Garçom)

1. Pede ao banco de dados o turno ativo.
2. Do turno, pede qual o setor alocado a *funcionario*
3. Do banco de dados, pega a lista de todas as mesas daquele setor.
4. Filtra as mesas para as que satisfazem as seguintes restrições:
	* estão ocupadas
	* não possuem pedido ou possuem um pedido cujo garçom é *funcionario*
5. Retorna essa lista filtrada

### getCardapio

Pede ao banco de dados o cardápio do restaurante

### criaPedido(mesa: Mesa, pedido: Pedido)

1. Pega o turno ativo do banco de dados;
2. Para cada item que for do tipo bebida de *pedido*, adiciona o custo ao turno atual;
3. Verifica se a mesa possui um pedido já feito.
	1. Se a mesa não possui pedido, *pedido* é dado como seu pedido, e o pedido é inserido na lista de pedidos do turno atual.
	2. Se a mesa possui pedido, chamamos *addItems* para o pedido da mesa, que cuida da adição dos itens novos. Também mudamos o estado do pedido da mesa para pendente, já que existem novos itens que precisam ser preparados.

###getMesasAbertas(garçom: Garçom)

1. Pega o turno ativo do banco de dados;
2. Pega o setor que foi alocado para *garçom* nesse turno;
3. Pega a lista de todas as mesas desse setor do banco de dados;
4. Filtra essa lista de mesas para aquelas que estão ocupadas e com um pedido feito por *garçom*.
5. Retorna essa lista de mesas

### getPreçoPedido(mesa: Mesa)

1. Pega o pedido da mesa
2. Retorna o preço do pedido

### fechaMesa(mesa: Mesa, aceita: boolean)

1. Pega o preço total do pedido da mesa.
2. Verifica o valor de aceita:
	1. Se verdadeiro, soma o preço no lucro do turno atual, além de colocar 7% de preço como a gorjeta do garçom responsável pelo pedido da mesa. Também marca que o valor de gorjeta de limpeza da mesa é 3% do preço.
	2. Se for falso, soma o preço aos custos do turno.
3. Marca a mesa como desocupada, removendo o seu pedido e marcando-a como precisando de limpeza.

## Database

A base de dados guarda todas as informações históricas do restaurante, além da despensa atual e outras informações estáveis do restaurante, como lista de funcionários, cardápio e mesas.

### armazenaTurnoAtivo( turno: Turno)

Retira o turno atual do turno ativo e o adiciona à lista de turnos.

### getSetores

Retorna uma lista com os setores.

### hasTurnoAtivo

Retorna um booleano indicando se existe um turno ativo no restaurante no momento.

### getTurnoAtivo

Retorna, em condições normais, o turno atual do restaurante. Se não houver um turno ativo, ou seja, o restaurante está fechado, joga uma exceção, *SemTurnoAtivoException*.

Esse comportamento serve para impedir ações que precisam de turno de acontecerem quando não há um turno ativo. 

### getTurno

Retorna uma lista de todos os turnos do restaurante para que o gerente selecione aquele que quer no momento.

### getFuncionario(ID: String)

Passa por todos os funcionários cadastrados, retornando aquele que possui o ID informado. Se não existe nenhum funcionário com esse ID, retorna null.

### getMesas(setor: Setor)

Retorna todas as mesas existentes no setor especificado.

### getMesas

Retorna todas as mesas existentes no restaurante.

### atualizaDespensa(itensARemover: List Ingrediente)

Remove da despensa todos os ingredientes de *itensARemover*. Não é possível que os itens em *itensARemover* não estejam na despensa, pois essa verificação é feita antes de permitir a inclusão destes itens em algum pedido.

### getCardapio

Retorna o cardápio do restaurante

## Turno

Guarda todas as informações relacionadas a um turno específico, como os pedidos de cada mesa, o dinheiro obtido por cada funcionário, a distribuição de garçom por mesa, etc. 

Mantém uma lista de todos os pedidos feitos no turno, sem deletar pedidos que foram completos.

### getGarcons

Retorna uma lista com os garçons daquele turno.

### setGarconsSetor

Solicita ao turno que defina os garçons para cada setor a partir de um dado mapa de garçons e setor.

### addPedido(pedido: Pedido)

Adiciona o pedido para a lista de pedidos do turno. É usado quando uma mesa faz um pedido novo.

###getPedidos

Retorna a lista de pedidos do turno. Para obter uma lista de pedidos pendentes, é necessário filtrar a resposta desse método, já que todos os pedidos históricos também estão presentes.

###addCusto(custo: double)

Adiciona o valor ao custo corrente do turno.

###getSomaCustos

Retorna o valor total do(a) lucro/despesa de um determinado turno já escolhido.

###addLucro(lucro: double)

Adiciona o valor ao lucro atual do turno.

###getSomaLucros

Retorna o valor total do lucro de um determinado turno já escolhido.

### getSetor(garçom: Garçom)

Retorna o setor alocado para o garçom especificado

### addGorjeta(funcionario: Funcionario, valor: double)

Adiciona o valor especificado para a entrada do funcionário no mapa de gorjetas. Na versão atual do programa, apenas garçons e auxiliares de cozinha podem receber gorjetas, mas o método é genérico para permitir mudanças futuras.

## Pedido

Guarda as informações sobre o pedido de uma mesa. Uma mesa não pode conter mais de um pedido. Ele contém um mapa relacionando seus itens com o estado de preparação de cada um. Além disso, ele possui um atributo indicando seu estado de preparação e o garçom responsável pelo pedido.

###setEstadoItens(itens: List Item, estado: Estado)

Modifica todos os estados de *itens* para *estado*. É possível que exista mais de um ItemPedido com algum dos itens da lista, mas qualquer um que seja modificado é válido, já que são todos iguais. Assim, pedidos com itens repetidos não são afetados por isso.

### addItems(pedido: Pedido)

Recebe um pedido e adiciona os itens desse pedido à lista de itens. Cada item de *pedido* deve ser adicionado através do método *adicionaItem*, que lida com a ordenação adequada dos items.

### addItem(item: Item)

Cria um novo *ItemPedido*, com o item especificado e estado PENDENTE. Depois, analisa o *item* o coloca na ordem adequada na lista, seguindo as especificações do trabalho.

### getPreço

Retorna a soma do preço de todos os itens do pedido.

## ItemPedido

Uma classe para facilitar o rastreamento da preparação de itens em pedidos. Guarda um item e seu estado de preparação, com getters e setters pra cada um dos atributos.

## Item

Guarda todas as informações relativas a um item do cardápio. Elas são: categoria, preço, custo e tempo de preparação e a receita do item.

## Receita

## Ingrediente

## Mesa

Guarda todas as informações relacionadas a uma mesa do restaurante. Além das informações permanentes, como capacidade e setor, também armazena informações de estado da mesa, como se está ocupada, limpa ou se possui uma reserva feita.

### desocupa

Passa o valor de ocupada para falso, e o valor de limpa para falso. Além disso, remove o pedido da mesa.

### isDisponível

1. Caso possua uma reserva feita para um horário que viola o intervalo de tolerância definido, a reserva é cancelada.
2. Retorna verdadeiro se ela está desocupada, limpa e não possui uma reserva feita. 

> Written with [StackEdit](https://stackedit.io/).
