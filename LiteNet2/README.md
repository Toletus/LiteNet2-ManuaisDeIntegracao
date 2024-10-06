# Toletus LiteNet2 - Manual de Integração

## Introdução

Este manual descreve como integrar a catraca Toletus LiteNet2 à sua aplicação, utilizando a interface de rede Ethernet. A comunicação é feita através do protocolo TCP/IP, modo IPv4, usando as seguintes portas:

- **Porta 7878**: Interface com a catraca.
- **Porta 7879**: Interface com o dispositivo de biometria.

## Estrutura de Pacotes

Todos os comandos são enviados em pacotes de tamanho constante de 20 bytes, seguindo o formato:

| Prefixo | Identificação de Comando | Dados          | Sufixo |
|---------|---------------------------|----------------|--------|
| 1 byte  | 2 bytes                   | 16 bytes       | 1 byte |

- **Prefixo**: Sempre igual a `83`.
- **Sufixo**: Sempre igual a `195`.
- **Identificação de Comando**: 16 bits, identifica o comando.
- **Dados**: Dependem do comando, com argumentos variáveis.

Pacotes que não seguirem essa estrutura serão ignorados.

## Lista de Comandos

### Funções Principais

1. **Libera Entrada (0x0001)**
   - Libera a catraca na direção de entrada, com mensagem opcional.
   - **Argumento**: Mensagem de até 16 caracteres em ASCII. Preencha com zeros se não desejar uma mensagem.

2. **Libera Saída (0x0002)**
   - Libera a catraca na direção de saída, com mensagem opcional.
   - **Argumento**: Mensagem de até 16 caracteres em ASCII. Preencha com zeros se não desejar uma mensagem.

3. **Reinicia (0x0003)**
   - Reinicializa a catraca.
   - **Argumento**: Não possui.

4. **Mensagem Temporária (0x0004)**
   - Define uma mensagem de notificação ao usuário.
   - **Argumento**: Mensagem de até 16 caracteres em ASCII.

5. **Notifica Usuário (0x0005)**
   - Apresenta uma notificação ao usuário da catraca.
   - **Argumentos**:
     - **Duração**: Tempo em milissegundos.
     - **Toque**: Definição de som (0: Sem toque, 1-3: Diversos toques).
     - **Cor**: Define a cor dos LEDs (0: Não muda, 1-8: Várias cores).
     - **Mostrar Texto**: Define se a mensagem de notificação deve ser exibida (0: Não, diferente de 0: Sim).

6. **Libera Bidirecional (0x0006)**
   - Libera um giro da catraca, tanto na direção de entrada quanto na de saída, com mensagem opcional.
   - **Argumento**: Mensagem de até 16 caracteres em ASCII. Preencha com zeros se não desejar uma mensagem.

7. **Reset de Periférico (0x0007)**
   - Reinicia os periféricos conectados, como o módulo de biometria.
   - **Argumento**: Não possui.

### Leitura/Escrita de Configurações

1. **Consulta Direção (0x0101)**
   - Consulta a direção atual configurada.

2. **Configura Direção (0x0201)**
   - Define as atribuições de entrada e saída da catraca.
   - **Argumento**: Valor de 8 bits que define se a entrada é no sentido horário ou anti-horário.

3. **Consulta Controle (0x0102)**
   - Consulta o controle de permissões de fluxo da catraca.

4. **Configura Controle (0x0202)**
   - Define o controle de permissões de fluxo da catraca.
   - **Argumento**: Valor de 8 bits que define o modo de controle (entrada livre/saída livre, controlada, etc).

5. **Consulta Configuração de Rede (0x0104)**
   - Consulta o modo de endereço IP da catraca (dinâmico ou estático).

6. **Configuração de Rede (0x0204)**
   - Define as configurações de rede (modo, IP e máscara).
   - **Argumentos**:
     - **Modo**: 0 para IP dinâmico, diferente de 0 para estático.
     - **Endereço IP**: IPv4 em 4 bytes.
     - **Máscara**: Máscara IPv4 em 4 bytes.

7. **Consulta Número MAC (0x0105)**
   - Consulta o número MAC do dispositivo.

8. **Configura Número MAC (0x0205)**
   - Configura o número MAC do dispositivo.
   - **Argumento**: Número MAC em 48 bits.

9. **Consulta Mensagem da Tela Inicial (0x0106/0x0107)**
   - Consulta a mensagem exibida na primeira e segunda linha da tela inicial.

10. **Configura Mensagem da Tela Inicial (0x0206/0x0207)**
    - Configura a mensagem exibida na primeira e segunda linha da tela inicial.
    - **Argumento**: Mensagem de até 16 caracteres em ASCII.

11. **Consulta Modo de Tela Inicial (0x0108)**
    - Consulta o modo de exibição da tela inicial (mensagem ou contadores).

12. **Configura Modo de Tela Inicial (0x0208)**
    - Define o modo de exibição da tela inicial.
    - **Argumento**: Valor de 8 bits que define o conteúdo (mensagens ou contadores).

13. **Consulta Silenciar Buzzer (0x0109)**
    - Consulta se o buzzer está silenciado.

14. **Configura Silenciar Buzzer (0x0209)**
    - Define se o buzzer deve ser silenciado.
    - **Argumento**: Valor de 8 bits (0: Não silenciar, diferente de 0: Silenciar).

15. **Consulta Tempo de Liberação (0x010A)**
    - Consulta o tempo de liberação da catraca.

16. **Configura Tempo de Liberação (0x020A)**
    - Define o tempo de liberação da catraca.
    - **Argumento**: Duração em milissegundos (16 bits).

17. **Consulta Senha do Menu (0x010B)**
    - Consulta a senha de acesso ao menu da catraca.

18. **Configura Senha do Menu (0x020B)**
    - Define a senha de acesso ao menu da catraca.
    - **Argumento**: Senha de até 16 dígitos em ASCII.

19. **Consulta Versão de Firmware (0x010C)**
    - Consulta a versão atual do firmware do dispositivo.

20. **Consulta Número Serial (0x010D)**
    - Consulta o número de série do dispositivo.

21. **Consulta Modo de Biometria (0x010E)**
    - Consulta o modo de operação do escâner biométrico.

22. **Configura Modo de Biometria (0x020E)**
    - Define o modo de operação do escâner biométrico.
    - **Argumento**: Valor de 8 bits (0: Automático, diferente de 0: Apenas local).

23. **Consulta Modo de Controle Estendido (0x010F)**
    - Consulta o modo de controle estendido da catraca.

24. **Configura Modo de Controle Estendido (0x020F)**
    - Define o modo de controle estendido da catraca.
    - **Argumentos**: Dois valores de 8 bits para configurar o controle de entrada e saída.

25. **Consulta Contador de Giros (0x0110)**
    - Consulta o contador de giros (quantidade de passagens).

26. **Configura Contador de Giros (0x0210)**
    - Zera o contador de giros.

### Notificações

1. **Notificação de ID com RFID (0x0301)**
   - Notifica a leitura de uma etiqueta RFID.
   - **Argumento**: Número de identificação em ASCII.

2. **Notificação de ID com Código de Barras (0x0302)**
   - Notifica a leitura de um código de barras.
   - **Argumento**: Número de identificação em ASCII.

3. **Notificação de ID com Teclado (0x0303)**
   - Notifica a inserção de senha no teclado.
   - **Argumento**: Número de identificação em ASCII.

4. **Notificação de Passagem pela Catraca (0x0304)**
   - Notifica cada passagem pela catraca, indicando a direção e o total de passagens.
   - **Argumentos**:
     - **Direção**: Valor de 8 bits (1: Entrada, 2: Saída).
     - **Passagens**: Quantidade total de passagens na direção especificada (32 bits).

5. **Notificação de Timeout de Liberação (0x0305)**
   - Notifica o término do intervalo de liberação sem que uma passagem ocorra.

6. **Notificação de ID com Biometria (0x0306)**
   - Notifica o reconhecimento de um usuário cadastrado pelo escâner biométrico.
   - **Argumento**: Número de identificação (16 bits).

7. **Notificação de Template de Biometria Não Cadastrado (0x0307)**
   - Notifica quando o escâner biométrico não identifica o usuário.

### Autenticação

1. **Solicitação de Desafio de Liberação (0x0401)**
   - Solicita um desafio para liberar a catraca.

2. **Mensagem de Desafio 1 (0x0402)**
   - Envia uma mensagem de desafio para autenticação.

3. **Resposta para o Desafio (0x0404)**
   - Responde ao desafio de autenticação.

## Controle Estendido

O controle de permissões de fluxo da catraca é feito através do **Modo de Controle Estendido (0x010F/0x020F)**, permitindo configurar cada direção em modo:

- **Livre**: Sempre liberada.
- **Controlada**: Bloqueada por padrão, liberada mediante comando.
- **Bloqueada**: Passagem não permitida.

## Configurações de Tela e Biometria

- **Mensagem da Tela Inicial (0x0106/0x0206 e 0x0107/0x0207)**: Configura a mensagem exibida na primeira e segunda linha da tela inicial.
- **Modo de Biometria (0x010E/0x020E)**: Configura o modo de interação com o escâner biométrico (automático ou apenas local).

## Exemplos de Uso

- **Libera Entrada**: Enviar um pacote com o prefixo `83`, ID de comando `0001`, mensagem (opcional) e sufixo `C3`.
- **Reinicia Catraca**: Enviar `83 00 03 00 ... 00 C3`.

## Observações

- Todos os comandos devem ser enviados em **strings hexadecimais**.
- Conteúdo textual é codificado em **ASCII**.
- Conteúdo numérico com mais de 8 bits é armazenado em **little-endian**.

## Versão do Manual

- **V1.0.22.0131**
