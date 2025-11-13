# Relatório Técnico: Análise de Sincronização de Threads

    Este relatório analisa o comportamento de três implementações do problema Produtor-Consumidor, com foco nos efeitos da sincronização na integridade dos dados.

## Atividade 1: Execução Sem Sincronização

### Detalhes da Execução
    Foi executado o código `MeuDadoThreadsJava.java`, onde Produtor e Consumidor acessam a mesma variável `Dado` sem qualquer tipo de trava.

### Análise das Saídas (Logs)
    Os logs mostram o problema clássico de Condição de Corrida

* **Dados Repetidos:** O Consumidor leu o mesmo valor várias vezes ex: "Consumidor: 29" aparece cinco vezes seguidas.
* **Dados Perdidos:** O Produtor armazenou valores que o Consumidor nunca chegou a ler.
* **Imprevisibilidade:** As duas execuções geraram logs  diferentes.

    A ausência de sincronização torna o programa incorreto, pois as threads acessam o recurso compartilhado ao mesmo tempo, corrompendo os dados.

## Atividade 2: Sincronização com Monitores (`synchronized`)

### Detalhes da Execução
    Foi usado o `MeuDadoMonitorJava.java`, que usa blocos `synchronized` para garantir a "exclusão mútua" (apenas uma thread por vez entra na área crítica).

### Análise das Saídas (Logs)
    Os logs mostram uma execução perfeitamente ordenada:

* `Produtor usando Monitor: 0`
* `Consumidor usando Monitor: 0`
* `Produtor usando Monitor: 1`
* `Consumidor usando Monitor: 1`

    O problema da integridade dos dados foi resolvido. No entanto, o método usou *busy-waiting* (laços `while(...)` que verificam ativamente), o que desperdiça CPU. Funciona, mas não é eficiente.

## Atividade 3: Sincronização com Eventos (`wait/notify`)

### Detalhes da Execução
    Foi usado o `MeuDadoEventJava.java`, que implementa a comunicação entre as threads. A thread que precisa esperar "dorme" (`wait()`) e só "acorda" (`notify()`) quando a outra termina sua parte.

### Análise das Saídas (Logs)
    Os logs também mostram uma execução perfeitamente ordenada e sem perdas, similar à Atividade 2.

    A grande vantagem é a eficiência: as threads não gastam processamento enquanto esperam. Elas pausam e liberam o monitor, permitindo um uso muito melhor dos recursos do sistema.

## Análise Comparativa e Opinião Pessoal

    A execução das três atividades foi importamte para entender o impacto da sincronização:

1.  **Sem Sincronização (Atividade 1):** É o cenário do problema. Leva à corrupção de dados e resultados imprevisíveis. Não é uma opção viável.
2.  **Monitor (Atividade 2):** Resolve o problema da integridade (os dados ficam corretos), mas é ineficiente por usar *busy-waiting*.
3.  **Eventos (Atividade 3):** É a solução ideal. Resolve a integridade dos dados e é eficiente, pois as threads pausam em vez de "esperar gastando CPU".

    Fica claro que a ausência de sincronização quebra o programa. Entre as soluções, o uso de `wait()` e `notify()` é tecnicamente superior por garantir a ordem correta e a melhora do sistema.