# MANUAL DE COMANDOS - Toletus LiteNet2

**Versão Firmware V2.1.1 R0**

**Versão Manual V1.0.22**

## SUMÁRIO

1. [INTRODUÇÃO](#introducao)
2. [INTERFACE DE COMANDOS](#interface-de-comandos)
   - [ESTRUTURA DE PACOTES](#estrutura-de-pacotes)
   - [LISTA DE COMANDOS](#lista-de-comandos)
   - [DETALHES DE COMANDOS](#detalhes-de-comandos)
     - [Funções](#funcoes)
       - [Libera entrada (0x0001)](#libera-entrada)
       - [Libera saída (0x0002)](#libera-saida)
       - [Reinicia (0x0003)](#reinicia)
       - [Mensagem temporária (0x0004)](#mensagem-temporaria)
       - [Notifica usuário (0x0005)](#notifica-usuario)
       - [Libera bidirecional (0x0006)](#libera-bidirecional)
       - [Reset de periférico (0x0007)](#reset-periferico)
     - [Leitura/escrita de configurações](#leitura-escrita-configuracoes)
       - [Direção (0x0101, 0x0201)](#direcao)
       - [Controle (0x0102, 0x0202)](#controle)
       - [Id de dispositivo (0x0103, 0x0203)](#id-dispositivo)
       - [Configuração de rede (0x0104, 0x0204)](#configuracao-rede)
       - [Número MAC (0x0105, 0x0205)](#numero-mac)
       - [Mensagem da tela inicial linha 1 (0x0106, 0x0206)](#mensagem-tela1)
       - [Mensagem da tela inicial linha 2 (0x0107, 0x0207)](#mensagem-tela2)
       - [Modo de tela inicial (0x0108, 0x0208)](#modo-tela)
       - [Silenciar buzzer (0x0109, 0x0209)](#silenciar-buzzer)
       - [Tempo de liberação (0x010A, 0x020A)](#tempo-liberacao)
       - [Senha do menu (0x010B, 0x020B)](#senha-menu)
       - [Versão de firmware (0x010C, 0x020C)](#versao-firmware)
       - [Número serial (0x010D, 0x020D)](#numero-serial)
       - [Modo de biometria (0x010E, 0x020E)](#modo-biometria)
       - [Modo de controle estendido (0x010F, 0x020F)](#modo-controle-ext)
       - [Contador de giros (0x0110, 0x0210)](#contador-giros)
       - [Flags de hardware (0x0111, 0x0211)](#flags-hardware)
       - [Estados de inicialização (0x0112, 0x0212)](#estados-inicializacao)
     - [Notificações](#notificacoes)
       - [Notificação de id com Rfid (0x0301)](#notificacao-rfid)
       - [Notificação de id com código de barras (0x0302)](#notificacao-barra)
       - [Notificação de id com teclado (0x0303)](#notificacao-teclado)
       - [Notificação de passagem pela catraca (0x0304)](#notificacao-passar)
       - [Notificação de timeout de liberação (0x0305)](#notificacao-timeout)
       - [Notificação de id com biometria (0x0306)](#notificacao-biometria)
       - [Notificação de template de biometria não cadastrado (0x0307)](#notificacao-template)

## 1. Introdução <a name="introducao"></a>

A comunicação é feita através da interface física de rede ethernet através de um cabo
RJ45 através do protocolo TPC/IP em modo IPV4 disponibilizando os seguintes serviços:

| Porta | Funcionalidade                           |
|-------|------------------------------------------|
| 7878  | Interface com a catraca                  |
| 7879  | Interface com o dispositivo de biometria |

Este manual detalha a especificação da interface de comandos com da catraca na porta 7878.
Para comunicação direta com dispositivo de biometria, conecte-se com a porta 7879 e utilize
os comando disponíveis na documentação do fabricante.

## 2 INTERFACE DE COMANDOS <a name="interface-de-comandos"></a>

Todos os comandos são formatados em pacotes de tamanho constante de 20 bytes. Os
seguintes padrões serão seguidos ao longo deste manual, a não ser que explicitamente definido
de outra forma:

- Todos os pacotes serão representados em strings hexadecimais;
- Conteúdo textual (strings) é codificado em ASCII;
- Conteúdo numérico com mais de 8 bits é armazenado em `little-endian`.

### 2.1 ESTRUTURA DE PACOTES <a name="estrutura-de-pacotes"></a>

Todos os pacotes possuem o formato definido abaixo:

| Prefixo | Identificação de comando   | Dados    | Sufixo  |
|---------|----------------------------|----------|---------|
| 1 byte  | 2 bytes                    | 16 bytes | 1 byte  |

Os campos `Prefixo` e `Sufixo` devem sempre ser os números 83 e 195 respectivamente.
Todos os pacotes que violarem esta regra serão ignorados.
O campo `Identificação de comando` consiste em um numero de 16 bits. A enumeração
de todos os comandos é apresentada no capítulo 2.2.
No campo `Dados` está uma quantidade de argumentos que depende da `Identificação de comando`. É frequente que nem todo o espaço em `Dados` seja esquadrinhado em
argumentos. É boa pratica que este espaço remanescente seja preenchido com zeros.

### 2.2 LISTA DE COMANDOS <a name="lista-de-comandos"></a>

| Descrição                                           | Id. de comando   | Direção  | Argumentos                     |                   |                   |
|-----------------------------------------------------|:-----------------|----------|--------------------------------|------------------|------------------|
| libera entrada                                      | 0x0001           | >placa   | texto de 16 caracteres         |                   |                   |
| libera saída                                        | 0x0002           | >placa   | texto de 16 caracteres         |                   |                   |
| reinicia                                            | 0x0003           | >placa   |                                |                   |                   |
| mensagem temporária                                 | 0x0004           | >placa   | texto de 16 caracteres         |                   |                   |
| notifica usuário                                    | 0x0005           | >placa   | Tempo = Numero 2 bytes         | Beep= 1 byte      | Leds = 1byte      |
| libera bidirecional                                 | 0x0006           | >placa   | texto de 16 caracteres         |                   |                   |
| reset de periférico (biometria)                     | 0x0007           | >placa   |                                |                   |                   |
| consulta direção                                    | 0x0101           | >placa>  | numero de 1 byte               |                   |                   |
| consulta controle                                   | 0x0102           | >placa>  | numero de 1 byte               |                   |                   |
| consulta id de dispositivo                          | 0x0103           | >placa>  | numero de 2 bytes              |                   |                   |
| consulta configuração de rede                       | 0x0104           | >placa>  | numero de 1 byte               | ip = 4 bytes      | mascara = 4 bytes |
| consulta numero mac                                 | 0x0105           | >placa>  | mac = 6 bytes                  |                   |                   |
| consulta mensagem da tela inicial linha 1           | 0x0106           | >placa>  | texto de 16 caracteres         |                   |                   |
| consulta mensagem da tela inicial linha 2           | 0x0107           | >placa>  | texto de 16 caracteres         |                   |                   |
| consulta modo de tela inicial                       | 0x0108           | >placa>  | numero de 1 byte               |                   |                   |
| consulta silenciar buzzer                           | 0x0109           | >placa>  | numero de 1 byte               |                   |                   |
| consulta tempo de liberação                         | 0x010a           | >placa>  | numero de 4 bytes              |                   |                   |
| consulta senha do menu                              | 0x010b           | >placa>  | texto de 16 caracteres \*      |                   |                   |
| consulta versão de firmware                         | 0x010c           | >placa>  | 4 bytes                        |                   |                   |
| consulta numero serial                              | 0x010d           | >placa>  | numero de 4 bytes              |                   |                   |
| consulta modo de biometria                          | 0x010e           | >placa>  | numero de 1 byte               |                   |                   |
| consulta modo de controle estendido                 | 0x010f           | >placa>  | numero de 1 byte               | numero de 1 byte  |                   |
| consulta contador de giros                          | 0x0110           | >placa>  | numero de 4 bytes              | numero de 4 bytes |                   |
| consulta flags de hardware                          | 0x0111           | >placa>  | Campo de 16 bytes              |                   |                   |
| consulta estados de inicialização                   | 0x0112           | >placa>  | numero de 2 bytes (extensível) |                   |                   |
| configura direção                                   | 0x0201           | >placa   | numero de 1 byte               |                   |                   |
| configura controle                                  | 0x0202           | >placa   | numero de 1 byte               |                   |                   |
| configura id de dispositivo                         | 0x0203           | >placa   | numero de 2 bytes              |                   |                   |
| configura configuração de rede                      | 0x0204           | >placa   | numero de 1 byte               | ip = 4 bytes      | mascara = 4 bytes |
| configura numero mac                                | 0x0205           | >placa   | mac = 6 bytes                  |                   |                   |
| configura mensagem da tela inicial linha 1          | 0x0206           | >placa   | texto de 16 caracteres         |                   |                   |
| configura mensagem da tela inicial linha 2          | 0x0207           | >placa   | texto de 16 caracteres         |                   |                   |
| configura modo de tela inicial                      | 0x0208           | >placa   | numero de 1 byte               |                   |                   |
| configura silenciar buzzer                          | 0x0209           | >placa   | numero de 1 byte               |                   |                   |
| configura tempo de liberação                        | 0x020a           | >placa   | numero de 4 bytes              |                   |                   |
| configura senha do menu                             | 0x020b           | >placa   | texto de 16 caracteres \*      |                   |                   |
| configura versão de firmware (não utilizado)        | 0x020c           |          | -                              |                   |                   |
| configura numero serial (não utilizado)             | 0x020d           |          | -                              |                   |                   |
| configura modo de biometria                         | 0x020e           | >placa   | numero de 1 byte               |                   |                   |
| configura modo de controle estendido                | 0x020f           | >placa   | numero de 1 byte               | numero de 1 byte  |                   |
| configura zero o contador de giros                  | 0x0210           | >placa   |                                |                   |                   |
| configura flags de hardware                         | 0x0211           | >placa   | Campo de 16 bytes              |                   |                   |
| configura estados de inicialização (não utilizado)  | 0x0212           |          | -                              |                   |                   |
| notificação de id com rfid                          | 0x0301           | placa>   | texto de 16 caracteres \*      |                   |                   |
| notificação de id com código de barras              | 0x0302           | placa>   | texto de 16 caracteres \*      |                   |                   |
| notificação de id com teclado                       | 0x0303           | placa>   | texto de 16 caracteres \*      |                   |                   |
| notificação de passagem pela catraca                | 0x0304           | placa>   | 1 byte                         | 4 bytes           |                   |
| notificação de timeout de liberação                 | 0x0305           | placa>   |                                |                   |                   |
| notificação de id com biometria                     | 0x0306           | placa>   | numero de 2 bytes              |                   |                   |
| notificação de template de biometria não cadastrado | 0x0307           | placa>   |                                |                   |                   |

### 2.3 DETALHES DE COMANDOS <a name="detalhes-de-comandos"></a>

Os comandos são divididos em 4 seções:

- Funções;
- Leitura/escrita de configuração;
- Notificações;
- Autenticação.

#### 2.3.1 Funções <a name="#funcoes"></a>

##### 2.3.1.1 Libera entrada (0x0001) <a name="libera-entrada"></a>

Libera catraca na direção de entrada com mensagem opcional. As configurações de direção são definidas no capítulo 2.3.2.1.

```
Firmware V2.0.0 R0 e posteriores.
```

**Argumentos:**

1) mensagem.
   A mensagem deve ser codificada em ASCII e com no máximo 16 caracteres de
   comprimento. Caso a mensagem não seja desejada, o campo deve ser preenchido com zeros.

##### 2.3.1.2 Libera saída (0x0002) <a name="libera-saida"></a>

Libera catraca na direção de saída com mensagem opcional. As configurações de direção são definidas no capítulo 2.3.2.1.

```
Firmware V2.0.0 R0 e posteriores.
```

Argumentos:

1) mensagem.
   A mensagem deve ser codificada em ASCII e com no máximo 16 caracteres de
   comprimento. Caso a mensagem não seja desejada, o campo deve ser preenchido com zeros.

##### 2.3.1.3 Reinicia (0x0003) <a name="reinicia"></a>

Reinicialização da catraca.

```
Firmware V2.0.0 R0 e posteriores.
```

Argumentos:

1) não há.

##### 2.3.1.4 Mensagem temporária (0x0004) <a name="mensagem-temporaria"></a>

Define mensagem de notificação de usuário. Relacionado com o comando `Notifica usuário` em 2.3.1.5.

```
Firmware V2.1.0 R0 e posteriores.
```

Argumentos:

1) mensagem.
   A mensagem deve ser codificada em ASCII e com no máximo 16 caracteres de

comprimento.

##### 2.3.1.5 Notifica usuário (0x0005) <a name="notifica-usuario"></a> <sup>Firmware V2.1.0 R0 ou posterior, <sup>mais cores a partir de V2.1.1 R0.</sup></sup>

Apresenta uma notificação ao usuário da catraca, com os Parâmetros especificados.

```
Firmware V2.1.0 R0 e posteriores, mais cores a partir de V2.1.1 R0.
```

Argumentos:

1) Duração;
   Número de 16 bits que estabelece por quanto tempo em milissegundos a notificação
   permanece no painel da catraca.
2) Toque;
   Número de 8 bits que estabelece o efeito sonoro emitido pela notificação.
   TODO: encontrar descrição de toques melhor que RTTTL.
   Valor Descrição
   0 Sem toque
   1 "Beep:d=4,o=6,b=180:16c,8g"
   2 "err:d=4,o=4,b=180:f#,32p,f#"
   3 "ntf:d=4,o=5,b=180:16a4,16d,32p.,16a4"
3) Cor;
   Número de 8 bits que define a cor dos leds do painel durante a notificação. Os códigos
   de cores são definidos conforme a tabela abaixo:

| Valor | Cor                  |
|-------|----------------------|
| 0     | Não muda de cor      |      
| 1     | Vermelho             |     
| 2     | Verde à direita      |    
| 3     | Verde à esquerda     |   
| 4     | Azul                 |  
| 5     | Verde dos dois lados | 
| 6     | Ciano                |
| 7     | Magenta              |
| 8     | Amarelo              |

4) Mostrar texto.
   Número de 8 bits que define se a mensagem de notificação de usuário (2.3.1.5) deve ser
   mostrada no painel.

| Valor | Descrição           |
|-------|---------------------|
| 0     | Não mostra mensagem |
| != 0  | Mostra mensagem     |

##### 2.3.1.6 Libera bidirecional (0x0006) <a name="libera-bidirecional"></a>

Libera um giro da catraca, ou na direção de entrada, ou na direção de saída. Uma
mensagem pode ser apresentada opcionalmente.

```
Firmware V2.1.0 R0 e posteriores.
```

Argumentos:

1) Mensagem.
   A mensagem deve ser codificada em ASCII e com no máximo 16 caracteres de
   comprimento. Caso a mensagem não seja desejada, o campo deve ser preenchido com zeros.

##### 2.3.1.7 Reset de periférico (0x0007) <a name="reset-periferico"></a>

Envia o sinal de `hardware reset` para os periféricos conectados. Este comando reinicia
o módulo escâner biométrico.

```
Firmware V2.1.1 R0 e posteriores.
```

Argumentos:

Não possui.

#### 2.3.2 Leitura/escrita de configurações <a name="leitura-escrita-configuracoes"></a>

Os comandos de leitura/escrita de configuração permitem consultar e modificar
parâmetros que persistem após a catraca ser desligada. Todos os parâmetros possuem comandos
de consulta e de modificação: alguns destes parâmetros são `somente leitura`. Comandos de
modificação para parâmetros somente leitura são mantidos para manter a consistência da
interface, porém tentativas de modificá-los são ignoradas.
Cada parâmetro possui um par de comandos associado sendo 0x01XX o id de comando
para escrita de parâmetros. A leitura correspondente é feita pelo id de comando 0x02XX.
Os argumentos, dentro de um comando de leitura enviado à catraca, podem ser
ignorados. Os comandos de leitura são respondidos pela catraca com um pacote com o mesmo
id de comando da leitura, e com os parâmetros devidamente preenchidos de acordo com o
formato de argumentos para o comando correspondente.

Ex.:

Enviado:
`53 03 01 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 c`

Recebido:
`53 03 01 5e 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 c`

##### 2.3.2.1 Direção (0x0101, 0x0201) <a name="direcao"></a>

Define as atribuições de direções de entrada e saída.
As direções são estabelecidas de acordo com a direção do mecanismo conforme imagem
abaixo:

```
Firmware V2.0.0 R0 e posteriores.
```

Argumentos:

1) Direção de entrada.
   Número de 8 bits que estabelece a atribuição de direção.

| Valor  | Descrição                                                 |
|--------|-----------------------------------------------------------|
| 0      | Entrada no sentido horário, Saída no sentido anti-horário |
| != 0   | Entrada no sentido anti-horário, Saída no sentido horário |

##### 2.3.2.2 Controle(0x0102, 0x0202) <a name="controle"></a>

Estabelece o controle de permissões de fluxo da catraca. A catraca pode gerenciar a
passagem de entrada e saída de maneira independente. Cada uma das direções podem ser
configuradas de duas maneiras possíveis:

- Livre: a catraca está sempre liberada nesta direção;
- Controlada: a catraca está bloqueada por padrão e pode liberar momentaneamente um único giro a cada comando de liberação (2.3.1.1, 2.3.1.2 e 2.3.1.6).

ATUALIZAÇÃO:
A configuração de controle é mantida por retrocompatibilidade. Considere utilizar o
modo de controle estendido (2.3.2.15).

```
Firmware V2.0.0 R0 e posteriores.
```

Argumentos:

1) Modo.
   Número de 8 bits que estabelece as permissões de fluxo. A configuração de entrada/saída
   é definida conforme a tabela abaixo:

| Valor | Descrição                            |
|-------|--------------------------------------|
| 0     | Entrada livre, Saída livre           |
| 1     | Entrada controlada, Saída livre      |
| 2     | Entrada livre, Saída controlada      |
| 3     | Entrada controlada, Saída controlada |

##### 2.3.2.3 Id de dispositivo (0x0103, 0x0203) <a name="id-dispositivo"></a>

O id de dispositivo permite ao anfitrião numerar cada catraca para facilitar sua identificação, em um ambiente com múltiplas catracas.

```
Firmware V2.0.0 R0 e posteriores.
```

Argumentos:

1) Id.
   Número de 16 bits. Este número pode ser configurado livremente.

##### 2.3.2.4 Configuração de rede (0x0104, 0x0204) <a name="configuracao-rede"></a>

Define as configurações de rede. O modo de endereço IP pode ser dinâmico ou estático.
Caso estático, o endereço de IP e a mascara de rede devem ser fornecidos.
O equipamento é compatível apenas com o protocolo Ipv4.

```
Firmware V2.0.0 R0 e posteriores.
```

Argumentos:

1) Modo;
   Número de 8 bits com valor igual a 0 para IP dinâmico e diferente de 0 para IP estático.
2) Endereço de IP;
   Número de 32 bits. Cada octeto do endereço IPv4 é armazenado em um byte nas ordens
   correspondentes. Ex.: 169.254.37.28 ~ 0xA9FE251C.
3) Mascara.
   Número de 32 bits. Mascara IPv4 codificada do mesmo modo que o endereço IP.

NOTA: A modificação deste parâmetro se efetiva apenas depois da reinicialização.

##### 2.3.2.5 Número MAC (0x0105, 0x0205) <a name="numero-mac"></a>

Número que identifica dispositivo de rede.

```
Firmware V2.0.0 R0 e posteriores.
```

Argumentos:

1) numero MAC.
   Número de 48 bits.

NOTA: A modificação deste parâmetro se efetiva apenas depois da reinicialização.

##### 2.3.2.6 Mensagem da tela inicial linha 1 (0x0106, 0x0206) <a name="mensagem-tela1"></a>

Mensagem que aparece na tela inicial da catraca na primeira linha.

```
Firmware V2.0.0 R0 e posteriores.
```

Argumentos:

1) mensagem.
   A mensagem deve ser codificada em ASCII e com no máximo 16 caracteres de
   comprimento.

##### 2.3.2.7 Mensagem da tela inicial linha 2 (0x0107, 0x0207) <a name="mensagem-tela2"></a>

Mensagem que aparece na tela inicial da catraca na segunda linha.

```
Firmware V2.0.0 R0 e posteriores.
```

Argumentos:

1) mensagem.
   A mensagem deve ser codificada em ASCII e com no máximo 16 caracteres de
   comprimento.

##### 2.3.2.8 Modo de tela inicial (0x0108, 0x0208) <a name="modo-tela"></a>

A tela inicial pode apresentar mensagens ou contadores da quantidade de passagem pela
catraca em cada direção.

```
Firmware V2.0.0 R0 e posteriores.
```

Argumentos:

1) Modo.
   Número de 8 bits. Estabelece o conteúdo da pagina inicial conforme a tabela abaixo:

| Valor | Descrição  |
|-------|------------|
| 0     | Mensagens  |
| 1     | Contadores |

##### 2.3.2.9 Silenciar buzzer (0x0109, 0x0209) <a name="silenciar-buzzer"></a>

É possível silenciar o Buzzer. Esta configuração silencia apenas os sons padrões, os alertas de erro não são silenciáveis.

```
Firmware V2.0.0 R0 e posteriores.
```

Parâmetros:

1) Silenciar.
   Número de 8 bits.

| Valor | Descrição     |
|-------|---------------|
| 0     | Não silenciar |
| != 0  | Silenciar     |

##### 2.3.2.10 Tempo de liberação (0x010A, 0x020A) <a name="tempo-liberacao"></a>

Quando um comando de liberação (8, 9 e 11) é enviado, a catraca irá permitir uma passagem durante um intervalo de tempo. Ao término deste intervalo, a liberação será suspensa, e a catraca voltará ao comportamento padrão conforme as configurações de fluxo (2.3.2.2 e 2.3.2.15).

```
Firmware V2.0.0 R0 e posteriores.
```

Argumentos:

1) Duração.
   Número de 16 bits. Duração do intervalo de liberação em milissegundos.

##### 2.3.2.11 Senha do menu (0x010B, 0x020B) <a name="senha-menu"></a>

Configuração de senha de acesso ao menu acessado no painel da catraca.

```
Firmware V2.0.0 R0 e posteriores.
```

Argumentos:

1) Senha.
   A senha deve conter até 16 dígitos codificados em ASCII.

##### 2.3.2.12 Versão de firmware (0x010C, 0x020C) <a name="versao-firmware"></a>

Parâmetro somente leitura que informa a versão atual do firmware.

```
Firmware V2.0.0 R0 e posteriores.
```

Argumentos:

1) Versão principal;
   Número de 8 bits.
2) Versão secundária;
   Número de 8 bits.
3) Versão de correção;
   Número de 8 bits.
4) Revisão.
   Número de 8 bits.

##### 2.3.2.13 Número serial (0x010D, 0x020D) <a name="numero-serial"></a>

Parâmetro somente leitura que informa o número de série da catraca.

```
Firmware V2.0.0 R0 e posteriores.
```

Argumentos:

1) Número de série.
   Numero de 32 bits.

##### 2.3.2.14 Modo de biometria (0x010E, 0x020E) <a name="modo-biometria"></a>

Configuração de escâner biométrico. Define como o computador interage com o escâner biométrico. O escâner possui um sensor de contato que dispara quando o usuário coloca o dedo.
No modo `automático`, é possível interagir com o escâner diretamente, utilizando a placa de controle como uma ponte. No modo `apensas local`, o escâner é controlado apenas pela placa, e possui funcionalidades limitadas.

```
Firmware V2.0.0 R2 e posteriores.
```

Argumentos:

1) Modo.
   Numero de 8 bits.

| Valor | Descrição      |
|-------|----------------|
| 0     | Automático     |
| != 0  | Apenas local   |

##### 2.3.2.15 Modo de controle estendido (0x010F, 0x020F) <a name="modo-controle-ext"></a>

Estabelece o controle de permissões de fluxo da catraca. A catraca pode gerenciar a passagem de entrada e saída de maneira independente. Cada uma das direções podem ser configuradas de três maneiras possíveis:

- Livre: a catraca está sempre liberada nesta direção;
- Controlada: a catraca está bloqueada por padrão e pode liberar momentaneamente um   único giro a cada comando de liberação (2.3.1.1, 2.3.1.2 e 2.3.1.6).
- Bloqueada: a catraca impede a passagem nesta direção.
  Pictogramas indicam os sentidos em que a passagem é permitida com uma seta verde, e o sentido em que a passagem é bloqueada, com um `X` vermelho.

```
Firmware V2.1.0 R0 e posteriores.
```

Argumentos:

1) Modo;
   Numero de 8 bits. Configuração conforme tabela abaixo:

| Valor | Descrição             |
|-------|-----------------------|
| 0     | Controlada Liberada   |
| 1     | Controlada Bloqueada  |
| 2     | Controlada Controlada |
| 3     | Liberada Controlada   |
| 4     | Bloqueada Controlada  |
| 5     | Liberada Liberada     |
| 6     | Liberada Bloqueada    |
| 7     | Bloqueada Liberada    |
| 8     | Bloqueada Bloqueada   |

2) Controle de pictograma. Os pictogramas podem ser ligados/desligados conforme a
   tabela abaixo:

<table><tr><th colspan="1" rowspan="2" valign="top">Valor </th><th colspan="2">Descrição </th></tr>
<tr><td colspan="1">Entrada</td><td colspan="1">Saída</td></tr>
<tr><td colspan="1">0 </td><td colspan="1">Ligado </td><td colspan="1">Ligado </td></tr>
<tr><td colspan="1">1 </td><td colspan="1">Ligado </td><td colspan="1">Desligado </td></tr>
<tr><td colspan="1">2 </td><td colspan="1">Desligado </td><td colspan="1">Ligado </td></tr>
<tr><td colspan="1">3 </td><td colspan="1">Desligado </td><td colspan="1">Desligado </td></tr>
</table>

##### 2.3.2.16 Contador de giros (0x0110, 0x0210) <a name="contador-giros"></a>

Este parâmetro registra a quantidade de passagens de entrada e de saída. Comandos deescrita (0x0110) para este parâmetro são tratadas como zeramento.

```
Firmware V2.1.0 R0 e posteriores.
```

Argumentos:

1) Contador de entradas;
   Número de 32 bits.
2) Contador de saídas.
   Número de 32 bits.

##### 2.3.2.17 Flags de hardware (0x0111, 0x0211) <a name="flags-hardware"></a>

Estas flags possuem detalhes técnicos de configuração de dispositivos de hardware. A tabela abaixo detalha cada flag. Todas as flags são empacotadas em um vetor de bits.

<table><tr><th colspan="1" rowspan="2" valign="top">Nome </th><th colspan="1" rowspan="2" valign="top">Posição </th><th colspan="2">Valor </th></tr>
<tr><td colspan="1">0 </td><td colspan="1">1 </td></tr>
<tr><td colspan="1">RDM6300_ALT_PROT </td><td colspan="1">0 </td><td colspan="1">Protocolo padrão </td><td colspan="1">Protocolo alternativo </td></tr>
</table>

```
Firmware V2.1.1 R0 e posteriores.
```

Argumentos:

1) Flags.
   Vetor de 16*8 bits. Conjunto de flags de hardware.

### 2.3.2.18 Estados de inicialização (0x0112, 0x0212) <a name="estados-inicializacao"></a>

Parâmetro somente leitura que indica o estado de inicialização de cada módulo. Módulos
iniciados de maneira bem sucedida possuem flag igual a 1, e possuem flag igual a 0 caso
contrário. Todas as flags são empacotadas em um vetor de bits.

| Nome         | Posição | Descrição                    |
|--------------|---------|------------------------------|
| STC\_NETWORK | 0       | Dispositivo de rede          |
| STC\_SCANNER | 1       | Escâner de impressão digital |

```
Firmware V2.1.1 R0 e posteriores.
```

Argumentos:

1) flags.
   Vetor de 16*8 bits.

## 2.3.3 Notificações <a name="?"></a>  <a name="notificacoes"></a>

Mensagens de notificação são enviadas para cada evento originado na catraca. Estas
mensagens são enviadas automaticamente, sem solicitação prévia.

### 2.3.3.1 Notificação de id com Rfid (0x0301) <a name="notificacao-rfid"></a>

Notifica a leitura de uma etiqueta RFID.

```
Firmware V2.0.0 R0 e posteriores.
```

Argumentos:

1) Número de identificação.
   Texto numérico com 16 caracteres em ASCII com o numero da etiqueta lida.

### 2.3.3.2 Notificação de id com código de barras (0x0302) <a name="notificacao-barra"></a>

Notifica a leitura de um cartão com código de barras.

```
Firmware V2.0.0 R0 e posteriores.
```

Argumentos:

1) Número de identificação.
   Texto numérico com 16 caracteres em ASCII com o numero do cartão lido.

### 2.3.3.3 Notificação de id com teclado (0x0303) <a name="notificacao-teclado"></a>

Notifica a inserção de senha no teclado.

```
Firmware V2.0.0 R0 e posteriores.
```

Argumentos:

1) Número de identificação.
   Texto numérico com 16 caracteres em ASCII com o numero inserido.

### 2.3.3.4 Notificação de passagem pela catraca (0x0304) <a name="notificacao-passar"></a>

Notifica cada passagem pela catraca. Nesta notificação também é informado a direção
de passagem, assim como a quantidade total atualizada de passagens nesta direção.
Firmware V2.0.0 R0 e posteriores, extensão com contadores a partir da V2.1.0 R0.

Argumentos:

1) Direção;
   Número de 8 bits.

| Valor | Descrição |
|-------|-----------|
| 1     | Entrada   |
| 2     | Saída     |

2) Passagens.
   Número de 32 bits. Quantidade atualizada de passagens na direção especificada.

### 2.3.3.5 Notificação de timeout de liberação (0x0305) <a name="notificacao-timeout"></a>

Ao final do intervalo (2.3.2.10) após uma liberação sem que uma passagem ocorra, a liberação será suspensa, havendo um evento de timeout.

```
Firmware V2.0.0 R0 e posteriores.
```

Argumentos:
Não há.

### 2.3.3.6 Notificação de id com biometria (0x0306) <a name="notificacao-biometria"></a>

Notifica evento em que escâner biométrico reconhece um usuário cadastrado.

```
Firmware V2.0.0 R0 e posteriores.
```

Argumentos:

1) Número de identificação.
   Número de 16 bits. Número cadastrado do usuário identificado.

### 2.3.3.7 Notificação de template de biometria não cadastrado (0x0307) <a name="notificacao-template"></a>

Notifica evento em que escâner biométrico não identifica o usuário escaneado.

```
Firmware V2.0.0 R0 e posteriores.
```

Argumentos:
Não há.

