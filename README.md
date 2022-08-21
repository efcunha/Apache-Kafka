# Instalando o Apache Kafka localmente

# Ferramentas necessarias para o projeto

https://kafka.apache.org/downloads

Descompacte o arquivo baixado em uma pasta de sua preferencia.

Altere os aequintes arquivos para levantar o cluster Kafka.

$ cd kafka_x.xx-x.x.x/config

$ mkdir -p /home/usuario/kafka_x.xx-x.x.x/data/

Edite os sequinte arquivo server.properties

broker.id=1
default.replication.factor=3
listeners=PLAINTEXT://:9091
offsets.topic.replication.factor=3
transaction.state.log.replication.factor=3
log.dirs=/home/usuario/kafka_x.xx-x.x.x/data/

mv server.properties server1.properties

# Start serviços brockers Kafka 1
bin/kafka-server-start.sh config/server1.properties

$ cd kafka_x.xx-x.x.x/config

cp server1.properties server2.properties

Edite os sequinte arquivo server2.properties

broker.id=2
listeners=PLAINTEXT://:9092

# Start serviços brockers Kafka 2
bin/kafka-server-start.sh config/server2.properties

$ cd kafka_x.xx-x.x.x/config

cp server2.properties server3.properties

Edite os sequinte arquivo server2.properties

broker.id=3
listeners=PLAINTEXT://:9093

# Start serviços brockers Kafka 3
bin/kafka-server-start.sh config/server3.properties

$ cd kafka_x.xx-x.x.x/config

cp server3.properties server4.properties

Edite os sequinte arquivo server2.properties

broker.id=4
listeners=PLAINTEXT://:9094

# Start serviços brockers Kafka 4
bin/kafka-server-start.sh config/server4.properties

# Verifica os topicos dos brockers Kafka

bin/kafka-topics.sh --bootstrap-server localhost:9091 --describe
bin/kafka-topics.sh --bootstrap-server localhost:9092 --describe
bin/kafka-topics.sh --bootstrap-server localhost:9093 --describe
bin/kafka-topics.sh --bootstrap-server localhost:9094 --describe

https://www.jetbrains.com/help/idea/installation-guide.html

OBS: O JDK utilize o mais recente disponivel.

# Vamos começar.

A gente vai aprender bastante coisa, a gente vai aprender o que que é mensageria, como que funciona e características especiais de Kafka.

Então, a gente vai ver alguns diferenciais de Kafka, em relação a outros sistemas de mensageria e ver porque que o sistema de streams consegue de uma maneira bem inteligente e interessante paralelizar, distribuir o nosso processamento em diversos serviços e diversos sistemas ao mesmo tempo, serializa-lo quando for necessário e interessante para a gente.

A gente vai ver como funciona as partições, a gente vai ver os tópicos, a gente vai ver o broker rodando, vai criar diversos consumidores, grupos de consumidores, paralisar, rodar eles em paralelo, rodar com produtores, que geram diversas mensagens e que são consumidas, que geram de volta e vai.

E fazer esse emaranhado e entender como que um sistema tradicional pode ser quebrado em vários serviços de uma forma que utilize uma comunicação, através de mensagens, em que a gente adquire certas independências entre os sistemas.

Claro, o sistema não é completamente dependente, porque a gente tem um esquema que tenta colar o que está acontecendo lá e a semântica das mensagens que também é importante.

E a gente vai ver como tudo isso se conecta dentro do Kafka e a gente colocar tudo isso, mão na massa, produzir o código, ver tudo isso acontecendo, levantar servidor, derrubar servidor, ver o que acontece quando está de pé, quando está deitado, quando está caído ou seja os serviços, seja o produtor, seja o broker da Kafka.

Imagina um sistema de e-commerce, se é um sistema de e-commerce, o que que eu vou ter nele? Eu vou ter, por exemplo, o meu usuário acessando o meu sistema online. Então aqui dentro do meu usuário, eu tenho aqui o meu usuário, que é o meu cliente, o meu navegador, que eu vou chamar de usuário.

Então no navegador, esse meu cliente está acessando o quê? A web e através da web, acessa um servidor Http. Então o acesso a esse servidor Http é feito, ele é feito e funciona. Vou colocar aqui uma setinha, bonitinha ao lado direito e funciona.

Agora, o que mais que acontece? Vou diminuir aqui essa minha barra. Então, repara que o servidor Http tem diversas tarefas para fazer, por quê? Porque é um processo de compra, você está efetuando uma compra. Então, o que que eu tenho que fazer?

Eu tenho que verificar se é uma fraude, eu tenho que antes de verificar se é uma fraude, enviar um e-mail dizendo: “Olha, a sua compra está sendo processada”, aí verifico se é fraude. Aí, se for fraude, eu fico [XXIninteligívelXX] e te notifico, sistemas de segurança.

Alguma coisa de segurança, se não for fraude, o que que eu tenho que fazer? Eu tenho que de alguma maneira efetuar a compra, o pagamento de verdade. Se o pagamento for um sucesso, eu tenho que liberar o produto, por exemplo, se é um produto online, como um e-book.

Então eu tenho que gerar o e-book com a versão customizada para aquele usuário ou usuária, que tem um nome daquela pessoa, o CPF, etc., e aí, sim, enviar esse produto por e-mail. Então, repara que vai ficando cada vez um passo depois do outro, um passo depois do outro, cada vez mais complexo.

Uma coisa cada vez maior, cada vez maior. Vou separar isso aqui em setinhas, em setinhas mesmo, em quadradinhos e setinhas, para a gente ir vendo a coisa. Então o servidor, ele envia um e-mail. Aí, você fala: “Guilherme, ele poderia fazer a fraude?”.

Depois do e-mail, um e-mail do tipo, recebi o seu pedido, então estou processando por fraude. Então, poderia ser, depois, é verdade? Eu poderia fazer isso aqui, nessa ordem e colocar todo esse código dentro de um único sistema, funcionária. Quer dizer, primeiro o meu servidor http, eu tenho um servidor http, o que que ele vai fazer?

Lá dentro, ele envia um e-mail. O e-mail foi enviado? Verifica se é fraude. É fraude? Faz tal coisa e por aí, vai. Tudo dentro de um grande programa, uma linha depois da outra, um problema disso, muito direto para os sistemas é que, por exemplo, esperar o e-mail, quer dizer que tem que esperar um sistema externo me responder, o meu servidor SMTP, que envia e-mails.

E pode ser que esse servidor esteja fora do ar, pode ser que esse servidor esteja lerdo, pode ser que não sei o quê. Então quer dizer, eu vou demorar para começar o processo de fraude, porque eu estou esperando o e-mail. Então é muito comum que em sistemas web, a gente queira dar uma resposta para o nosso usuário, o mais rápido possível.

Isso é muito comum. Então é muito comum que esse tipo de tarefa, a gente faça e paralelo, a gente não faça isso sequencial. A gente costuma fazer isso em paralelos. Então, ele dispara o e-mail dispara o sistema de verificar se é fraude ao mesmo tempo. O que que quer dizer, dispara o e-mail, dispara o sistema, ele vai verifica se é fraude?

Quer dizer que pode ser no mesmo computador duas trets, quer dizer que pode ser eu me comunicando com dois computadores diferentes e falando: “Olha, o computador aí que tem outro programa, envia um e-mail, uma requisição http, via rest”, seja o lá o que for.

Aqui também, uma requisição http via rest ou uma outra trede, falando: “Detecta aí a fraude” e enquanto isso, eu já dei uma resposta aqui para o meu cliente dizendo: “Sua compra está sendo processada” e agora eu vou processando tudo isso em paralelo, na mesma máquinas, em máquinas distintas, são opções.

Essa comunicação feita http, via rest, via outro tipo de mensageria, etc., são várias opções, o tradicional seria, primeiro na mesma máquina com várias tredes, depois em máquinas distintas se comunicando via http, que é uma maneira comum de ser feito isso, entre sistemas.

E a gente vai... vamos para frente nesse sisteminha aqui, naqueles comentários que eu fiz, né? Então, eu detecto é fraude. Se é fraude ou não, eu vou ter que fazer alguma coisa, por exemplo, se for fraude... se não for fraude, eu tenho que fazer o quê?

Efetuar o pagamento, quer dizer, aí sim, eu vou lá e cobro do cartão de credito da pessoa, assumindo que é cartão de crédito. Depois que eu cobrei do cartão de crédito, o que que eu tenho que fazer? Se for sucesso, eu tenho que preparar o envio, vamos pensar num bem digital, como um PDF, um E-book.

Então, nesse caso, teria que gerar o PDF e por fim, eu teria que enviar o e-mail do PDF, então tudo isso teria que acontecer. Aqui, parece ter que ser sequencial, né? Porque eu não vou gerar o PDF, antes de gerar o pagamento ou pelo menos enviar o e-mail, eu não vou, antes de confirmar o pagamento, provavelmente.

Então, você vai definir a ordem, o que que você quer fazer em paralelo ou não, mas você começa a ter agora mais várias setinhas, eu tenho aqui uma setinha da fraude, desse sistema de fraude ou desse código de fraude, para essa próxima parte.

Aí, eu tenho do sistema de efetuar pagamento ou do código de efetuar pagamento, para gerar o PDF e por aí, vai. Eu estou tento até dificuldade de tanta setinha que eu tenho aqui, eu vou tendo dificuldade de arrastar esses caras, porque é muita coisa que tem que ser feita sequencialmente ou um código que chama http e o outro código.

E para pra pensar, isso é no caro de sucesso, no caso de sucesso, efetuei o pagamento e no caso de fracasso? “Ah, no caso de fracasse, por exemplo, do pagamento, eu gostaria de também enviar um e-mail, eu também vou ter que enviar um e-mail de fracasso, nesse caso”.

Então, nesse caso aqui, eu tenho que enviar um e-mail também, eu vou ter que ter um outro cara aqui, que é enviar o e-mail, que na verdade, não é e-mail do PDF, “Enviar o e-mail do fracasso”. Além disso tudo, eu gostaria, que eu tivesse suporte à produtos físicos.

Então, quer dizer, se é produto físico, eu tenho um estoque e na hora que você pediu a compra, eu já tenho que reservar esse estoque para você. Então, eu tenho que aqui, logo de cara, já reservar o estoque, tem que reservar o estoque, então eu vou e reservo o estoque.

Então eu tenho três... se são máquinas diferentes, com serviços diferentes, três requisições https, uma requisição http, outra requisição, outra, outra e por aí, vai. Ah, e calma aí, a compra foi confirmada? Efetuou o pagamento e a compra foi confirmada? E se ela for digital, o que que eu tenho que fazer?

Eu tenho que confirmar do estoque, eu reservei? Agora eu tenho que confirmar o estoque, confirmar que vai embora, confirmar o estoque e se deu fracasso, o que que eu tenho que fazer? Possivelmente, vai depender de como você quer fazer, a o ordem, cancelar do estoque, só se for um produto físico.

Então, se for esse caso, você vai ter que cancelar do estoque, se for físico. Calma aí, está ficando cada vez mais complexo e isso tudo aqui, eu posso ir falar: “Ah, eu tenho um serviço de estoque, que tem essas três URIs”, “Eu tenho o serviço de fraude, que em essa URI”, “O serviço de pagamento que tem essa URI”, “Eu tenho o serviço de PDF, que é essa URI”, “eu tenho o serviço de e-mail, que são essas três URIs”.

Mas todas essas comunicações, todas essas setas, sou eu Guilherme quem programo, sou em quem faço todas elas. Eu sei quem está na outra ponta e envio uma mensagem http ou a gente pode definir outro termo, de acordo como essa requisição é feita, notificando o que eu gostaria que fosse feito ou algo do gênero.

Vamos complicar mais ainda, como no mundo real. Tudo isso daqui, eu preciso de log, então eu preciso de toda a vez que eu disparo um e-mail, logar em algum lugar, que foi disparado um e-mail. Então, eu preciso de um sistema de log ou de algo de log, que eu tenha o quê?

Tudo o que acontece agora vamos lá, “Boa sorte Guilherme”, sofre aqui é quem desenha, quem tem que ficar desenhando na mão as coisas. Todo mundo aqui tem que fazer o quê? Acessar o sistema de log, porque tudo o que acontece, tem que ir para o sistema de log.

Tudo o que acontece, tem que ir para o sistema de log, por quê? Se eu quiser fazer uma auditoria, saber a ordem que aconteceram as coisas, qualquer coisa do gênero, tem que ir para o sistema de log. Então, reparem, eu nem vou terminar tudo do sistema de log e já está uma bagunça do tamanho de outro planeta, por quê?

Porque vários sistemas, conhecem vários sistemas. Vários sistemas sabem como outro sistema funciona e você começa a ficar com esse emaranhado de todo mundo se conhecer e todo mundo saber como o outro funciona e todo mundo saber qual é o próximo passo e qual é o passo anterior, de onde que veio, para onde que vai e não sei o quê, todo mundo com tudo.

Mais ainda, além do log, tem outras preocupações, outros concerns, que cortam a nossa aplicação inteira, que são Cross Cutting Concerns, o que que tem isso, por exemplo? Os dados... o Analytics, eu quero saber, como que a gente está de fraude, hoje a gente está com 10%? 10% é a nossa média histórica.

Se hoje está 20%, opa, algo está acontecendo de errado com o nosso sistema de fraude ou com os fraudadores ou a gente está com o sistema de fraude... deu uma zoada hoje e aí, está detectando 20% de fraude, quando o normal 10, tem anos que o normal é 10%, estou citando exemplos.

Então aconteceu alguma coisa hoje de estranha com o meu sistema ou realmente os fraudadores estão fazendo um ataque, alguma coisa, para tentar fraudar o meu sistema. Então, ei preciso de um Analytics, para acompanhar as métricas, para saber, tem alguma coisa fora do ar?

Tem alguma coisa que não está dando conta? Tem alguma coisa que está dando mais erro do que o comum? Então, calma aí, se esse tipo de coisa, eu preciso saber, não só para fraude, eu preciso saber também para o pagamento. A taxa de pagamento está como a taxa histórica de sucesso ou a taxa de sucesso não está bem assim?

A taxa de e-mails que são enviados com sucesso, que não são bounce, que não bate e volta, está na minha taxa normal ou está batendo e voltando mais? Então, quer dizer que os servidores de e-mail estão achando que eu sou spam e eu tenho que fazer alguma coisa? Analytics.

“Ah, os PDFs, que eu estou gerando, eu estou gerando num ritmo que eu esperava ou não, estou gerando muito mais?” Então, deu algum bug ali e entrou num looping e infinito ou não, estou gerando a menos. Então ou é vendas a menos ou o sistema está lerdo e está acumulando de PDFs serem gerados.

Analytics, tudo coisas Analytics. Então, repara, olha o fuzuê desse sistema, por que que a gente tem esse fuzuê? Porque todo mundo conhece todo mundo, não é todo mundo, mas auê, conhece o fuzuê, fica esse fuzuê total e aí, você tem que conhecer com quem você vai chamar requisição e é síncrono, só que é externo, mas se esse cara caiu, como que eu tenho que reagir com esse?

Imagina, o Analytics caiu e aí, a fraude, o que que ela faz? Ela vai para a próxima fase ou ela espera o Analytics? O que que ela faz? Como é que eu faço para, tipo: “Não, não, vai para a próxima fase e daqui a pouco, quando o Analytics subir, eu aviso o Analytics. Boa sorte, implementar isso, boa sorte, por quê?

Porque se o seu sistema de fraude cai agora, onde é que você anatou que tinha notificar o Analytics daqui um tempo? Aí é claro, você tem sistemas (poling), tem sistemas (wacher), tem sistema de (observes),.

Você começa a criar várias estruturas complexas, para tentar lidar com essa complexidade dos processos, do processo externo que a gente tem aqui dentro, que deixou de ser sequencial, passou a ser paralelo, por quê? Porque com isso a gente potencializa o desempenho da nossa aplicação.

Então, eu posso executar 10 máquinas de fraude e uma só de e-mail, mas se de repente, efetuar o pagamento também, é uma coisa que precisa de muita máquina, tenho cinco máquinas, então eu posso escalar cada um desses serviços com máquinas distintas.

Então, eu tenho essa vantagem de estar com tudo isso distribuído e paralelizado. Então, repara a bagunça que é trabalhar com esse tipo de sistema dessa forma é claro, existem sistemas e formas de trabalhar mais inteligentes ou pelo menos diferentes, que vão trazer certas vantagens nessas abordagens.

Vamos dar uma pensadinha, como a gente pode fazer isso? Eu vou copiar tudo isso, vou colar aqui em baixo, colei aqui em baixo e o que eu quero fazer agora é repensar, represar todas essas setas. Vamos repensar todas essas setas aqui. Tudo isso aqui, eu quero repensar, por quê?

Porque a ideia é que, eu não quero fazer com que eles se conheçam, eles não precisam se conhecer, por exemplo, claro, quando o meu cliente, que eu não copiei aqui, que é o meu usuário, o navegador, podia ser um aplicativo, podia ser outra coisa, que é o usuário final.

Acessa o servidor http? Claro, conhece, está fazendo uma requisição, poderia ser um aplicativo fazendo uma requisição http, o que fosse. A partir daqui, o servidor http recebeu novo pedido de compra? Ele faz o quê? Ele simplesmente envia uma mensagem que se chama: novo pedido de compra.

Então, eu tenho alguém aqui que simplesmente se chama... o meu broker, que é quem recebe mensagens. Então, eu simplesmente mando uma mensagem para o meu broker. Eu falo: “Broker, olha, toma aí, uma mensagem para você”.

E aí, quando eu mando a mensagem para o broker, eu falo: “Olha, a minha mensagem é de nova compra”, ou “Novo pedido de compra”, eu poderia definir uma outra coisa. Eu mando essa mensagem e eu não sei quem vai receber isso, eu não sei e não importa quem vai receber isso, por quê?

Porque o e-mail, que é disparado, quando tem um novo pedido de compra, ele está escutando esse tópico, ele está escurando esse assunto dessa mensagem. Então, ele está escutando qual assunto, mesmo? O assunto da nova compra, ele está escutando, mas não só ele, o fraude também está escutando isso.

O fraude também, então o fraude também está escutando isso, mas não só isso, o reservar o estoque, também está escutando esse tópico e não só isso, o Analytics também está escutando esse tópico e o log também está escutando esse tópico, todos esses estão escutando esse tópico.

O servidor http sabe alguma coisa sobre isso? Não, ele simplesmente envia uma mensagem falando: “Tenho um novo pedido de compra, aqui estão as informações”, todos esses sistemas estão escutando esse tópico, cada um fazer a sua tarefa de forma síncrona, na mesma máquina ou máquinas distribuídas, não estou nem aí.

Então, para simplificar, eu nem preciso das setas, eu só preciso dizer que esse serviço que está rodando numa máquina escuta o “Nova compra”, vou colocar assim: “Nova compra”. O fraude está escutando o “Nova compra”, o reservar o estoque está escutando o “Nova compra”.

O Analytics está escutando o “Nova compra” e o log está escutando o “Nova compra”, o que que acontece? Quando o e-mail é enviado, o que que acontece? O sistema de e-mails, o serviço de e-mail envia uma mensagem para o broker, falando o quê?

Eu vou deixar aquela setinha, para deixar claro aqui o do e-mail também, falando o quê? Ele fala, assim: “Olha, eu terminei aqui a minha parte, o que que é a minha parte? Eu enviei e-mail enviado”.

E aí, quem está escutando “e-mail enviado”? O log, está escutando “e-mail enviado” e o Analytics está escutando “e-mail enviado”, reparou? O que mais? E quando a fraude deu sucesso? Não tem fraude, o que que a gente vai querer fazer nesse instante?

Nesse instante, a gente vai querer validar o pagamento, que eu não sei, deve estar escondido aqui atrás em algum lugar. Então, eu vou copiar para cá e vou colocar: “Efetua o pagamento”. Efetua o pagamento, tem que fazer o quê? Tem que escutar o tópico de “Compra sem fraude”, certo?

Então é: “Compra sem fraude”, por exemplo, tá? Você poderia falar outra coisa, outro tópico, você define o tópico, etc., eu estou usando esse exemplo aqui agora, “Compra sem fraude”, opa, desculpa. Então, o fraude envia essa mensagem, o fraude vai enviar a mensagem e qual mensagem que ele envia mesmo?

Ele envia a mensagem: “Compra sem fraude”. Quem está escutando esse tópico? O efetua pagamento, o log e o Analytics. Então, repara aqui o que está acontecendo, da maneira que eu estou desenhando os meus sistemas, eu estou falando assim: “Olha, não me importa quem vai escutar o meu status, uma atualização de status, uma situação que ocorreu no meu sistema; ocorreu uma nova compra, um pedido de nova compra; ocorreu um e-mail enviado; ocorreu que a compra foi validada sem fraude; ocorreu que o pagamento foi efetuado com sucesso; ocorreu que o PDF foi gerado com sucesso” .

Essas coisas ocorreram, quem está escutando isso para agir, não me importa e isso é o conceito de mensageria, esse conceito de mensageria, de troca de mensagens aparece em diversos sistemas, diversas implementações. O Kafka tem algumas sacadas aqui, algumas são comuns à mensageria e o Kafka tem algumas sacadas especiais dele.

Então, uma das sacadas aqui de mensageria é: eu posso ter quantos servidores e serviços rodando de e-mail eu quiser, como eu disse antes, como funcionava antes com o próprio http, se o sistema de fraude é um sistema que consome muito a CPU e pouca memória, eu posso ter várias máquinas com CPUs potentes rodando isso.

Se o sistema de gerar PDF, consome pouca CPU, mas muita memória, eu posso ter algumas máquinas com o CPU mediano e bastante memória para elas. Então, você pode escalar de acordo com o necessário.

Além disso... Então quer ser, você tira um pontinho de falha aqui, se eu tivesse só uma máquina rodando de fraude, se caísse, eu me dava mal, se eu tenho 10 máquinas rodando, se uma cai, eu ainda tenho nove e por aí, vai, vou tirando os pontos de falha, condicionando redundância.

O broker também, você pode replicar, você não precisa ter um único broker rodando, você poder ter um cluster de brokers. Então, eu tenho um cluster com três brokers, por exemplo ou um cluster com 30 brokers, rodando 30 instâncias do Kafka, estou dando exemplo.

Então, o que acontece? Quando você envia uma mensagem, essa mensagem vai parar possivelmente em mais de um broker, por quê? Porque se um broker desligar, o outro broker ainda tem essa mensagem. Então, você começa a ter mais tolerância a falha ainda, por quê?

Porque se você manda uma mensagem, qualquer mensagem e a mensagem está armazenada em três máquinas, até ela ser recebida, até ela ser recebida por quem quiser, o que que acontece?

Se uma dessas máquinas cair, as outras duas ainda tem essa mensagem, se uma dessas máquinas pegar fogo, as outras duas ainda teme essa mensagem. Então, você ganha mais reliability, você consegue garantir que as coisas vão estar lá, serão recebidas, etc.

Mais ainda, você consegue rodar isso em paralelo, como a gente está fazendo, você consegue rodar em paralelo. Você consegue com que esses dados, os dados das mensagens que chegam, sejam distribuídos para várias instâncias do fraudador, do detector de fraude.

Então, se eu recebi cinco mensagens de novas compras, eu posso mandar dois para um, dois para o outro, um para o outro, por exemplo, se eu tenho três instâncias de fraudador, de detecção de fraude.

Eu posso fazer com que se os... Automaticamente, na verdade, se os sistema de (fraudadores) aqui caírem e voltarem só amanhã, porque deu um pau aqui no meu sistema, só voltou amanhã, não tem problema, as mensagens estão armazenadas aqui e eu consigo executar elas um dia depois.

Não tem problema, se caiu, “Falhou”, aquelas minhas 10 máquinas caíram por algum motivo e eu não estou conseguindo levantar, eu consigo armazenar essa mensagem por um tempo que eu configuro, posso configurar um tempo ou quantidade de espaço em disco, que eu quero gravar as mensagens, sem problemas.

Eu posso também falar: “Olha, pensa bem, se a compra de um usuário foi fraude, eu não quero executar as outras desse usuário”, poderia ser uma definição do sistema, pode ser.

Então, o que que você pode fazer? Você pode no Kafka, por mais que a gente execute em parelho, eu estou falando que eu posso executar tudo em paralelo... Você pode em determinados momentos falar: “Olha, as compras para determinado usuário, na mensagem de gerar PDF, eu quero que gerem em sequência”, por quê?

Porque se um usuário comprou mil PDFs, eu não quero que fique gerando os mil PDFs daquele usuário e a galera fique esperando, eu quero ir gerando um PDF para cada um, assim eu consigo... A pessoa não vai ler os mil de uma vez, então todo mundo está lendo alguma coisa, por exemplo.

Então, você poderia definir regras do gênero, de que olha: “Apesar de que eu quero paralelizável, quando eu penso em um usuário, eu quero que os daquele usuário execute sequencial”.

Por exemplo, o estoque, eu reservar o estoque, eu posso executar em paralelo, mas para um produto específico, provavelmente, eu quero reservar o estoque sequencial, eu quero tirar de lá de dentro o do estoque reservado, o estoque em sequência para o produto cinco.

Mas, para o produto cinco e para o produto 15, eu posso executar em duas máquinas, em paralelo, não estou nem aí. Então, eu posso usar o produto como chave para serializar a execução, deixar em sequência.


