# Questionário Sistemas Embarcados I

## 1. Explique brevemente o que é compilação cruzada (***cross-compiling***) e para que ela serve.
Cross compilation é o processo de compilação de um código por meio de um sistema diferente daquele que executará o programa. Ela serve para se criar programas destinados a diferentes dispositivos que não possuem a capacidade de gerar softwares. Desse modo, é por esse processo que se utiliza computadores para criação de códigos de muitos sistemas embarcados, permitindo a compilação de tais programas para as arquiteturas específicas de cada dispositivo.

## 2. O que é um código de inicialização ou ***startup*** e qual sua finalidade?
Um código de inicialização ou startup é o programa executado logo ao se iniciar um sistema embarcado que tem como objetivo preparar o ambiente de execução do dispositivo para que ele possa funcionar corretamente e como desejado. Dentre algumas funções desse código estão a inicialização do stack, onde serão armazenados os dados do programa, dos vetores de interrupção, da área de armazenamento da memória Ram e todas as outras configurações necessárias para que a função main do código possa ser chamada e o programa iniciar sua execução.

## 3. Sobre o utilitário **make** e o arquivo **Makefile responda**:

#### (a) Explique com suas palavras o que é e para que serve o **Makefile**.
Makefile é um arquivo desenvolvido para implementação padrão no Linux e macOS, que contém instruções que realizarão a compilação automática de um outro programa e suas bibliotecas. Desse modo, o makefile serve para tornar o processo de compilação mais rápido e usual.

#### (b) Descreva brevemente o processo realizado pelo utilitário **make** para compilar um programa.
O processo se inicia com o make fazendo a leitura do código makefile e checando se os targets do programa estão desatualizados quanto aos seus pre-requisitos. Os targets são considerados desatuailizados caso não existam ou tenham data de criação anterior a última data de alteração de qualquer um de seus pre-requisitos. Assim, caso o target esteja desatualizado o make executa a regra vinculada a esse target, a qual permite a construção ou atualização desse target usando como base seus arquivos de pre-requisitos.

#### (c) Qual é a sintaxe utilizada para criar um novo **target**?
A sintaxe para criar um novo target é:
      targets: prerequisitos
            recipe

      Exemplo:
      main.o: main.c
	        arm-none-eabi-gcc -c -g -mcpu=cortex-m4 -mthumb -O0 -Wall main.c -o main.o

Onde main.o é o target (arquivo que será gerado), main.c o prerequisito (arquivo necessário para gerar o target main.o) e a linha de baixo a regra que no caso gera o arquivo de compilação main.o.


#### (d) Como são definidas as dependências de um **target**, para que elas são utilizadas?
As dependências são definidas pela sintaxe, targets: dependências, ou seja, na declaração do target. Elas são utilizadas para apresentar quais arquivos serão necessários para se gerar o target. Além disso, por meio da checagem da data da última alteração dos arquivos de dependência o make observa se o arquivo target gerado é mais antigo que tal modificação e caso seja o reconstrói, ou caso ele não existisse, o arquivo target é gerado da mesma forma seguindo os comandos associados a ele no Makefile.



#### (e) O que são as regras do **Makefile**, qual a diferença entre regras implícitas e explícitas?
Regras são as instruções escritas no Makefile para a criação de um target. Regras implícitas são aquelas predefinidas que geralmente possuem uma forma genérica usada para criação de vários targets similares. Um exemplo de regra implícita seria:

	%.o: %.c
	    arm-none-eabi-gcc -c -mcpu=cortex-m4 -mthumb $< -o $@
Na regra acima qualquer aquivo .c será compilado para um aquivo .o, sendo genérica para criação de diferentes targets, e portanto, uma regra implícita. Já as regras explicitas, são aquelas usadas para criar um target específico como por exemplo:

    main.o: main.c
		arm-none-eabi-gcc -c -mcpu=cortex-m4 -mthumb main.c -o main.o


## 4. Sobre a arquitetura **ARM Cortex-M** responda:

### (a) Explique o conjunto de instruções ***Thumb*** e suas principais vantagens na arquitetura ARM. Como o conjunto de instruções ***Thumb*** opera em conjunto com o conjunto de instruções ARM?
O conjunto de instruções Thumb utiliza 16 bits para passagem de suas informações sendo mais compacto que o conjunto de 32 bits denominado ARM. As vantagens do uso do conjunto Thumb estão relacionadas ao menor uso de memória, consumo de energia, mas ele costuma apresentar velocidade inferior ao conjunto ARM. O processador da ARM é capaz de executar ambos esses conjuntos de instruções, de modo que, é possível se ter partes do código rodando em modo Thumb quanto em modo Arm possibilitando uma maior otimização do programa.

### (b) Explique as diferenças entre as arquiteturas ***ARM Load/Store*** e ***Register/Register***.
Na arquitetura Arm load/store todo dado só pode ser acessado ou gravado na memória por meio das intruções load ou store. Portanto, ao se realizar uma operação em um registro da placa que precise de um dado da memória o registro precisará da instrução load para acessar tal dado e da instrução de store para armazenar o resultada da operação. Já a arquitetura Register/Register memory possibilita que algumas intruções realizem operações diretamente na memoria sem a necessidade de copiar o dado desta para um registrador e então realizar a operação.

### (c) Os processadores **ARM Cortex-M** oferecem diversos recursos que podem ser explorados por sistemas baseados em **RTOS** (***Real Time Operating Systems***). Por exemplo, a separação da execução do código em níveis de acesso e diferentes modos de operação. Explique detalhadamente como funciona os níveis de acesso de execução de código e os modos de operação nos processadores **ARM Cortex-M**.
Os níveis de acesso de execução de código são divididos em dois, o de acesso privilegiado e o não privilegiado. No primeiro, o código possui total acesso aos recursos do processador e de seus registros restritos. No segundo, o código terá acesso limitado a apenas registros e instruções não restritas. Quanto a operação o processador pode funcionar em dois estados: estado de debug, usado para depuração do código e estado thumb. Neste último, o código do processador é executado normalmente em dois possíveis modos de operação, handler mode e thread mode. O primeiro ocorre quando o processador está tratando uma exceção ou interrupção e ele sempre é executado com nível de acesso privilegiado. Já no último, o processador está executando o código normal da aplicação, de modo que, é configurável se este estará em nível privilegiado ou não de acesso.

### (d) Explique como os processadores ARM tratam as exceções e as interrupções. Quais são os diferentes tipos de exceção e como elas são priorizadas? Descreva a estratégia de **group priority** e **sub-priority** presente nesse processo.
Quando uma interrupção ou exceção ocorre o contexto atual do programa é salvo no stack pointer e o processador confere na tabela de vetores de interrupção qual o endereço do código de tratamento da interrupção ocorrida e executa tal código, durante este processo o processador se encontrará em handler mode, e após o termino do tratamento o processador recupera o seu contexto anterior salvo no stack e continuar a executar o código normal em thumb mode. As interrupções e exceções são separadas em diferentes níveis de prioridade, os quais quanto menor o número mais prioritários serão, de modo que, são executadas as interrupções com prioridade maior inicialmente e que caso ocorra uma interrupção mais prioritária durante o tratamento de outra, este última terá sua execução interrompida e contexto salvo para se processar a com maior preferência.  Esse conceito de prioridade pode ser dividido em dois grupos de priority e Sub-priority, os quais são uma divisão nos bits usados para se atribuir o nível de prioridade de uma interrupção, em que geralmente os bits mais significativos fazem parte do grupo priority e os menos significativos do grupo sub-priority. Desse modo, é possível agrupar interrupções, devido ao fato delas teram um possível sentido lógico entre si, em um grupo de priority e diferencia-las pelo grupo sub-priority. Quanto aos exceções elas podem ser divididas em exceções síncronas que ocorrem pela execução de instruções inválidas pelo processador, exceções assíncronas as quais ocorrem por eventos externos ao processador e exceções de software as quais são geradas pelo código do programa e são usadas para sinalizar eventos específicos.

### (e) Qual a diferença entre os registradores **CPSR** (***Current Program Status Register***) e **SPSR** (***Saved Program Status Register***)?
O CPSR armazena o estado atual do processador, como o modo de operação e status das flags, enquanto o programa está em modo normal de execução. Já o SPSR armazena o estado do CPSR antes da ocorrência de uma interrupção ou exceção e é acessível para escrita e leitura pelo hardware do processador apenas durante o tratamento da interrupção. Concluído o tratamento o SPSR é copiado para o CPSR o restaurando e possibilitando a continuação da execução do programa em modo normal.

### (f) Qual a finalidade do **LR** (***Link Register***)?
O Link Register é um registrador que tem como finalidade armazenar o endereço de retorno de uma sub-rotina. Então, por exemplo, caso ocorra uma interrupção o Link Register armazenará o endereço de retorno, de modo que, ao se terminar o tratamento da interrupção o programa volte a executar deste ponto do endereço de retorno.



### (g) Qual o propósito do Program Status Register (PSR) nos processadores ARM?
O PSR é um registrador que armazena informações sobre o estado atual de execução do programa. Entre essas informações estão algumas flags(Exemplo: flag de carry, de zero, overflow e de negativo), o número de qual interrupção está sendo tratada (exception number), e o modo de operação atual (como modo privilegiado ou não e  modo de interrupção) entre outras. Desse modo, o propósito deste registrador é para conter informações que serão utilizadas por outras intruções e registradores necessárias para execução correta do programa, como operações de comparação com as flags, determinar se o processador está tratando uma execução dentre outras, ou seja, ele é importante para o controle do fluxo do programa.

### (h) O que é a tabela de vetores de interrupção?
A tabela de vetores de interrupção é uma região da memória que armazena os endereços dos códigos de tratamento de interrupções e exceções. Cada valor nesta tabela aponta para o código de tratamento de uma exceção ou interrupção específica, de modo que, toda vez que uma interrupção ocorre é nesta tabela onde será buscado o endereço do código que realizará seu tratamento. Esta tabela é estabelecida logo após o início do processador em uma região fixa da memória.

### (i) Qual a finalidade do NVIC (**Nested Vectored Interrupt Controller**) nos microcontroladores ARM e como ele pode ser utilizado em aplicações de tempo real?
Ele serve para gerenciar e controlar as interrupções e exceções, determinado a habilitação e ordem de execução destas por meio dos níveis de prioridades atribuídas a cada uma. Desse modo, ele pode ser usado em aplicações de tempo real ao controlar por exemplo a ordem das interrupções causadas devido a fatores externos pela sua ordem de prioridade.

### (j) Em modo de execução normal, o Cortex-M pode fazer uma chamada de função usando a instrução **BL**, que muda o **PC** para o endereço de destino e salva o ponto de execução atual no registador **LR**. Ao final da função, é possível recuperar esse contexto usando uma instrução **BX LR**, por exemplo, que atualiza o **PC** para o ponto anterior. No entanto, quando acontece uma interrupção, o **LR** é preenchido com um valor completamente  diferente,  chamado  de  **EXC_RETURN**.  Explique  o  funcionamento  desse  mecanismo  e especifique como o **Cortex-M** consegue fazer o retorno da interrupção. 
Ocorrida a interrupção o Link Register será preenchido com o valor EXC_RETURN, o qual apresentará as condições atuais do processador, como o contexto da pilha, incluindo, o modo de exceção original e o estado dos registradores salvos durante a entrada na interrupção. Assim, quando a interrupção for finalizada é por meio destas informações que o processador volta a atuar no seu contexto anterior de funcionamento.

### (k) Qual  a  diferença  no  salvamento  de  contexto,  durante  a  chegada  de  uma  interrupção,  entre  os processadores Cortex-M3 e Cortex M4F (com ponto flutuante)? Descreva em termos de tempo e também de uso da pilha. Explique também o que é ***lazy stack*** e como ele é configurado. 
O córtex M4F por ter suporte a ponto flutuante salva alguns registros específicos e status dessa funcionalidade no stack quando ocorre uma interrupção ou exceção, junto com o contexto tradicional geral do processador (Alguns outros registradores e o Link Register). Já para o córtex M3 os registros específicos para ponto flutuante não seriam salvos pois tal modelo não possui suporte para float e apenas o contexto tradicional seria armazenado. Desse modo, o salvamento do contexto para o córtex M4f demanda um maior tempo e uso da memória do stack em comparação ao córtex M3. Lazy stack é uma configuração em que o contexto de ponto flutuante só será salvo no stack caso a interrupção a ser tratada usar valores de ponto flutuante. O Lazy Stack é configurado pelo registrador FPCCR sendo que se os bits 31 (nomeado ASPEN) e 30 (nomeado LSPEN) estiverem em 1 isso significa que o lazy stack está ativado.

## Referências

### Básicas

[1] [STM32F411xC Datasheet](https://www.st.com/resource/en/datasheet/stm32f411ce.pdf)

[2] [RM0383 Reference Manual](https://www.st.com/resource/en/reference_manual/rm0383-stm32f411xce-advanced-armbased-32bit-mcus-stmicroelectronics.pdf)

[3] [Using the GNU Compiler Collection (GCC)](https://gcc.gnu.org/onlinedocs/gcc/index.html)

[4] [GNU Make](https://www.gnu.org/software/make/manual/html_node/index.html)

### Vídeos Microsoft Teams

[5] [05 - Introdução à arquitetura de computadores](https://web.microsoftstream.com/embed/channel/f6b3a0de-e6f3-4652-b2d5-f1164032498a?app=microsoftteams&sort=undefined&l=pt-br#)

[6] [06 - Arquitetura Cortex-M Parte 1/2](https://web.microsoftstream.com/embed/channel/f6b3a0de-e6f3-4652-b2d5-f1164032498a?app=microsoftteams&sort=undefined&l=pt-br#)

[7] [07 - Arquitetura Cortex-M Parte 2/2](https://web.microsoftstream.com/embed/channel/f6b3a0de-e6f3-4652-b2d5-f1164032498a?app=microsoftteams&sort=undefined&l=pt-br#)

[8] [08 - Microcontroladores STM32](https://web.microsoftstream.com/embed/channel/f6b3a0de-e6f3-4652-b2d5-f1164032498a?app=microsoftteams&sort=undefined&l=pt-br#)

[9] [10 - Introdução à arquitetura de computadores](https://web.microsoftstream.com/embed/channel/f6b3a0de-e6f3-4652-b2d5-f1164032498a?app=microsoftteams&sort=undefined&l=pt-br#)

### Material Suplementar

[5] [A General Overview of What Happens Before main()](https://embeddedartistry.com/blog/2019/04/08/a-general-overview-of-what-happens-before-main/)
 
[6] [Bare metal embedded lecture-1: Build process](https://youtu.be/qWqlkCLmZoE?si=mn5yDnJYudQ1PpZH)
 
[7] [Bare metal embedded lecture-2: Makefile and analyzing relocatable obj file](https://youtu.be/Bsq6P1B8JqI?si=yuNLPj3JQ-2IT1yo)
 
[8] [Bare metal embedded lecture-3: Writing MCU startup file from scratch](https://youtu.be/2Hm8eEHsgls?si=c27MpZ47ApiMSwHR)
 
[9] [Lecture 15: Booting Process](https://youtu.be/3brOzLJmeek?si=MsHRUEJP8zofjwJQ)