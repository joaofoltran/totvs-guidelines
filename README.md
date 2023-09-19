# PostgreSQL Guidelines

O propósito deste documento é fornecer padrões de arquitetura e parametrização para que aplicações nascendo ou migrando para o PostgreSQL possuam o ambiente mais performático e seguro a desastres possível.

O documento serve para todos os profissionais DBA, DEV e DevOps.
&NewLine;
&NewLine;

## Arquitetura

Pontos interessantes de arquitetura voltados a performance, alta disponibilidade e segurança:

 - Connection Pooling (pgBouncer)
 - Instância DR (physical replication)
 - Particionamento (nativo, timescaledb)
 - Cuidados com querys automáticas de ORMs
&NewLine;
&NewLine;

#### Connection Pooling

Toda vez que uma requisição é recebida, existem diversas atividades que o Postgres precisa realizar para iniciar, manter e depois fechar uma conexão. Todas essas atividades são multiplicadas pela quantidade de requisições que seu cluster recebe por segundo.

Através da utilização de connection pooling, é mantido uma **pool** de conexões ativas ao backend do Postgres que serão distribuídas para todas as requisições que chegarem ao seu banco de dados, desta maneira o backend não precisa passar por todas aquelas atividades para abrir uma nova conexão, diminuindo o overhead.

Para utilização do PgBouncer, a aplicação invés de ser apontada para a porta direta ao banco de dados, será apontada para a porta do pooler.
&NewLine;
&NewLine;

#### Instância DR

Possuir uma instância específica para Disaster Recovery é extremamente importante em casos de desastre.
No Postgres essas instâncias são mantidas através da replicação física nativa, onde arquivos de log de transação chamado WALs são enviados e aplicados na máquina "secundária" para mantê-la atualizada com as últimas alterações realizadas na máquina principal.

#### Particionamento

Particionamento é uma arquitetura extremamente importante para manter tabelas que guardem dados históricos, ao particionar uma tabela desta maneira fica mais fácil de deletar dados que não são mais necessários e também proporciona performance
