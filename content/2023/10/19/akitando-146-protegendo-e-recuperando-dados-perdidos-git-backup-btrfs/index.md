---
title: "[Akitando] #146 - Protegendo e Recuperando Dados Perdidos - Git, Backup, BTRFS"
date: '2023-10-19T10:00:00-03:00'
slug: akitando-146-protegendo-e-recuperando-dados-perdidos-git-backup-btrfs
tags:
- git
- docker
- btrfs
- zfs
- synology
- manjaro
- ubuntu
- akitando
draft: false
---

{{< youtube id="yfEhz9poqW8" >}}

Fazia tempo que queria fazer um último episódio à playlist de armazenamento trazendo um pouco mais de BTRFS. Aproveito pra adicionar mais sobre Git, Docker tudo voltado ao tema de como não perder dados nunca mais e até como recuperar dados numa emergência.

Tudo que você nem sabia que queria saber sobre armazenamento e gerenciamento de dados no seu HD.

## Conteúdo

* 00:00:00 - Intro
* 00:00:51 - CAP 1 - Recapitulando Sistemas de Arquivos - Journaling
* 00:02:42 - CAP 2 - Caminho pro Desastre! Git e GitHub
* 00:07:21 - CAP 3 - Git SEM GitHub - Git Raíz
* 00:12:25 - CAP 4 - Partição Perdida - Sem Superbloco
* 00:16:09 - CAP 5 - Não Caia em Golpes - Aliexpress
* 00:19:46 - CAP 6 - Tentando Consertar - Backup do Superbloco
* 00:24:51 - CAP 7 - Tentando Consertar - Última Instância
* 00:29:36 - CAP 8 - Checksums - Sistemas de Arquivos Modernos
* 00:34:18 - CAP 9 - Deduplicação - Sistemas de Arquivos Modernos
* 00:36:28 - CAP 10 - Copy On Write - Sistemas de Arquivos Modernos
* 00:40:21 - CAP 11 - BTRFS no Ubuntu - Por que Manjaro?
* 00:45:51 - CAP 12 - Snapshots e Timeshift - BTRFS
* 00:53:56 - CAP 13 - Snapshots e Docker - Não Perca Espaço
* 01:02:59 - CAP 14 - Meu NAS Anti Ransomware - Synology
* 01:07:37 - CAP 15 - Pacotes, Docker, Flatpak, QEMU - Sistema Estável
* 01:10:03 - Bloopers
## Links

* A Quick and Hacky Way to Serve a Git Repo over HTTP (https://theartofmachinery.com/2016/07/02/git_over_http.html)
* NGrok (https://ngrok.com/docs/getting-started/)
* TestDisk LiveCD (https://www.cgsecurity.org/wiki/TestDisk_Livecd)
* TestDisk Docs (https://www.cgsecurity.org/testdisk_doc/presentation.html#testdisk-partition-recovery)
* ArchWiki: BTRFS (https://wiki.archlinux.org/title/Btrfs)
* Timeshift and grub-btrfs in Ubuntu (https://www.lorenzobettini.it/2022/10/timeshift-and-grub-btrfs-in-ubuntu/)
* timeshift-autosnap-apt (https://github.com/wmutschl/timeshift-autosnap-apt)
* Scheduled snapshots not working (https://github.com/teejee2008/timeshift/issues/135)
* Why is Grub menu not shown when starting my computer? (https://askubuntu.com/a/1187104)
* Full-disk encryption with Btrfs on Ubuntu Xenial (https://albertodonato.net/blog/posts/full-disk-encryption-with-btrfs-on-ubuntu-xenial.html)
* How To Add Swap Space on Ubuntu 20.04 (https://www.digitalocean.com/community/tutorials/how-to-add-swap-space-on-ubuntu-20-04)
* Swapfile Swapon invalid argument (https://unix.stackexchange.com/a/713929)
* Thoughts on swap partition vs swap file in subvolume with Btrfs? (https://www.reddit.com/r/archlinux/comments/ymq7ve/thoughts_on_swap_partition_vs_swap_file_in/?onetap_auto=true)
* How do I use Timeshift/btrfs to permanently restore to a previous snapshot? (https://forum.manjaro.org/t/how-do-i-use-timeshift-btrfs-to-permanently-restore-to-a-previous-snapshot/124683)

## SCRIPT 

Olá pessoal, Fabio Akita



Estou bem atrasado com a segunda parte do video da Rinha, e vai demorar mais um pouco. Honestamente, o tema me deu burn out, então resolvi fazer um video antes que não deixa de ser um pré-requisito porque até o final vou explicar um pouco mais sobre Docker e Docker Compose. Mas antes, quero falar de um problema que vira e mexe vejo desenvolvedores tendo: perder dados.




Sim, acidentes acontecem, e tanto o hardware quanto sistemas operacionais de hoje em dia, ajudam muito a não perder dados. Mas sério, a maioria de vocês não ia conseguir sobreviver com a tecnologia rudimentar que a gente tinha nos anos 80 e 90. Eu mesmo não lembro mais a última vez que perdi dados. Acho um absurdo que programadores ainda percam tempo com esse tipo de problema, então quero discutir diversas soluções pra isso. Vamos lá.




(...)




Um bom programador é mestre da sua máquina, nunca o contrário. Quando seu hardware perde seus dados e te força a passar perrengue, ele se tornou seu mestre, e você não é mais que um mero peão de um objeto inanimado. Vocês tem que mostrar quem manda. Vamos a alguns fatos. Em termos de sistema operacional, tanto faz se usa Mac, ou Windows ou Linux. Nenhum deles é muito melhor que o outro em termos de garantia de dados. Não é porque escolheu usar Mac que está magicamente mais protegido do que Windows. Isso não é verdade faz anos.







Vejam minha série sobre armazenamento. O NTFS do Windows tá longe de ser perfeito, cheio de problemas ainda, mas pro dia a dia, você não vai ter mais problemas do que teria com um ext4 no Linux ou APFS no Mac. Todos tem journaling, por exemplo. Significa que, mesmo que acabe a luz no meio de uma operação de disco, seu sistema de arquivos não vai ficar corrompido a toa. Vira e mexe alguns blocos podem se perder mesmo, mas as rotinas de manutenção padrão, são suficientes pra resolver a maioria dos problemas.






Quando alguém perde dados só existem duas razões: porque comprou um HD muito do porcaria ou porque cometeu erro humano mesmo. Aliás, vira e mexe alguém faz aquele meme de digitar "rm -rf /" e diz que iniciante não sabe o que isso faz. Mas parece que quem faz o meme também não sabe. Já faz algum tempo que o comando "rm" não apaga a raíz assim do nada. Precisa ou digitar "/*" com asterisco no final ou usar a opção "--no-preserve-root" pra realmente apagar a raíz. The more you know.






Enfim, nenhum sistema operacional vai resolver qualquer um desses problemas. Hardware porcaria só tem uma solução: jogar fora e comprar um de verdade. Toda vez que estiver tendo problemas frequentes, a probabilidade maior é hardware porcaria. Já a parte do erro humano, vamos tentar mitigar um pouco disso hoje.






Alguns meses atrás, um conhecido foi tentar instalar Linux como dual boot com Windows no notebook dele. Não sei qual era a marca, nem nada. Mas ele se ferrou: a partição Linux não bootava mais. Agora vem o problema: as últimas coisas que ele fez no projeto dele, não tinha dado push pro GitHub ainda. E, como praticamente todo mundo aqui assistindo, não tinha backup.






Tudo que vou narrar a partir daqui poderia ter sido evitado. Hoje em dia, todo programador usa Git. Isso é obrigatório. Mesmo se trabalha numa empresa, o mais porcaria possível, que por alguma razão do além, ainda não está usando Git, não existe desculpa: o projeto não precisa adotar Git pra você usar Git. GitHub é opcional, Git não. Marrete esse conceito na sua cabeça. Não importa se é um projetinho pessoal só na sua máquina. Faça "git init" e comece a fazer "git commit". 






Git não precisa de um servidor externo. Digamos que essa empresa hipotética super tosca pra onde está trabalhando não queira usar Git pra não ter que pagar GitHub ou alguma bobagem desse tipo. Ou usa alguma alternativa obsoleta de 20 anos atrás como a enorme porcaria de um SourceSafe da vida. Aí são dois grandes erros imperdoáveis. E digamos que você também não queira investir numa conta paga de GitHub, portanto não tem como fazer repositórios privados. E agora? Como faz?






Vou repetir: não precisa existir projeto no GitHub pra usar Git. Muitos iniciantes confundem isso. Git é uma ferramenta, GitHub é um serviço. GitHub precisa de Git, mas Git não precisa de GitHub. Qualquer diretório do seu computador pode virar um repositório Git, basta digitar "git init" dentro e pronto, ele cria o subdiretório ".git" que é o repositório propriamente dito. Agora basta fazer "git add ." pra adicionar tudo nesse diretório e "git commit -m" com uma mensagem pra criar um commit e já era. Só isso.






Eu fiz dois videos onde conto tudo que você deveria saber sobre Git e recomendo que assistam. Inclusive eu conto como lá nos primórdios de Git, quando realmente ninguém nem sabia que existia, eu trabalhei em projetos de clientes que usavam coisas como Subversion, mas só eu trabalhava com Git e usava um plugin chamado git-svn pra integrar os dois. Se lá em 2007 eu conseguia usar, sem nenhum suporte, em 2023, não existe desculpa aceitável pra não usar.






Isso resolve o problema de acidentalmente apagar um arquivo que não deveria, ou mudar de ideia num código que acabou de fazer, e conseguir voltar pra como estava no último commit. Digamos que você apagou um arquivo. Puts, apagou o arquivo errado? E agora? Se você fez a coisa certa, que é ficar fazendo pequenos commits, o tempo todo, ainda deve ter a versão anterior no último commit. Faça "git status", olha ele marcado pra deleção. Basta fazer "git checkout" e o nome do arquivo, e pronto, listamos e olha só o arquivo de volta aqui. 






Qualquer programador que use de desculpa "puts, apaguei o arquivo sem querer e perdi tudo", ainda é um iniciante que precisa aprender a usar git. Ah, mas eu gosto de primeiro terminar tudo, tudo e depois fazer commit. E eu digo que você é um idiota. Vou dar um exemplo. Digamos que fiz um código aqui num arquivo, mas não terminei. E acabou o dia. Como sou disciplinado, já vou fazer um "git commit -m 'codigo no meio'". Puts, mas que commit feio. Melhor um commit feio agora do que ter um acidente e perder o arquivo. Agora posso desligar o computador e ir dormir.






Acordo no dia seguinte, vou continuar editando esse arquivo e aí sim, finalmente termino. Agora posso fazer um "git commit -m 'codigo final' --amend". Essa opção amend, vai desfazer o commit anterior e trocar por esse novo commit, com a nova mensagem mais bonita e descritiva. Rode "git log" olha só, aquela mensagem feia com código parcial sumiu e estou com o commit bonito. Agora posso fazer push pro servidor se quiser. 






Dentro de um branch, faça quanto commits quiser, dê push de commits feios, e no final, faça um squash do branch antes de criar um pull request, se fizer muita questão. É uma das formas de lidar com isso. O histórico de commits nunca deve ser reescrito no branch master ou main, mas no seu branch, onde só você está trabalhando, foda-se, faça cinquenta commits. Daqui uma semana quando terminar, faça squash, se a quantidade de commits incomodar. Daí manda merge pra master.







Existem várias nuances no que falei, não é uma regra escrita em pedra, essas convenções vão variar de equipe pra equipe. Mas voltando ao meu ponto inicial: digamos que você não tenha um repositório remoto no GitHub. Estou trabalhando sozinho na minha máquina local. E não tenho pra onde dar push. Se der alguma pane catastrófica na minha máquina, eu perco tudo. Como evitar isso?






Se o projeto que estiver trabalhando for de cliente, e você for freelancer, por favor, pague uma conta no GitHub ou GitLab e faça push pra lá. Não desperdice o tempo de todo mundo. Isso tem nome: se chama economia porca. Porque na hora que alguma coisa der pane, não tem mais como recuperar. Isso dito, você é mão de vaca bagarai. Ok. Vou te dar a solução mais barata de todos os tempos. Espete um pendrive ou HD externo no seu PC.






Do diretório do seu projetinho faça "git clone --bare . /media/pendrive/projeto.git". Isso vai criar um clone do seu projeto nesse pendrive. Lógico o caminho de onde o pendrive montou vai variar de distro pra distro. Adicione o pendrive como origin com "git remote add origin /media/pendrive/projeto.git". Pronto. Vamos continuar trabalhando, criando um novo arquivo, adicionando, fazendo commit. Mas agora posso fazer o bom e velho "git push origin master" e ele vai mandar praquele clone no pendrive. O pendrive está fazendo o papel de GitHub pra você, sacou?







E podemos ir mais longe. Digamos que é aquela sexta feira no escritório, você está finalizando uma nova funcionalidade que precisa entregar hoje. O Tech lead tá lá esperando pra finalizar a parte dele. Mas aí dá uma daquelas panes no GitHub e ele fica fora do ar algumas horas. Bem no fim da sexta feira. Fodeu né? Fica pra segunda então? Não. Faça git push no pendrive, passa o pendrive pro tech lead, ele consegue adicionar como remote e dar pull no repo dele. E vai conseguir continuar o trabalho na máquina dele, mesmo sem ter GitHub.






GitHub é uma conveniência, mas não é mandatório. Qualquer coisa pode substituir. O que você perde enquanto ele tá fora do ar são as conveniências, a integração com o continuous integration, os pull requests que tavam em aberto e você não puxou, lista de issues e coisas assim, mas o código em si, dá pra manipular de vários jeitos diferentes.






Só pra concluir essa parte de Git, vou mais longe no exemplo do GitHub saindo fora do ar. Meu exemplo com pendrive assume todo mundo trabalhando presencialmente no mesmo escritório. Mas que cabeça a minha, todo mundo tá home office hoje né? Então fodeu mesmo, sem GitHub pra centralizar, se cair, todo mundo fica sem conseguir colaborar né? Só que não. 






Eu terminei a minha parte na minha máquina, o tech lead tá lá na casa dele, precisando puxar o que acabei de fazer pra continuar. Não temos GitHub. Como faz? Simples. Sabe o clone "bare" que eu fiz no tal pendrive? Vamos modificar ele. Basta entrar no diretório ".git" e começar fazendo "git --bare update-server-info" e depois "mv hooks/post-update.sample hooks/post-update" e pronto, nosso repositório está pronto pra ser integrado a um servidor web qualquer. O mais simples que consigo imaginar é o http server que vem no Python. Com uma única linha de comando "python -m http.server 8000" e já era, temos um servidor web git.







Pra testar, basta ir no "/tmp" ou qualquer lugar e fazer "git clone http://localhost:8000 teste" e voilá, consigo fazer clone do meu repositório local. Quem trabalha com VPNs como TailScale ou ZeroTier já sabe o que fazer: só trocar localhost pelo IP da VPN que todo mundo da equipe enxerga e fazer o mesmo que descrevi antes: o tech lead adiciona um remote apontando pra esse servidor, consegue dar "git fetch" do seu branch e pode continuar a trabalhar tranquilamente enquanto povo do GitHub fica lá brincando de restartar servidor até voltar.






Outra solução rápida é instalar ngrok na sua máquina. Ngrok é o jeito mais simples de criar um túnel de fora pra dentro da sua máquina. Muita gente usa pra mostrar uma aplicação web rodando na sua máquina local que ainda não tem servidor de testes ou staging. Assim um cliente que não está presente consegue visualizar o andamento do trabalho olhando o que tá rodando no localhost da sua máquina. Entenderam?






Olha só, posso rodar "ngrok http 8000". Isso vai abrir um tunel no serviço ngrok apontando pro nosso servidorzinho python local. Ele nos dá essa URL gigante de "Forwarding". Podemos passar ela pro nosso querido tech lead. Na máquina dele, ele pode fazer "git clone " e essa URL-zona e ... voilá, olha só. Da casa dele, tá conseguindo puxar os commits da minha máquina. Entenderam o que aconteceu aqui?







Num dia normal, o trabalho iria parar por horas até o GitHub voltar. Mas sabendo dessas coisas simples e triviais, conseguimos continuar trabalhando como se nada tivesse acontecido. Esse é o poder do Git e de saber como juntar componentes simples. Criar um clone "bare", habilitar  pra ser servido via http, subir um servidor http simples com python e abrir um túnel seguro usando ngrok.






Mas claro, tudo isso assume que você, no mínimo, usa Git minimamente direito. Fazendo commits frequentes. Fazendo backups frequentes. Só quem já perdeu dados sabe da importância disso. Todos que dizem que é desnecessário só provam que são virgens e ainda precisam perder alguma coisa importante pra aprender. Vocês assistindo podem aproveitar o que falei e evitar ter que passar por esse estresse.







Digamos que não fez nada disso. Deu na telha que quer experimentar montar um dual boot na sua máquina pela primeira vez, e gosta de viver perigosamente, nem deu push dos projetos pro GitHub, nem copiou num pendrive, nem sequer fez commits, tá tudo parcial na sua máquina. Mas tá seguro, você assistiu um tutorial no YouTube, seus parças tão te chamando pra jogar Genshin a noite. Não tem tempo a perder, bora redimensionar o disco, criar uma segunda partição de Windows e passar a noite jogando.






Só que aí dá tudo errado. Tudo na primeira vez, sempre vai dar errado. Aceitem essa máxima. Se for a primeira vez de qualquer coisa, já comecem sabendo que não vai funcionar de primeira. Não custa nada espetar um HD externo e copiar as coisas mais importantes lá. Mas alas, a pressa é inimiga da perfeição mesmo. Não lembro onde guardei o HD externo, não quero perder tempo procurando. Foda-se, vai dar certo, confia, cola no pai.







É muito difícil saber exatamente o que acontece em panes. Se foi algum procedimento errado ou se foi mesmo algum defeito de hardware. Pra tentar demonstrar algo parecido, fiz uma máquina virtual com um Ubuntu e um disco virtual. Esse disco aparece como dispositivo "/dev/vdb" de virtual device B, já que o virtual device A é disco primário. Está formatado como ext4, dentro coloquei meu projeto da rinha em rails, um ISO de Ubuntu. Faça de conta que é um HD normal do seu notebook.






Agora vou desmontar com "umount". Um dos jeito de simular um erro de hardware é escrever por cima dos primeiros bytes no começo do dispositivo. Lembram dos meus videos sobre sistemas de arquivo? O que é uma partição? É uma estrutura de dados que guarda coisas como em qual endereço começa e onde termina uma partição, o tipo de sistema de arquivo, localização dos blocos. Esses primeiros bytes formam o que chamamos de Super Bloco. 






Olha só, se eu tentar montar esse dispositivo de novo, vai dar erro. O Linux não sabe o que fazer, porque não tem mais o superbloco. Os dados em si ainda estão na partição, no linguição de bytes, mas sem o superbloco não dá pra saber onde começa e onde termina o que. Pense assim, seus arquivos são como casas montados com lego. Cada lego está enterrado num campo de futebol, o seu HD. Mas o superbloco tem a localização de cada bloco, e as instruções de como remontar sua casa de lego. Sem esse superbloco, mesmo achando as peças, não sabemos se ainda falta mais blocos ou como encaixar esses blocos.






Se acontecer um erro como esse, onde a partição se recusa a bootar ou montar e aparecem erros como esse, filesystem errado, ou opção errada, ou superbloco errado, é um péssimo sinal. A primeira coisa que recomendo fazer é evitar insistir. Se usar comandos errados que sobrescrevem em cima do disco, se fodeu, vai perder dados. Muito cuidado com comandos como fdisk ou mkfs, que é o equivalente de format de Linux.






A forma de diagnosticar algo assim é achar outro computador, outro notebook, baixar a ISO de algum Linux como o próprio Ubuntu, gravar num pendrive, daí passar a fazer boot pelo pendrive. A maioria das distros Linux tem um ambiente de Live CD que boota um ambiente operacional inteiro, que inclusive se conecta no seu Wifi e permite instalar qualquer pacote. Isso permite baixar quaisquer ferramentas de diagnóstico e recuperação.






A partir desse ambiente, ele vai achar seu dispositivo como um "/dev/sdb" ou "/dev/nvme2" ou qualquer coisa assim. Assistam meus videos sobre Linux, o de Gentoo principalmente, porque a instalação dele exige manualmente fazer exatamente esse tipo de operação. Por isso fiz aquele video: qualquer um que já instalou um Gentoo ou Arch do zero, já sabe como fazer isso. 






Antes de continuar deixa eu comentar uma coisa: porque esse tipo de problema acontece? Digamos que não foi erro humano. SSDs quebram assim do nada mesmo? E sim, quebram. Se for no Aliexpress e pesquisar por SSDs e ordenar a partir do menor preço, sim, as primeiras dez páginas, no mínimo, é quase 100% de chance de vir um tijolo. Aliás, se vier um tijolo pelo menos vai ser mais seguro. Problema é quando vem um drive mesmo e você confia com seus dados.






Primeiro, tem os golpes mais óbvios. Quando desparafusa e abre, dentro não tem uma placa controladora e chips de memória Flash. Não, tem um adaptador USB barato pra micro SD Card. Chips de SD Card sempre usam componentes mais baratos que SSD. Eles foram feitos pra serem quase descartáveis, justamente pra uso em câmeras fotográficas ou celulares. Não foram pensados pra uso como um HD de notebook. Não tem banda pra isso, não tem controlador pra isso, não tem memória RAM de cache, a qualidade da memória NAND flash é muito inferior. Por isso são muito mais baratos.







Já disse isso antes e vou repetir: nunca deixe nada importante num SD Card. É comum eles corromperem. SD Card deve ser usado única e exclusivamente como armazenamento temporário, tempo suficiente pra chegar em casa da sua viagem, e descarregar os cartões no seu notebook. Mas os tais SSDs baratos de marcas chinesas genéricas contam com sua burrice. Ninguém nunca abre a carcaça de um SSD pra ver o que tem dentro, e eles contam com isso.







Mesmo quando vem com componentes de SSD de verdade, eles vendem chips de 500MB como se fosse 1TB. Por isso sai pela metade do preço, mas o controlador tá gambiarrado e mente pro seu computador dizendo que tem 1TB. Se você tentar mesmo gravar mais que 500MB, ele vai começar a sobrescrever os dados do começo. Como a maioria das pessoas vai só gravando mas não checa na hora se tudo gravou mesmo, só vai descobrir da pior maneira: quando precisar do arquivo, tentar acessar, e descobrir que os arquivos não estão mais lá ou estão incompletos e corrompidos.







Como evitar isso? Nunca, jamais compre marcas chinesas genéricas desconhecidas. Compre Samsung, Crucial, Sandisk, Western Digital, só marcas reconhecidas de longa data. E de lojas com alguma reputação e não um revendedor aleatório desconhecido. Muitos vão pegar a carcaça de um Samsung quebrado, colocar o tal adaptador USB com SD card e te revender como se fosse original Samsung. Só compre em loja de verdade e só compre item novo e lacrado. Pense em SSD como escova de dente: você não quer um usado.







Em quais situações você pode arriscar um SSD mais barato genérico? Se for pra ser sua biblioteca de Steam, por exemplo. HD pra games. Aí foda-se se perder dados. Se o HD for mesmo um golpe, pega outro HD barato, e baixa tudo da Steam de novo. Mas nunca, jamais, sob nenhuma circunstância use um HD desses pra trabalho, ou mesmo pra guardar coisas importantes pra você, como as fotos da sua família, dos seus filhos, das suas viagens. Você vai perder esses dados pra sempre.







Obviamente, todo mundo concordou com o que falei, mas tá fazendo exatamente o que eu disse pra não fazer. Não usou Git. Não fez uma cópia dos seus projetos num pendrive ou hd externo. Foi querer usar o mesmo HD de trabalho pra games com dual boot. E chegamos na situação que ele não boota mais e nem conseguimos mais montar a partição. O próximo passo é arranjar um outro HD, seja interno, seja externo, que tenha mais espaço livre do que o total do HD quebrado. Vamos fazer uma cópia bit a bit, pra garantir que não vamos perder mais nada.







Usando o comando "lsblk" podemos ver os dispositivos de armazenamento conectados. Aqui neste exemplo, tenho o "/dev/vba" que é a partição funcionando do meu Ubuntu, e o "/dev/vdb" que é a partição corrompida. Antes de tentar fazer qualquer coisa, o certo é usar o comando "dd" e fazer uma cópia, assim "sudo dd if=/dev/vdb of=~/Downloads/backup.hd bs=4M". O nome de arquivo e extensão não importam, qualquer coisa serve. Dependendo do tamanho do seu HD quebrado e velocidade do seu PC, isso pode levar horas. Vai parecer que está travado. Deixa quieto lá, vai jantar e volta amanhã. O LOLzinho com os parça essa noite já miou, nem insiste.






Pronto, agora temos uma cópia bit a bit do HD nesse arquivo. Garanta que não vai perder ele, porque se fizer mais alguma coisa errada na tentativa de consertar, temos uma segunda chance com esse backup. Coloque num outro HD externo se quiser garantir. Mantenha longe de você por enquanto, você já causou problemas demais. Um profissional de recuperação de dados tem muito mais opções do que vou mostrar agora. Só vou falar o mais óbvio, porque eu também não sei tudo.






Felizmente muita gente já passou por situação similar, e a partição costuma ter backups desse superbloco. Mais de um na real. Faça o teste no seu próprio sistema. Num Arch linux, primeiro precisamos instalar o pacote "e2fsprogs", que contém programas pra lidar com sistema de arquivos como ext2 ou ext4. Usando o comando "lsblk" podemos listar os dispositivos e com o comando "dumpe2fs /dev/vdb | grep -i superblock" podemos listar os tais superblocos e backups.






Como esperado, o superbloco primário está no endereço zero, mas veja quantos backups temos. Um no endereço 32 mil e tantos, outro no endereço 98 mil e tantos, oito backups. Um erro catastrófico é quando alguma coisa acontece que corrompe não só os primeiros bytes, mas todos os bytes até o último backup. É raro, mas pode acontecer, especialmente se você usar errado uma ferramenta como fdisk e sobrescrever uma nova partição em cima da antiga. 







O comando "dumpe2fs" talvez não consiga listar esses superblocos caso a partição esteja corrompida. Tentando no dispositivo "/dev/vdb" olha só, ele dá erro e não lista nada. Podemos usar o comando "mke2fs" que normalmente serve pra formatar uma partição, mas passando a opção "-n" de "dry-run" vai só simular a execução do comando sem de fato executar nada. Lembra o que falei antes: não queremos que nada escreva nesse disco quebrado. O programa não está consultando a partição com problema, só dizendo que, se não fosse um dry-run, escreveria os superblocos nesses endereços que listou.







Pra facilitar recuperação é importante saber qual é o tipo de sistema de arquivos que escolhemos na instalação. No caso de Windows é fácil, acho que ninguém escolhe nada diferente de NTFS. Mac já complica porque antigamente tinha o HPFS+ que mudou pra APFS a partir do final de 2017 com o macOS 10.13, mas muita gente continuou usando o antigo até depois de 2018. Já Linux, maioria costuma escolher ext4. Tem gente que escolhe ReiserFS ou os novos ZFS ou BTRFS. Cada um deles tem ferramental diferente. O que estou mostrando até agora é assumindo que é ext4.






Agora podemos tentar recuperar o superbloco a partir dos backups. Temos que tentar um a um até conseguir. Talvez nenhum dos backups estejam bons, talvez já no primeiro resolva. Pra fazer essa recuperação usamos o comando "fsck" com opção "-b" e o número do backup. Agora é ir confirmando "yes" até o fim. E se continuar assim, yes, yes, olha só, terminou. Será que funcionou? 






Pra saber, podemos tentar montar de novo. E sucesso, montou de primeira, sem nenhum erro. Se listarmos dentro, sim, aqui estão nossos arquivos. À primeira vista tudo intacto. Nem sempre as coisas acontecem assim bonitinho, lembrem-se que eu só simulei um problema num ambiente controlado. Sob condições ideais de temperatura e pressão, é assim que conseguimos recuperar.






O certo agora é não confiar que só porque parece tudo ok, parar aqui. O certo é plugar outro HD externo, e usar um programa como rsync pra fazer uma cópia dos arquivos pra outro lugar. Você quis economizar com HD barato, mas tá percebendo que pra recuperar agora vai precisar de vários outros HDs pra fazer o backup que não quis fazer antes? Depois de tudo copiado, o certo é formatar essa partição do zero e copiar os arquivos de volta. Mas o mais certo mesmo é, depois de copiar os arquivos pra um lugar seguro, se livrar desse HD. 






Se foi problema de hardware e não erro humano, é provável que vai voltar a dar pau de novo. Nunca confie num hardware que já te deixou na mão uma vez. É lixo, joga fora. Se o cavalo te derruba, você sacrifica, é o que eu sempre digo. No mínimo troca por um SSD novo, de verdade, de uma Crucial ou Samsung da vida, e usa esse HD que deu pau num estojo externo pra ser HD USB só pra coisas não importantes como games da Steam.








Mas, digamos que o comando "fsck" não funcionou, nenhum dos backups estava bom e não deu pra recuperar o superbloco. A partir daqui depende da importância dos dados no seu HD. Faz de conta que é daqueles casos excepcionais onde sua carteira de Bitcoin com milhões de dólares está só nesse HD. Um puta erro de amador, mas já vimos isso acontecer antes. Nesse caso o melhor é procurar um profissional de recuperação de dados. Pra recuperações mais hardcore, ele vai desmontar o SSD, colocar num hardware especializado pra esse tipo de coisa e vai fazer magia negra. Dependendo da pane, talvez consiga recuperar.






Se não for esse caso, aí recomendo que instale a ferramenta TestDisk. Eu não tenho tanta experiência nisso, então recomendo que procure outros videos como este daqui. Vou deixar o link abaixo. O TestDisk propriamente dito tem algumas ferramentas de análise pra tentar recuperar as informações da partição, reconstruir o que conseguir, mas não tem nenhuma garantia que vai funcionar. Ele não é muito intuitivo de usar, por isso recomendo ler bem a documentação e assistir alguns videos de exemplo.






Seja por ter corrompido a partição ou seja porque você deletou arquivos acidentalmente, o caso é o mesmo: os bytes que compõe seu arquivo nunca são deletados de verdade. Eles ainda estão fisicamente no HD. Acontece o seguinte: o tal sistema de arquivos, como ext4, mantém um índice dizendo: arquivo chamado "hello.txt" tem 20 blocos, e os endereços desses blocos são X e Y. Quando pedimos esse arquivo, ele vai buscar esses blocos e nos devolve na ordem certa. Blocos são conjuntos de bytes, depende do sistema de arquivos dizer quantos bytes tem num bloco. Por isso falo que é tudo um linguição de bytes.







Quando mandamos apagar um arquivo, não vai bloco a bloco e escreve zeros em cima. Isso até existe, alguns sistemas chamam de "Deleção segura". Só apaga o registro do arquivo "hello.txt" desse índice, daí perdemos a localização dos blocos. Mas os blocos em si continuam no HD. Lembram a metáfora dos legos enterrados no campo de futebol. Eles continuam lá enterrados, só jogamos fora o mapa da localização de cada peça. 






Os blocos só vão se perder se algum novo arquivo escrever por cima deles. Por isso falamos que, se apagou algo por engano, a primeiríssima coisa a se fazer é desmontar esse HD e tirar da máquina, pra nenhum processo acabar gravando nada em cima por acidente. E por isso a segunda coisa é fazer um backup bit a bit com a ferramenta DD, porque isso vai preservar todos os blocos, exatamente como eles estavam até aquele momento. Daí podemos fuçar sem perigo de perder alguma coisa, porque tem o backup, a imagem exata.







No pacote do TestDisk existe a ferramenta Photorec. Também já expliquei em outro video a diferença de arquivos texto e arquivos binários, e expliquei como o sistema operacional sabe que um determinado binário é um executável. Todo arquivo binário, tem nos primeiros bytes, um cabeçalho que indica que tipo de arquivo é. Um JPEG, um PNG, um video MP4, um executável ELF de Linux, um documento de Word, todos tem cabeçalhos padrão. 






Na metáfora dos Legos, é como ir desenterrando peça a peça, sabendo que um JPEG começa com um bloco vermelho, um Word começa com bloco azul, e aí tentar montar na tentativa e erro. Uma ferramenta como Photorec vai tentar, por tentativa e erro, localizar o começo dos arquivos e os blocos que fazem sentido vir depois.







Eu simulei isso nesse dispositivo "/dev/vdb", fazendo de conta que o fsck não conseguiu recuperar. Rodando o photorec, ele vai pedir pra apontar um diretório em outra partição boa, pra onde queremos que vá gravando os arquivos que conseguir encontrar. Dependendo da quantidade de arquivos que tinha, vai demorar um bocado. No final vai surgir um diretório com um monte de arquivos com nomes esquisitos. É o máximo que podemos fazer se não tiver como recuperar o superbloco. 






Olha só, tentando abrir alguns desses arquivos, de fato o conteúdo faz algum sentido. Mas claramente alguns estão incompletos. E vai vir muito lixo junto. Nesse ponto assuma que você se fodeu, perdeu a maior parte, e qualquer resquício que conseguir recuperar já é lucro. Entendeu? Se você perder o superbloco, perdeu os metadados, diretórios, nomes de arquivos, tudo. Nesse estágio estamos escovando bit mesmo.






Como não chegar nesse ponto? Só existe uma maneira: faça backup. É a recomendação mais velha da computação que ninguém segue, e eu nem tenho esperanças que vocês assistindo vão seguir, mas a gente tenta. E é a economia mais porca de todas não fazer backup. Um SSD Sandisk de 1TB, no MercadoLivre, custa uns 300 reais. Sandisk não é e melhor marca, mas é mil vezes melhor que um chinês genérico no fim do Aliexpress. Um case USB que transforma esse SSD num drive externo, não custa 30 reais. Uma balada paga isso. 






Todo sistema operacional já vem com software de backup. Nem precisa instalar nada. Use o Time Machine do MacOS, use o app de Backup que já vem no Windows, use um app como Deja Vu que já vem nos Ubuntu. Não seja preguiçoso. E backup não é só pro caso de dar pane de hardware no seu SSD barato, tem caso pior. Já ouviu falar de ransomware? Vou contar como eu sou imune a isso.






Se num extremo está a pessoa que confia todos os seus dados importantes num SSD genérico chinês do fundo do Aliexpress, sem nenhum backup, do outro lado estou eu. Já mostrei minha solução em outros videos e no Insta, mas eu tenho um NAS de 60 terabytes. São 6 HDs IronWolf de nível industrial de 12 terabytes cada, em sistema de redundância, onde não perco nada mesmo se um HD morrer agora. Eu uso não só pra backup, mas faço coisas como edição dos meus videos direto dele.







Claro que não dá pra recomendar isso, é uma solução de mais de 30 mil reais de investimento, na época. Como preços pioraram, agora, é inviável. Minha recomendação é um HD externo USB, coisa de menos de 400 reais, e que vai evitar muita dor de cabeça. Qualquer coisa acima é luxo. O mínimo é suficiente, faça o que puder, só não faça nada.







Quero aproveitar pra falar de outra coisa que não falei na série de armazenamento: sistemas de arquivos modernos. Lembrando, sistemas de arquivos como os antigos FAT16 e seus derivados como FAT32, ou mesmo ext2 nos primeiros Linux, foram pensados numa época onde espaço em disco era caro. RAM era caro. CPU era caro. Não havia recursos pra desperdiçar. Era a época onde falo que a gente escovava bit, no sentido que cada bit extra fazia diferença.






Segurança e performance são trade offs. Quanto mais segurança quiser, menos conveniente as coisas vão ser e mais recursos vamos precisar. Quando gravamos um arquivo num HD, o sistema de arquivos divide os bytes em blocos e grava nos setores do disco. Esse arquivo fica lá, semanas, meses, às vezes anos, sem ser acessado. Um belo dia você resolve abrir esse arquivo. Digamos que é uma planilha. Como eu sei que um dos setores físicos do disco magnético não falhou, flipou um bit, e algum dos valores na planilha não estão errados?







Num sistema antigo como FAT16, não tem como saber. Se corromper, corrompeu e não vamos saber. Num sistema moderno existe checksums. Num APFS de Mac ou NTFS de Windows, existe checksum no nível do arquivo. Quando o arquivo é gravado, o sistema gera um checksum e grava junto. Quando pedimos pra ler, ele pega os blocos e recalcula o checksum pra comparar com o que estava guardado. Se os checksums forem diferentes, significa que o arquivo está corrompido. Não é isso, mas se não sabe o que é um checksum, pense num dígito verificador.






Essa informação é super importante, porque numa planilha com milhares de valores, podemos estar tomando decisões importantes de forma errada por causa de um único bit errado. Um único bit pode mudar um valor de 100 reais pra milhões, por exemplo. Mas se o sistema avisar que o checksum está errado, sabemos que não podemos confiar nesse arquivo. O que fazer? Se você seguiu a recomendação e fez a porcaria de um backup, podemos ir nesse backup recuperar esse arquivo.






Em sistemas de arquivos mais modernos, pensados pra situações mais parrudas como servidores de arquivos, pense um Dropbox ou Google Drive da vida, com dados de milhões de pessoas, existem opções como o lendário ZFS da antiga Sun e o novo BTRFS do Linux. Eles tem checksum por blocos. Então conseguimos saber qual bloco dentro do arquivo está corrompido. Mas eles vão mais longe.






Meu NAS Synology está em configuração de RAID, com múltiplos HDs. Significa que o mesmo bloco tem cópias redundantes em mais de um HD. E BTRFS tem uma rotina que recomenda rodar periodicamente, digamos de 3 em 3 meses ou de 6 em 6 meses, chamado "scrubbing". É um processo pesado que vai rodar em background, sem interromper seu dia a dia, e rechecar checksum a checksum de todos os blocos. Se notar que uma falhou, e estivermos em RAID, ele vai procurar uma cópia redundante desse bloco. A chance dos dois estarem corrompidos ao mesmo tempo é baixa, então podemos usar o bloco intacto pra consertar o que corrompeu. 







Todo HD falha. Não importa se é HD mecânico com discos ou chips de Flash NAND de SSDs ou NVMEs. Todo chip Flash tem uma vida útil de quantidade de operações de escrita. Depois que passar desse limite, estraga e é isso. As checagens que falei, em muitos casos, só vão indicar mais cedo que algo falhou, mas sem redundância, sem uma segunda cópia intacta, não tem como adivinhar o que era o dado original que corrompeu, só dá pra saber que corrompeu.






Checagens de checksum, recuperação de blocos corrompidos, gastam RAM, gastam CPU. Não sai de graça. Servidores de arquivo parrudos como ZFS precisam de gigabytes de RAM pra serem eficientes, e vão usar toda RAM possível. Claro que tem mais vantagens que só isso. NTFS acho que não tem, mas APFS de Mac, assim como ZFS e BTRFS de Linux tem deduplication.





Faz de conta que você é desenvolvedor web e tem diversos projetos de Node.js na sua máquina. Sabe o maldito "npm install" que gera aqueles diretórios node_modules em todo projeto? Concorda que na maioria dos projetos vai ter as mesmas bibliotecas e os mesmos arquivos? Então estamos desperdiçando espaço com tanta duplicata? A resposta é sim, está desperdiçando.






Pra combater isso existem processos de deduplicação. Num NTFS de Windows, se estiver em Windows Server, ele oferece ferramenta pra que, caso dois arquivos tenham exatamente o mesmo conteúdo binário, ele consiga fazer o metadado dos dois apontarem pros mesmos conjuntos de blocos. Se forem dois arquivos de 200MB, originalmente iriam ocupar 400MB, mas com deduplicação, vai ocupar só 200MB. Só volta a ocupar mais espaço se decidirmos modificar o segundo arquivo, por exemplo. 






Isso é muito útil no cenário de servidor de arquivos da empresa. O chefe manda um email com um PDF de 100MB pra 10 pessoas. Cada uma grava no seu diretório pessoal no servidor. Iria ocupar 1 giga. Mas se rodar a ferramenta de deduplicação, ele faz as 10 cópias apontarem pros mesmos blocos de um arquivo só, economizando 10 vezes o espaço. É o mesmo caso de termos 10 projetos de Node, cada um com node_modules repetidos. Rodando algum processo de deduplicação, no caso uma ferramenta como Opendedup no Linux, dá pra fazer algo semelhante no ext4.







Mas nenhuma dessas soluções é ideal. Os sistemas de arquivos NTFS e ext4 não foram feitos pra suportar esse tipo de coisa. O sistema de arquivos em si não suporta nada disso, daí dependemos de uma ferramenta externa pra tentar adicionar essas funcionalidades. O ideal é que o próprio sistema de arquivos implemente Copy on Write, independente de uma ferramenta externa.






Copy on Write ou CoW é super importante. É literalmente os conceitos de um Git só que em nível do sistema de arquivos inteiro, funcionando de forma transparente pro usuário. Num sistema copy on write, quando editamos um arquivo que já existe, os novos blocos não são gravados por cima dos antigos, ele faz o equivalente a um branch de Git e os novos blocos são gravados nesse branch, parecido com commits. Isso mantém o arquivo original intacto.







De cara, isso também evita mais corrupção de dados. Antigamente, num FAT16 da vida, onde estávamos na situação onde não tínhamos espaço sobrando, não tinha outra opção: toda modificação de arquivos precisava gravar os blocos novos em cima dos velhos. Mas e se acabasse a luz no meio? Ia ficar um arquivo corrompido. E isso era comum. Pra mitigar esse problema criou-se o conceito de journaling, que NTFS no WIndows, ext4 no Linux. HPFS+ no Mac passaram a suportar no fim dos anos 90.







Super resumido é assim. Digamos que eu tenha um arquivo, aquele Powerpoint, e ele ocupa 10 blocos no disco. No sistema de arquivos temos o nome do arquivo e um mapa apontando pra esses 10 blocos. Isso faz parte dos tais metadados de um arquivo. Agora editamos por cima e essa mudança afetaria os dois últimos blocos. Esses dois blocos serão gravados no disco e a localização será gravada no journal. O arquivo original ainda tem os 10 blocos originais até este ponto.






Ao terminar de gravar os dois novos blocos, só aí o NTFS vai atualizar o mapa do arquivo e mudar a referência dos dois últimos blocos pra apontar pra localização dos dois novos. Mas digamos que ele começou a mudar a referência do penúltimo bloco e antes de mudar a referência do último, acaba a luz e o sistema reboota. Quando reiniciar, o NTFS checa o journal e vê que faltou mudar a localização do último bloco. 







Como ele tem essa informação no journal, vai conseguir terminar a operação e o Powerpoint não fica corrompido, como ficaria antes. Se ele ficasse em dúvida, faria rollback, e manteria o arquivo intacto. Os novos blocos não sobrescreveram os antigos ainda. Esse processo gasta um pouco mais de espaço, mas evita que arquivos fiquem corrompidos. Espaço hoje é mais barato, então podemos fazer isso.







Copy on Write vai um passo além. Depois que os novos blocos são gravados e registrados no journal, é feito tipo um fork no nível dos metadados. Imagine o mundo antes de Git. Se você copiasse um arquivo em cima do outro, o que acontece com o original? Ele se perderia pra sempre, certo? É assim que todo sistema de arquivos funciona. Mas o que acontece quando habilita Git num diretório e copia um arquivo em cima do outro? Vamos testar.






Aqui temos um diretório com Git habilitado e os arquivos já comitados. Se listar o que tem neste hello.txt, veja, "hello world". Vou criar um novo arquivo fora, lá no /tmp com conteúdo de "bla bla bla". Copio o arquivo por cima do meu original. Normal, meu arquivo original com "hello world" foi pro saco como podem ver. E se eu mudar de idéia? 







Agora entra o Git pra te salvar. Fazendo "git status" podemos ver que ele sabe que esse arquivo foi modificado mas ainda não comitamos, não nos comprometemos com ele. Então podemos fazer "git checkout" desse arquivo, olha o conteúdo, "hello world" de volta. Git nos permite mudar de idéia e não perder dados ao custo de manter uma cópia do original em algum lugar ocupando espaço extra.







É por isso que todo projeto deve usar Git. Podemos mudar de idéia sem preocupação. Quando estivermos satisfeitos fazemos commits, e mesmo assim podemos reverter e até recuperar commits apagados. Copy on Write em sistema de arquivos é a mesma coisa. E em Linux, que eu saiba, só existem dois sistemas de arquivos modernos que suportam isso: o  ZFS e o BTRFS.






Quem usa Ubuntu tem na instalação a opção de usar ZFS. E aqui já aviso: evite. É muito fácil fazer besteira em ZFS e perder tudo sem querer. ZFS é excelente pra quem está montando um servidor de arquivos, usando distros com o TrueNAS, que é o melhor. Só quem estudou bastante a documentação de ZFS deve tentar usar. Não basta só instalar e acabou, precisa de constante manutenção e monitoramento pra saber se está realmente tudo funcionando como deveria. Pra usar no dia a dia, no seu notebook, acho que vai mais atrapalhar do que ajudar.







Isso não quer dizer que ZFS é ruim, pelo contrário, é provavelmente o sistema de arquivos mais avançado que existe. Porém, pros propósitos de máquina do dia a dia de programador é como matar uma mosca com uma bazuca e arriscar atirar a bazuca no seu pé. Outra coisa: todo mundo me pergunta qual distro eu mais gosto. Eu uso Manjaro GNOME no meu dia a dia. Primeiro porque é um Arch Linux mais estável. Ele segura um pouco os pacotes mais novos, pra testar e garantir que nada vai quebrar. Mesmo assim alguns updates ainda quebram de vez em quando, mas é um bom compromisso entre ter coisas novas mais rápido e não ficar parado a cada novo update.







Mais importante: Manjaro já vem pré-configurado pra BTRFS logo na instalação. É só instalar, escolher BTRFS e ele vai se virar e te dar um ambiente estável. Em vez de mostrar Manjaro, vou mostrar Ubuntu 23, que acabou de lançar. Todo o trabalho de configuração que vou mostrar agora não precisa fazer fazer no Manjaro. Começando na instalação, todo mundo escolhe aqui a primeira opção, que vai instalar EXT4 igual toda outra distro. Mas agora ela tem essa segunda opção com ZFS, que é experimental ainda. De novo: não teste numa máquina de trabalho. Teste numa máquina virtual pra aprender tudo primeiro caso queira mesmo adotar ZFS.







Em vez disso vamos nessa terceira opção e particionar o disco manualmente. Não vou explicar cada detalhe. Vamos assumir que você está num PC moderno, dos últimos 10 anos. Provavelmente vem com modo legado na BIOS desligado, já suportando boot com EFI. Falando nisso, não deixe de assistir meu video que explica como o Boot de Linux funciona.






Enfim, vamos primeiro criar uma partição EFI que a BIOS vai procurar primeiro. Uns 200 megabytes costuma ser suficiente, deixe 500 se quiser garantir. Depois precisamos de uma segunda partição com um sistema de arquivos legado fácil da BIOS ler. Em Windows costuma ser FAT32, mas aqui podemos escolher o antigo EXT2 mesmo. Eu deixo 500 mega, mas de novo, pra garantir pro futuro caso a kernel cresça de tamanho, deixa 1 gigabyte. Espaço é barato.







Agora usamos o resto do espaço pra partição primária com BTRFS e montando o root nela. Se for um notebook, sempre recomendo criar uma partição encriptada com LUKS, ou no mínimo ter o subvolume de HOME encriptado com LUKS. Não vou explicar nada disso aqui porque é um saco de configurar, então pesquisem no Wiki do Arch sobre isso. BTRFS funciona mais ou menos como um LVM, um gerenciador de volumes lógico. Ele começa como um pool de subvolumes e por default vai criar um subvolume chamado "arroba" onde vai montar o "/" root e outro  "@home" onde vai montar o "/home".







Novamente, pesquise sobre LVM e volumes lógicos. Antigamente uma boa prática era separar duas partições: uma pra root onde fica o sistema operacional e outra pra home, daí montamos essa partição como "/home". O problema de usar partições é que eles tem tamanho fixo. Uma vez definido não tem como redimensionar fácil. É como no Windows quando você particiona C: e D:. Mas com volumes lógicos, podemos redimensionar on the fly, sem qualquer problema. E BTRFS vai um passo além, subvolumes servem propósito parecido de volumes lógicos mas nem precisamos nos preocupar em redimensionar, eles são dinâmicos.








Enfim, quando terminar de instalar podemos bootar. Vamos acelerar aqui, três, dois, um e pronto. Ubuntu instalado. É muito fácil instalar distros Linux hoje em dia. Só não instala quem tem muita preguiça ou tem um hardware muito, mas muito ruim. Quando Linux não identifica hardware só tem duas opções: ou ele é muito ruim, ou é muito novo ou recém lançado. Se você não comprou um note que saiu este ano, não tem desculpa, é o hardware que é ruim, porque drivers pra Linux são atualizados constantemente. 







Se estiver em dúvida num notebook, e tiver a oportunidade de experimentar antes de comprar, leve um pendrive com um Manjaro ou Ubuntu da vida e faça boot pelo pendrive. Veja se ele consegue identificar tudo: wifi, bluetooth, áudio, placa de video. É o melhor teste que pode fazer. Se alguma coisa não for detectada, desista desse modelo. Vai te dar mais trabalho do que compensa.






Pronto, agora vamos listar o arquivo "/etc/fstab" que o Ubuntu criou. Super simples. Dispositivos de armazenamento são identificados por um UUID. Abrindo o terminal, basta digitar "lsblk -f" e vai ter uma coluna com UUID. O arquivo "/etc/fstab" define quais dispositivos vão montar onde durante o boot. Por exemplo, o dispositivo de UUID que começa com ed022 vai montar como root "/". Olhem na listagem, é o dispositivo /dev/vda3. O /dev/vda2 de UUID que começa com 294c8 monta como /boot.






A graça começa quando vemos o ponto de montagem de "/home". O Ubuntu, pelo menos, separou corretamente um subvolume de BTRFS chamado "@home". É uma boa prática. Mas temos alguns problemas. Do jeito que está aqui, não podemos ativar snapshots, porque seu espaço em disco vai desaparecer rapidamente.





Snapshots são possíveis por causa da funcionalidade de copy on write do BTRFS que expliquei antes. Copy on write funciona mais ou menos como branches num Git. Lembra que mostrei que o Git consegue manter o arquivo original, sem perder o conteúdo, mesmo eu gravando outro por cima? Pra fazer isso, obviamente, precisa manter o conteúdo original gravado em algum lugar, certo? Está gastando espaço extra. Diretórios do Linux como /var/cache, /var/log, /var/tmp estão em operação o tempo todo. Mesma coisa /tmp ou arquivo de swap. Se ficar mantendo versão velha deles em snapshots, o espaço livre do seu sistema vai acabar muito rápido sem você entender porque.






Pra resolver é assim: abre o terminal e faça "sudo chattr +C /var/log". Isso é change attribute, estamos mudando o atributo do diretório pra dizer pro BTRFS não considerar arquivos nesse diretório pra snapshot. Esse change attribute só vai afetar novos arquivos criados, não os antigos que já existem, sempre se lembrem disso, mas como acabamos de instalar, acho que só isso deve ser suficiente.







Agora vamos instalar o Timeshift. É um programa gráfico pra fazer snapshots periódicos do sistema. Pra iniciantes, vai ser mais fácil do que lembrar os comandos pro terminal. Basta fazer "apt install timeshift" ou "yay -S timeshift" no Arch ou Manjaro. Logo que abrir ele vai oferecer pra configurar pra você. Mesmo se não estiver usando BTRFS, a alternativa é usar Rsync, caso esteja usando ext4.






Rsync, é um programa pra sincronizar dois diretórios. Equivalente no Windows é o novo robocopy ou o antigo xcopy. O jeito força bruta, sem a ajuda do sistema de arquivos, pra ter um backup local, é fazendo sincronização de diretórios. O problema é óbvio: vai ocupar espaço pra cacete no seu sistema, porque periodicamente vai duplicar tudo. Sim, se algo der errado e apagar algum arquivo que não devia, vai ter uma cópia. Com a funcionalidade de copy on write e snapshots do Btrfs temos a mesma coisa, mas sem duplicar nenhum arquivo e sem gastar mais espaço do que realmente precisa no mesmo HD.






Tá complicado de entender? Olha só, vou baixar um arquivo qualquer aqui, escolhi a ISO do Ubuntu. Acelerando e ... pronto, baixado. Agora vou no Timeshift e crio um snapshot manualmente. Com o timeshift configurado posso escolher pra  fazer snapshots de hora em hora, e guardar X snapshots por hora, Y snapshots por dia, Z por semana e assim por diante. Depois dêem uma fuçada, mas criei um pra mostrar agora. Snapshots são baratos, pode fazer toda hora. Não importa se você tem mil arquivos ou um milhão de arquivos, diferente de rsync, o tempo de criação de snapshots é quase instantâneo, porque ele não está duplicando nenhum byte, só metadados.






Simulando um acidente, oops, apaguei essa ISO que acabei de baixar. Que droga, vou ter que esperar um tempão pra baixar de novo. Ou não. Vamos no Timeshift e posso navegar pelos arquivos do snapshot. Olha só, se formos na pasta de downloads, a ISO está lá. Pronto, só copiar de volta pra pasta de downloads corrente e tá na mão: arquivo recuperado.





Não ficou claro? Vou dar outro exemplo. Mas pra isso precisamos terminar de configurar o Timeshift. De novo, siga o artigo que deixei linkado abaixo. Diferente do Manjaro que já me dá isso automaticamente, no Ubuntu precisamos manualmente instalar o grub-btrfs a partir do código fonte. Primeiro precisa instalar os pacotes build-essential pra termos compilador, e o git. Daí fazemos clone do repositório e "make install".






Também precisamos instalar o pacote inotify-tools e editar a configuração do grub-btrfs com "systemctl edit --full grub-btrfsd" e trocar a opção "/.snapshots" por "--timeshift-auto". Pronto, agora só iniciar o serviço e habilitar pra iniciar automaticamente depois do boot. E finalmente, mais um programa que precisamos instalar a partir do código fonte, o timeshift autosnap. Mesma coisa, só fazer clone do repositório e rodar "make install" dentro. 





O que tudo isso faz? Se ainda não passou por isso, é só uma questão de tempo. Sabe quando estamos no meio do dia e pensamos, ah, vou fazer uma atualização no sistema. "apt update" daí "apt upgrade", reboota o computador e pau, não boota mais. E vai ser justo naquele dia que você tá sem nenhum pendrive por perto, daí não consegue bootar no LiveCD. Você tá de home office e decidiu ir trabalhar do Starbucks, não tem nenhum amigo com computador por perto pra baixar a ISO pra você. Acabou sua tarde. Vai ter que voltar pra casa, achar um pendrive, baixar a ISO e gastar um tempo no stackoverflow tentando voltar um kernel antigo pra bootar de volta na sua máquina. Quem nunca?







E se eu disser que nunca mais vai precisar fazer isso? Vamos simular de novo. O Timeshift já está configurado pra fazer snapshots periódicos. Com isso, caso um dia lembre, puts, eu queria aquele arquivo que apaguei mês passado e achei que não fosse precisar. Meu HD de backup tá em casa, e agora? Sem problemas, só pegar o snapshot do mês passado, que está local na sua máquina. Mas, e na situação quando fizer um "apt upgrade" e isso quebrar seu sistema? Sem problemas também, aquele pacote timeshift autosnap vai criar um snapshot automático antes de todo "apt upgrade".






Daí aquele pacote grub-btrfs vai listar esses snapshots no menu de boot do grub. Oops, Ubuntu por padrão não mostra o menu do Grub. Mas isso é fácil, só editar o arquivo "/etc/default/grub" e mudar a opção GRUB_TIMEOUT_STYLE de "hidden" pra "menu" e a opção GRUB_TIMEOUT de zero pra uns cinco ou dez segundos. Pra atualizar, só rodar "update-grub". Vamos ver como funciona.





Pra simular, vamos criar mais um snapshot temporário chamado "teste" no Timeshift. Como é um snapshot manual, vou rodar "update-grub" manualmente, mas num "apt upgrade" não precisaria. Agora vou abrir a loja do Ubuntu e instalar alguns programas. Vamos instalar aqui o Gimp, vai demorar um bocado. Acelerando ... Agora instalamos o Discord, de novo, vamos acelerar. Pronto. Podemos checar no menu, e estão aqui, Gimp, e Discord. Faz de conta que instalei um monte de coisas mas no final mudei de idéia, queria desinstalar tudo e voltar como estava antes. Como faz?






Fácil, só rebootar. Agora aparece o menu do Grub aqui no boot e tem esta nova opção e olha só os snapshots que estávamos vendo no timeshift. Estão vendo a descrição "teste"? Basta escolher esse ou um snapshot anterior. Entenderam? Posso bootar direto em um snapshot. Esperamos iniciar, e olha só. Cadê o Gimp? Cadê o Discord? Como o nome diz, timeshift, ele fez um shift no tempo, voltamos ao passado, exatamente como estava antes de instalar aqueles aplicativos. Imagine que em vez de Gimp ou Discord fosse um "apt upgrade" e veio uma kernel quebrada que fode o boot? Basta rebootar, escolher o snapshot anterior e pronto, vai ser como se nada tivesse acontecido.






Abrindo o Timeshift, pra tornar isso permanente, posso fazer um Restore do snapshot antigo pra passar a ser meu sistema corrente. Só isso. Entenderam? Com o Timeshift temos um backup local que ocupa muito pouco espaço e com isso qualquer acidente pode ser facilmente recuperado sem precisar de HD externo, sem precisar esperar copiar tudo de backup nem nada, muito menos reinstalar tudo do zero. É instantâneo.






Claro, isso é uma conveniência, pense como sendo sua última barreira de defesa. Isso não substitui um backup externo. O Timeshift e BTRFS, pra funcionar, depende que seu SSD esteja funcionando perfeitamente. Se o SSD em si der pau, como mostrei no começo do video, isso não vai te salvar. Aí só um backup externo. O que estou recomendando é ter as três coisas que mostrei até agora: se disciplinar a usar Git em todo projeto, pessoal ou de trabalho. Daí ter BTRFS e Timeshift pra consertar acidentes rápidos. E um backup externo pra evitar catástrofes mais sérias. São três níveis de segurança de dados pra ter dar paz de espírito.







E pra finalizar, tem um detalhe importante no caso de BTRFS. Aqui tem a ver um pouco com a Rinha de Backend de novo. Durante toda minha exploração dos projetos dos participantes, fiquei rodando Docker Compose de cada projeto. Eu já fiz um video explicando containers e Docker em particular, não deixem de assistir. Mas vamos entender o problema.






De dentro do projeto de qualquer um dos participantes, vamos rodar "docker compose up". Lendo o arquivo docker-compose.yml, vai estar configurado várias aplicações, por exemplo, nginx, postgres. De onde vem esses aplicativos? O Docker vai baixar a imagem do docker.io. É isso que significa esses "pulls". Uma imagem não é um único arquivo, é um conjunto de arquivos binários, que chamamos de overlays. Pense como se fossem commits de Git, mas commits binários. Esses overlays são unificados num único sistema de arquivos usando o Union File System ou UFS.







Se rodar o comando "mount" olha só aqui no fim, vários pontos de montagem usando overlays. Rode o comando "docker image ls" e temos a listagem das imagens baixadas e geradas localmente. A imagem do projeto em Node.js tá custando 220MB, o nginx tá ocupando quase 200MB, o postgres mais de 400MB. Só esse projeto, pra rodar, precisou gastar quase 1 Giga aqui no meu HD.






Onde ficam esses arquivos de overlays? Dentro do diretório "/var/lib/docker/overlay2". Podemos listar como sudo e temos vários diretórios com o SHA256 de cada camada da imagem. Agora experimente testar docker compose de uns 50 projetos. Coisas como postgres e nginx, se forem da mesma versão, vão ser reusados, mas cada imagem de aplicação custa na faixa de 200 MB pra cima. Então 50 vezes 200 vai dar quase 10 Giga.






De tempos em tempos é recomendado rodar um "docker system prune" pra apagar imagens que não estão sendo usadas. Mas cuidado, se tiver containers, mesmo desligados, associados a uma imagem, ela não vai ser apagada e vai ficar sobrando ocupando espaço a toa. Use o comando "docker container ls -a" pra listar e apague os que não precisa. No caso de docker compose, quando acabar de testar o projeto, faça "docker compose down rmi --all" pra limpar containers, imagens, volumes e network que não for usar mais.






Mas beleza, 10Gb pra 50 projetos parece pouca coisa. O problema é que eu não excluí o diretório do docker da solução de snapshot. E o timeshift vai ficar criando snapshots periodicamente. Pense assim, eu fui baixando docker compose up, um monte de imagens, ao longo de mais de uma semana. Todo dia o timeshift foi fazendo snapshots. Num dia eu fazia docker compose up. No dia seguinte fazia docker compose down, achando que tava apagando tudo, mas na verdade eles estavam sendo guardados nos snapshots.






Depois eu resolvo testar o projeto de novo, faço um novo docker compose up. Vai baixar 1 giga de imagens outra vez. Só que agora tem 1 giga das imagens antigas no snapshot de ontem, e mais 1 giga duplicado no snapshot de hoje. BTRFS tem opção de deduplicação, mas ele não roda  automaticamente, por padrão vai duplicar tudo. Repita isso em diversos projetos ao longo de duas semanas.






Quando terminei de testar tudo, escrevi o script do último video, gravei, editei, e meu sistema começou a ficar estranho. Tudo foi ficando mais lento. No começo achei que era pau do Microsoft Edge, porque ele começou a crashear do nada. Tentei reinstalar e nada. Ué, que diabos tá acontecendo com meu sistema. Eis que comandos de instalar pacotes como yay começaram a dar problema e aí caiu a ficha. Fui checar quanto de espaço eu tinha com "df -h" e tava dizendo que tava com 100% ocupado na partição primária.






Puts, as malditas imagens de docker! Tentei dar um docker system prune. Mas nada. Continua ocupando 100% do disco. Puts, os snapshots! As malditas imagens ficaram presas em snapshots do passado. Abri o timeshift e apaguei todos os snapshots do passado. Voltei pra verificar o espaço livre, e nada, continuava ocupando. Ué, que diabos.






BTRFS é conservador, ele não vai apagar nada a menos que você realmente diga pra apagar. Apagar snapshots não libera o espaço, pra isso precisa reclamar esse espaço de volta. Deixa eu demonstrar. Podemos rodar o comando "btrfs filesystem df /". Uma das pequenas desvantagens de usar btrfs é que ferramentas normais de Linux como o comando "df" propriamente dito, podem informar valores incorretos.






No nosso Ubuntu de exemplo, estamos usando um total de 25GB. Isso é somando coisas como metadados do sistema de arquivos e tudo mais. Os bytes dos arquivos em si é esse "used", pouco mais de 23GB. Ou seja, pelo menos 2GB são só metadados. Você precisa entender essa diferença. Tamanho de um arquivo não são só os bytes dentro desse arquivo. O nome do arquivo ocupa espaço, a informação de data de criação também, atributos como read only, localização no diretório também. Tudo ocupa espaço. E quanto mais avançado é o sistema de arquivos, mais espaço vai ocupar de metadados.







Enfim, se você não tem noção de quanto um sistema recém instalado ocupa, pode pensar "ah, 25 giga, ok, faz sentido". Mas lembra que eu fiz alguns testes de snapshots pra vocês verem na prática? E aquele Gimp, e aquele Discord? Eles desapareceram, mas os bytes deles continuam no snapshot antigo. Vamos abrir o Timeshift e apagar todos os snapshots. 






Rodando de novo o comando "df" do btrfs, não mudou nada. Continua indicando 25GB. Foi o que aconteceu na minha máquina quando acabou espaço. Mesmo apagando os snapshots, continuava indicando falta de espaço. Precisa manualmente reclamar o espaço de volta, pra isso usamos o comando "btrfs filesystem balance start /". Esse comando é bem demorado. Pode levar mais de hora numa máquina como a minha, mas pelo menos podemos continuar usando o sistema em paralelo.






Enfim, esperamos acabar e pronto. Vamos ver de novo o comando "df" e olha só, caiu de 25GB pra 10GB! Tinha 15 GB sendo reservado pelo btrfs seja pros snapshots antigos e outras coisas dele. E isso numa instalação nova, praticamente vazia, onde só instalamos 2 programas. Agora imagina no meu caso, com dezenas de imagens de docker em snapshots. Ocupou meu HD inteiro de 1 terabyte. Pois é, foi muito fácil encher 1 tera em poucos dias só de não ter tomado cuidado.







E como faz pra isso não acontecer? Tem que fazer "sudo chattr +C /var/lib/docker" ou também "sudo chattr +C /var/lib/libvirt" no caso de estar usando Libvirt pra máquinas virtuais. E de novo, isso só vai se aplicar a arquivos novos. O correto é primeiro limpar esses diretórios, daí mudar esse atributo. Acho que o certo não é aplicar esse atributo no diretório todo, algumas coisas talvez você queira manter snapshots, como volumes de Docker. Fique só atento com isso.







Essa dica é muito importante, porque se você não entender o funcionamento de snapshots no btrfs e começar a usar como máquina de desenvolvimento, com certeza vai ter imagens de docker, e isso com certeza vai destruir o espaço livre em poucas semanas. Você não vai saber que é, nenhum tutorial de como liberar espaço vai funcionar, você não saberia que precisa rebalancear o sistema, e no final iria desistir e reinstalar tudo do zero de novo. 






No momento em que estou escrevendo este script, no meu Manjaro, se rodar "df" ele me diz que está usando 775 giga do meu NVME de 1TB de novo. De arquivos mesmo são só 164 GB. Bora rodar "balance start" e ir tomar café. Deixei 1 hora rodando e olha só, agora diz que está usando um total de só 166 giga, que faz mais sentido. Ainda tinha uns snapshots velhos, provavelmente com imagens de docker velhas. 






Logo que instalei o Timeshift, mostrei que tem como configurar pra guardar snapshots só de hoje, ou da semana, ou até de meses. Se você tem um bom backup externo, isso não é necessário, só o do dia ou até o da semana deve ser suficiente. Quando ver que está acabando o espaço, sabendo que tem tudo no backup externo, pode apagar todos os snapshots do timeshift, menos o último. Daí rodar o rebalanceamento e pronto.







Segurança é um trade off com performance e espaço. Gastamos mais processamento e gastamos mais espaço, pra garantir paz de espírito, essa é a filosofia. E felizmente chegamos num ponto onde o sistema operacional realmente nos ajuda a não perder nada. A única coisa que nenhum software consegue fazer, é consertar um hardware barato porcaria. Contanto que o hardware seja bom, é quase impossível perder dados hoje em dia. Tão importante quanto não perder dados importante é não perder horas ou dias tentando recuperar seu sistema por causa de uma atualização bugada. Basta não ter preguiça, porque as ferramentas estão à nossa disposição e, como demonstrei, são super fáceis de usar.







O meu NAS Synology usa BTRFS também, mas em configuração de RAID. Ele roda scrubbing a cada 6 meses e faz checagens de checksum pra garantir que nenhum erro físico nos HDs me faça perder dados. Isso é só pra quem escolher ter um NAS: lembre-se de configurar pra rodar scrubbing, é essencial, mas isso vem desligado por padrão. Não basta só instalar e achar que vai tudo funcionar por mágica. 







Numa distro Linux como Ubuntu ou Manjaro, tenho o Timeshift. Mas no sistema operacional da Synology, ele tem o Snapshot Replication, que também me permite navegar por snapshots antigos e recuperar coisas rapidamente. Olha só, se der qualquer problema, consigo ver como eram meus arquivos de 4 meses atrás. E não sei se perceberam, mas esse sistema de snapshots me dá outra coisa de graça: proteção contra ransomware.







O que é um ransomware? É um malware que você instala ao abrir um powerpoint ou PDF qualquer que veio de um email desconhecido e na verdade era um programa malicioso. É assim que YouTuber perde o canal deles: clicando em PDF falso que não era um PDF. Parem com essa mania de dar duplo clique em tudo. O malware se instala silenciosamente na sua máquina e começa a encriptar seus arquivos. Pode levar dias e você não vai perceber até precisar abrir um desses arquivos e notar que não abre mais.






No final vai aparecer uma janela parecida com essa dizendo algo como "Você se fodeu, encriptamos todos os seus arquivos, podemos te dar a chave pra desencriptar mas você vai nos pagar X bitcoins pra isso. Mande pra este endereço e daí te damos a chave". Isso realmente acontece, e não existe garantia nenhuma de receber essa tal chave, mesmo pagando. Se isso acontecer com você, nunca pague, só aceite que você se fodeu. Reze pra ter seguido minhas recomendações.






Ah, mas eu tenho backup externo, então tô seguro, não? Não. Eu disse que leva dias pra esse processo conseguir encriptar todos os seus arquivos. Durante esse período o que você fez? Plugou o HD externo e deixou fazendo backup dos arquivos encriptados por cima dos arquivos bons do backup. Agora seu backup também foi contaminado, parte dele tá encriptado. Perdeu playboy.







É menos ruim se estiver usando Google Drive ou Dropbox ou OneDrive da vida, porque eles tem suporte de snapshot. Mas eu não confiaria muito. Nunca precisei usar o deles então não fucei o suficiente, mas veja esta minha pasta do Obsidian no Dropbox, que é onde edito meus scripts. Tem essa função de Rewind, que é voltar pra trás. Escolho o dia que quero voltar, e ele me mostra as últimas atividades. Daqui posso escolher voltar pra algum ponto do passado, mais ou menos como commits também. É meio um saco porque eu não vi uma interface que permite voltar meu Dropbox inteiro, acho que tem que ir diretório a diretório, não tenho certeza. Enfim, não achei muito intuitivo de usar, mas em última instância, se não tiver mais pra onde recorrer, talvez isso ajude.








Se um ransomware encriptar todos os seus arquivos, mesmo no Dropbox vai estar tudo encriptado, mas se tiver o Rewind de alguns dias pra trás,  pode tentar recuperar. Isso é ok, mas eu não gosto de depender de um serviço que não tenho controle. Por isso essa mesma pasta de Dropbox está em BTRFS no meu NAS, sendo sincronizado com o aplicativo Cloud Sync da Synology. Eu uso os três, Google Drive, Microsoft OneDrive e Dropbox como backups secundários offsite de alguns dados. E o meu NAS tem snapshots de BTRFS ativado pra até 4 meses pra trás.







Ou seja, isso nunca vai acontecer, mas se por ventura algum malware conseguir passar por mim e encriptar meus arquivos, eu só dou risada, volto alguns snapshots pra trás e mando esse hackerzinho de merda tomar no cú. Não pego malware faz pelo menos uns 20 anos, e sempre tenho múltiplos backups de tudo, tanto local quanto offsite, fora daqui. E os arquivos mais antigos que realmente não quero perder já queimei tudo em Blurays Millenium Disk, feitos de material inorgânico que nunca oxidam. Todos guardados no meu cofre. Vem tentar encriptar meus dados seus hackerzinhos de merda. 







Segurança é uma questão de oportunidades. Quanto mais você pensar que nunca vai acontecer com você, maiores as chances de realmente acontecer. Eu penso o contrário, sempre penso que estou 100% inseguro 100% do tempo e faço toda a estratégia em cima disso. Segurança funciona em camadas. Se o plano A falhar, tem o plano B. Se o plano B falhar tem o plano C. Isso se chama contingência. Quem não prepara contingências, sempre vai viver dando desculpas. E eu odeio desculpas. Se desculpas funcionassem, não precisávamos de polícia.






Por fim, um comentário que não coube antes no video é sobre como mantenho meu Manjaro. Uma recomendação que dou é usar muito Docker pros seus projetos. Não precisa instalar coisas como MySQL, Postgres, Redis nem nada local. Use tudo com Docker Compose. Primeiro porque isso garante que estamos rodando a versão certa de cada coisa pra cada projeto. Segundo, porque é menos pacotes instalados na sua máquina que o Manjaro vai precisar ficar atualizando. Quanto mais pacotes instalar, maiores as chances de algum upgrade quebrar alguma coisa.






Pelo mesmo motivo, tudo que é aplicativo como Discord, Zoom, o próprio browser Edge que eu uso, Firefox, e muito mais, eu prefiro instalar via Flatpak. Flatpaks são containers, tipo um Docker light. E dá pra usar coisas como FlatSeal pra garantir que nenhum aplicativo tem mais permissões e acessos do que deveria. Podemos fechar um Discord pra não conseguir enxer nada dos seus arquivos, só deixar ele ver a pasta de Downloads, por exemplo. Depois veja este video explicando sobre Flat Seal.





Eu sei que vai aparecer o povo do NixOS nos comentários, mas eu não pretendo usar e nem preciso. Meu sistema é super estável. Raramente tenho problemas, porque eu instalo o mínimo possível. Pra projetos tá tudo em Docker. Aplicativos tudo em Flatpak ou Snap. E coisas mais arriscadas, como games, ou meus experimentos com LLMs e IAs, estão tudo em máquinas virtuais com QEMU/KVM como mostrei neste outro video. Fazendo tudo que mostrei neste video, não precisei reinstalar meu sistema nenhuma vez e nunca perdi nem dados e nem tempo. Qualquer besteira que eu faço, o Timeshift me salva, qualquer besteira maior e os backups no meu NAS me salvam.






Recapitulando: sempre, sempre, inicie um repositório git local no seu projeto, independente de ter no GitHub ou não. Sempre, sempre, tenha no mínimo um HD externo USB da vida, um pouco maior do que o HD do seu PC, e configure o app de Backup que já vem no seu sistema operacional. Não precisa de nada super fancy nem nada. Só habilita. Qualquer backup é melhor que nenhum backup. Finalmente, se quiser aprender mais de Linux e usar o que há de mais novo, instale Manjaro com suporte a BTRFS e configure o Timeshift como eu expliquei. Isso vai tornar você quase invencível, é praticamente um super poder. Se ficaram com dúvidas, mandem nos comentários abaixo, se curtiram o video deixem um joinha, assinem o canal e compartilhem o video com seus amigos. A gente se vê, até mais!

