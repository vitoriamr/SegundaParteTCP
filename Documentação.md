

**Table of Contents**

- [UIAction](#UIAction)
	- [LoginAction](#loginaction)
	- [LogoutAction](#logoutaction)
	- [SentaClienteAction](#sentaclienteaction)
- [RestaurantOperationService](#restaurantoperationservice)
	- [getMesasPara(pessoas: int)](#getmesasparapessoas-int)
	- [sentaCliente(mesa: Mesa)](#sentaclientemesa-mesa)
- [Database](#database)
	- [getTurnoAtual](#getturnoatual)
	- [getFuncionario(ID: String)](#getfuncionarioid-string)
- [Turno](#turno)
	- [sentaCliente(mesa: Mesa)](#sentaclientemesa-mesa)


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

### SentaClienteAction

Essa *UIAction* se refere à ação de sentar um cliente novo em uma mesa disponível, e deve ser feita pelo atendente. 

O fluxo de dados é bem simples, quando existe um turno ativo:

* Primeiro, o atendente informa quantas pessoas querem ser sentadas, e faz uma chamada para *RestaurantOperationService* para obter as mesas disponíveis no turno atual que comportam essa quantidade de pessoas.
* Depois, as mesas válidas são mostradas, para uma ser escolhida.
* Quando a mesa desejada é escolhida, é informado ao *RestaurantOperationService* que um novo cliente foi sentado na mesa, e ela é removida da lista de mesas disponíveis.

### ReservarMesaAction

*ReservarMesaAction* é chamada quando um atendente deseja reservar uma mesa para determinado horário.

O atendente busca todas as mesas disponíveis para este horário, que são mostradas e aquela que for escolhida será marcada como "reservada".

### CancelaReservaAction

É chamada pelo atendente para cancelar reservas que já haviam sido feitas.

As reservas existentes são mostradas no sistema e, a partir destas, o atendente cancela a reserva escolhida.

### VisualizaMesasSujasAction

É utilizada pelo auxiliar de limpeza para verificar quais mesas precisam ser limpas.

Recebe todas as mesas sujas de um determinado turno e as mostra no sistema.

### LiberaMesaAction

O atendente informa ao sistema as mesas liberadas para uso. 

Inicialmente o atendente pede a lista de mesas sujas, em seu turno, e verifica as que ele já limpou.

Após esta verificação, a mesa limpa é marcada como disponível.


## RestaurantOperationService

Gerencia a comunicação entre as ações, que lidam com a obtenção e display de informações ao usuário, e os dados propriamente ditos.

### getMesasPara(pessoas: int)

* Pede o turno ativo ao banco de dados;
* Do turno ativo, pega quais são as mesas disponíveis
* Filtra as mesas para apenas as que suportam *pessoas* ou mais
* Retorna essa lista de mesas filtradas.

### sentaCliente(mesa: Mesa)

* Pede o turno ativo ao banco de dados;
* Avisa ao turno ativo que *mesa* foi ocupada, e deve ser removida da lista de mesas disponíveis.

### getMesasDisponiveis(horario: Time)

* Pede o turno ativo ao banco de dados;
* Obtém uma lista de todas as mesas existentes através do turno;
* Obtém todas as reservas deste turno;
* Retorna todas as mesas que não possuem uma reserva equivalente em tal horário;

### reservaMesa(mesa: Mesa, horario: Time)

* Pede o turno ativo ao banco de dados;
* Avisa ao turno ativo que *mesa* foi reservada naquele horário.

### getMesasSujas

* Pede o turno ativo ao banco de dados;
* Obtém uma lista de todas as mesas sujas do restaurante, no turno.

### liberaMesa(mesa: Mesa)

* Pede o turno ativo ao banco de dados;
* Informa ao turno que *mesa* foi liberada para uso.

### getReservas

* Pede o turno ativo ao banco de dados;
* Obtém todas as reservas feitas naquele turno.

### cancelaReserva(reserva: Reserva)

* Pede o turno ativo ao banco de dados;
* Informa ao turno que a reserva foi cancelada.

## Database

A base de dados guarda todas as informações históricas do restaurante, além da despensa atual e outras informações estáveis do restaurante, como lista de funcionários, cardápio e configuração das mesas.

### getTurnoAtual

Retorna, em condições normais, o turno atual do restaurante. Se não houver um turno ativo, ou seja, o restaurante está fechado, joga uma exceção, *SemTurnoAtivoException*.

Esse comportamento serve para impedir a maior parte das ações, excetuando algumas do gerente, de acontecerem quando não há um turno ativo. 

### getFuncionario(ID: String)

Passa por todos os funcionários cadastrados, retornando aquele que possui o ID informado. Se não existe nenhum funcionário com esse ID, retorna null.

### getMesas

Retorna todas as mesas existentes no restaurante.

## Turno

Guarda todas as informações relacionadas a um turno específico, como a situação de cada mesa, o dinheiro obtido por cada funcionário, a distribuição de garçom por mesa, etc. 

Como cada instância de *Mesa* guarda apenas as informações permanentes dela (a capacidade), o estado atual dela precisa ser armazenado de alguma forma em cada *Turno*. Para isso, a solução pensada foi representar o estado dela pela presença ou não da mesa em uma lista.

Existem 3 estados principais em que uma mesa pode estar, com um deles sendo quebrado em dois sub-estados. São eles:

* Livre
* Suja 
* Ocupada
	* Pedido a fazer
	* Pedido feito

Para isso, são feitas duas listas e um mapa em cada *Turno*, e cada *Mesa* deve estar em exatamente uma dessas 3 coleções. As duas listas ,*mesasLivres* e *mesasALimpar*, representam as mesas livres e sujas, respectivamente. 

Para representar as mesas ocupadas, é usado um mapa, com a mesa sendo a chave e o pedido da mesa sendo o valor. Para as mesas que ainda não fizeram o pedido, o valor ligado a ela será nulo, e assim sua identificação será fácil de ser feita. 

### sentaCliente(mesa:Mesa)

1. Se a mesa não está na lista de mesas disponíveis, joga uma *AçãoInválidaException*, com a mensagem de que a mesa não está disponível.
2. Remove a mesa da lista de disponíveis, e a adiciona ao mapa de mesas ocupadas, com um pedido nulo (null).

### getReservas

Retorna todas as reservas do turno.

### reservaMesa(mesa: Mesa, horario: Time)

Cria uma nova instância de *Reserva* com a mesa e horário especificados e adiciona à lista de reservas.  

### cancelaReserva(reserva: Reserva)

Remove reserva da lista de reservas.

### getMesasSujas

Retorna a lista de todas as mesas sujas do restaurante.

###liberaMesa(mesa: Mesa)

Remove da lista de mesas sujas e acrescenta nas mesas disponíveis para uso.