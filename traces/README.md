Não consigo deixar os arquivos traces e usar o dtruss ou dtrace (equivalente do macOS do strace), porque meu sistema está com System Integrity Protection (SIP) ativado. O mac simplesmente não deixa a monitoração das syscalls através desses métodos mais simples, sem desligar o SIP.
Seria necessario utilizar o fs_usage e o logging e learning curve seria bem mais alto do que o processo mais simples em sistemas sem SIP. Lamento não fornecer os arquivos, mas espero que o troubleshooting e tentativas sejam o suficiente, pois eu com certeza aprendi algo novo.

Como adicional, gostaria de demonstrar que poderia ter simplesmente colado de qualquer aluno. Um problema no sistema de permissões e encapsulamento do main repository me deixa ver todos os commits de todos os alunos. Estarei deixando aqui alguns arquivos como sample de que é possível. Seria extremamente banal simplesmente colar um output exato de syscalls invariáveis, mas preferi estudar os conflitos do SIP e ser transparente sobre isso, que inclusive, potencialmente outro aluno pode ter usado.

Gostaria de acrescentar que não tenho nenhuma conexão aos alunos cujos samples foram adicionados, e foram meramente escolhidos aleatoriamente.
Posso demonstrar em aula a possibilidade de visitar os commits (e nesse contexto prático, o repositório/exercícios de todos os alunos).

-Alice
