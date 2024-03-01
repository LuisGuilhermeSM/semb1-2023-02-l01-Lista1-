CC = arm-none-eabi-gcc 
RM = rm -rf
# parametro -f ou --force: Ignora mensagens de erro e não solicita confirmação antes de remover os arquivos
# parametro -r permite remover diretórios

CFLAGS = -g -mcpu=cortex-m4 -mthumb -O0 -Wall


#Arquivos a serem compilados
SRCS = startup.c main.c

OBJSDIR = build
#Para referênciar variável usa $(variável)

DEPDIR = .deps

DEPFLAGS = -MMD -MP -MF $(DEPDIR)/$*.d

#$* variável que representa o nome do arquivo sem extensão usado para criar o alvo (target) no caso mostrará main que será juntada ao .d

DEPS = $(patsubst %, $(DEPDIR)/%.d, $(basename $(SRCS)) )
$(shell mkdir -p $(dir $(DEPS) > /dev/null))

# $(basename names) função que retorna apenas o nome do arquivo sem sufixo ou prefixo
# $(patsubst pattern, replacement, text) função que procura palavras separadas por espaço que combinam com um padrão e substitui o padrão com um texto

OBJS = $(patsubst %, $(OBJSDIR)/%.o, $(basename $(SRCS)))

#Diretório onde serão salvos os arquivos

# craindo o diretório
$(shell mkdir -p $(dir $(OBJS)) > /dev/null)

#$(shell ) é uma construção do makefile que permite executar comandos do sistema operacional a partir do Make, no caso o comando mkdir

all: $(OBJS)

$(OBJSDIR)/%.o: %.c $(DEPDIR)/%.d
	$(CC) -c $(CFLAGS) $(DEPFLAGS) $< -o $@


#$(OBJSDIR)/%.o: %.c
#	$(CC) -c $(CFLAGS) $< -o $@

$(DEPS):
#build/startup.o: startup.c
#build/main.o: main.c config.h
-include $(DEPS)
# inclui o conteudo do arquivo DEPS que seria o build/startup.o: startup.c e dps o build/main.o: main.c config.h



.PHONY: clean
clean:
	$(RM) $(OBJSDIR) $(DEPDIR) *.elf

#clean:
#	rm -f *.o


#all: startup.o main.o
# Se não usar o make all o make só realizará a primeira regra (target)
# Com o all o make checa os arquivos startup.o e main.o e se não tiverem atualizados os atualizam


#main.o: main.c
#	$(CC) -c $(CFLAGS) $< -o $@  
# Variaveis automáticas usadas para tornar o código mais genérico
# $< variavél que representa o primeiro elemento da lista de pre-requisitos
# $@ variável que representa o alvo da regra
# Compila o main.c (S<) no main.o ($@)

#startup.o: startup.c
#	$(CC) -c $(CFLAGS) $< -o $@

# Regra para excluir todos os aquivos .o


# arm-none-eabi-gcc -M main.c se usado no compilador mostra as dependências do aruivo main.c
# arm-none-eabi-gcc -MM main.c mostro os aquivos de dependencia apenas do usuário (criados pelo usuário provavelmente) e não mostra os aquivos de dependencia do 
#sistema que aparecieriam por exemplo com o #include <stdlib.h>
# arm-none-eabi-gcc -MM -MF .deps/main.d main.c
# A opção -MF nome_do_arquivo_que_será_salvo possibilita salvar as depêndencias de main.c
# main.d será o arquivo de dependência gerado e .deps o seu diretório
# .deps é um diretório oculto e que precisa ser criado antes

# arm-none-eabi-gcc -c -g -mcpu=cortex-m4 -mthumb -Wall -O0 -MMD -MF .deps/main.d main.c -o build/main.o
# o parametro -MMD é capaz de gerar a lista de dependências como subproduto do processo de compilação

