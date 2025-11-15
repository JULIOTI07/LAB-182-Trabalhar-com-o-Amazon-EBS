# LAB-182-Trabalhar-com-o-Amazon-EBS
O Amazon Elastic Block Store (Amazon EBS) é um serviço de armazenamento em bloco dimensionável e de alto desempenho projetado para o Amazon Elastic Compute Cloud (Amazon EC2). Neste laboratório, vamos criar um volume do EBS e a executar operações nele, como anexá-lo a uma instância, criar um sistema de arquivos e fazer um backup de snapshot.


<img width="1085" height="365" alt="lab 182" src="https://github.com/user-attachments/assets/1128dca9-a31b-4a65-b5e6-b004dd6551a1" />

 
Objetivos
Ao final deste laboratório, você poderá:

Criar um volume do EBS.

Anexar e montar o volume do EBS em uma instância do EC2.

Criar um snapshot de um volume do EBS.

Criar um volume do EBS com base em um snapshot.
Acessar o Console de Gerenciamento da AWS
Na parte superior destas instruções, selecione  Iniciar laboratório para iniciar o laboratório. 

Dica: se você precisar de mais tempo para concluir o laboratório, selecione novamente  Iniciar laboratório para reiniciar o cronômetro do ambiente.

O status dos recursos do laboratório é exibido no canto superior esquerdo:

A AWS  indica que os recursos do laboratório da AWS estão sendo criados no momento.

A AWS  indica que os recursos do laboratório da AWS estão prontos.

Aguarde até que o laboratório esteja pronto para prosseguir.

Na parte superior destas instruções, selecione AWS  para abrir o Console de Gerenciamento da AWS em uma nova guia do navegador. O sistema faz seu login automaticamente.

Dica: se uma nova guia do navegador não for aberta, um banner ou um ícone na parte superior do navegador indicará que ele está impedindo que o site abra janelas pop-up. Clique no banner ou no ícone e selecione Permitir pop-ups.

Organize o Console de Gerenciamento da AWS para que ele seja exibido com essas instruções. De preferência, você deverá visualizar as duas guias do navegador ao mesmo tempo para seguir as etapas do laboratório.

 

Tarefa 1: Criar um volume do EBS
Nesta tarefa, você criará e anexará um volume do EBS a uma nova instância do EC2.

No Console de Gerenciamento da AWS, na barra Pesquisar, insira e escolha EC2 para abrir o Console de Gerenciamento do EC2.

No painel de navegação à esquerda, selecione Instâncias.

Uma instância do EC2 chamada Lab já foi iniciada para o laboratório.

Anote a Zona de Disponibilidade da instância Lab. Ela é semelhante à seguinte: us-west-2a

Dica: talvez você precise rolar para a direita para ver a coluna Zona de disponibilidade.

No painel de navegação à esquerda, em Elastic Block Store, escolha Volumes.

Você verá um volume existente (8 GiB) que a instância do EC2 está usando.

Escolha Criar volume e configure as seguintes opções:

Tipo de volume: selecione General Purpose SSD (gp2) (SSD de uso geral [gp2]).

Tamanho (GiB): insira 1. 
Observação: você pode estar impedido de criar volumes grandes.

Zona de Disponibilidade: escolha a mesma Zona de Disponibilidade da sua instância do EC2 (que, neste caso, é us-west-2a).

Na seção Tags (opcional), escolha Adicionar tag e configure as seguintes opções:

Chave: insira Name.

Valor: insira My Volume.

Selecione Criar volume. 

Um novo volume é exibido com o status Criando na coluna Estado do volume. Esse status logo muda para Disponível. Talvez seja necessário selecionar Atualizar  para ver o novo volume.

Dica: talvez você precise rolar para a direita para ver a coluna Estado do volume.

 

Tarefa 2: Anexar o volume a uma instância do EC2
Agora, anexe o novo volume a uma instância do EC2.

Selecione My Volume.

No menu Ações, escolha Anexar volume.

Na lista suspensa Instância, escolha a instância Lab.

O campo Nome do dispositivo está definido como /dev/sdf. Os comandos que você executará posteriormente neste laboratório incluem esse identificador de dispositivo. 

Selecione Associar volume.

O Estado do volume do seu novo volume agora é Em uso.

 

Tarefa 3: Conectar-se à instância do EC2 chamada Lab
Nesta tarefa, você usará o EC2 Instance Connect para se conectar à instância do EC2 chamada Lab. 

No Console de Gerenciamento da AWS, na barra Pesquisar, insira e escolha EC2 para abrir o Console de Gerenciamento do EC2.

No painel de navegação, selecione Instâncias.

Na lista de instâncias, selecione a instância Lab.

Selecione Conectar-se.

Na guia EC2 Instance Connect, selecione Conectar-se.

Essa opção abre uma nova guia do navegador com a janela do terminal do EC2 Instance Connect.

Observação: se preferir usar um cliente SSH para se conectar à instância do EC2, consulte as orientações em Conecte-se à sua instância do Linux.

Use essa janela do terminal para concluir as tarefas do laboratório. Se o terminal deixar de responder, atualize o navegador ou use as etapas desta tarefa para se conectar novamente.

 

Tarefa 4: Criar e configurar o sistema de arquivos
Nesta tarefa, você adicionará o novo volume a uma instância do Linux como um sistema de arquivos ext3 no ponto de montagem /mnt/data-store.

Para visualizar o armazenamento que está disponível na instância, no terminal do EC2 Instance Connect, execute o seguinte comando:

df -h
Você deve ver uma saída semelhante à seguinte:

Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        488M   60K  488M   1% /dev
tmpfs           497M     0  497M   0% /dev/shm
/dev/xvda1      7.8G  982M  6.7G  13% /
Esses resultados mostram o volume de disco original de 8 GB. Seu novo volume ainda não aparece.

Para criar um sistema de arquivos ext3 no novo volume, execute o seguinte comando:

sudo mkfs -t ext3 /dev/sdf
Para criar um diretório para montar o novo volume de armazenamento, execute o seguinte comando:

sudo mkdir /mnt/data-store
Para montar o novo volume, execute o seguinte comando:

sudo mount /dev/sdf /mnt/data-store
echo "/dev/sdf   /mnt/data-store ext3 defaults,noatime 1 2" | sudo tee -a /etc/fstab
A última linha desse comando garante que o volume seja montado mesmo depois que a instância for reiniciada.

Para visualizar o arquivo de configuração e ver a configuração na última linha, execute o seguinte comando:

cat /etc/fstab
Para exibir o armazenamento disponível novamente, execute o seguinte comando:

df -h
A saída agora contém uma linha adicional semelhante à seguinte: /dev/xvdf

Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        488M   60K  488M   1% /dev
tmpfs           497M     0  497M   0% /dev/shm
/dev/xvda1      7.8G  982M  6.7G  13% /
/dev/xvdf       976M  1.3M  924M   1% /mnt/data-store
Para criar um arquivo e adicionar texto no volume montado, execute o seguinte comando:

sudo sh -c "echo some text has been written > /mnt/data-store/file.txt"
Para verificar se o texto foi gravado no volume, execute o seguinte comando:

cat /mnt/data-store/file.txt
   A saída exibe o texto que esse comando copia para o arquivo. 

 

Tarefa 5: Criar um snapshot do Amazon EBS
Nesta tarefa, você criará um snapshot do volume do EBS.

Os snapshots do Amazon EBS são armazenados no Amazon Simple Storage Service (Amazon S3) para maior durabilidade. Novos volumes do EBS podem ser criados com base em snapshots para clonagem ou restauração de backups. Os snapshots do Amazon EBS também podem ser compartilhados entre contas da Amazon Web Services (AWS) ou copiados em Regiões AWS.

No Console de Gerenciamento do EC2, selecione Volumes e escolha My Volume.

No menu Ações, selecione Criar snapshot.

Na seção Tags, escolha Adicionar tag e configure as seguintes opções:

Chave: insira Name.

Valor: insira My Snapshot.

Selecione Criar snapshot.

No painel de navegação à esquerda, selecione Snapshots.

O Status do snapshot do seu snapshot é Pendente. Após a conclusão, o status muda para Concluído. Somente os blocos de armazenamento usados são copiados para os snapshots, portanto, os blocos vazios não ocupam espaço de armazenamento do snapshot.

Na janela do terminal do EC2 Instance Connect, para excluir o arquivo que você criou no volume, execute o seguinte comando:

sudo rm /mnt/data-store/file.txt
Observação: se o terminal não estiver respondendo, atualize o navegador ou reconecte-se conforme necessário.

Para verificar se o arquivo foi excluído, execute o seguinte comando:

ls /mnt/data-store/file.txt
É exibida a seguinte mensagem: ls: não é possível acessar /mnt/data-store/file.txt: Arquivo ou diretório inexistente

O arquivo foi excluído.

 

Tarefa 6: Restaurar o snapshot do Amazon EBS
Se você precisar recuperar os dados armazenados em um snapshot, poderá restaurar o snapshot em um novo volume do EBS.

Tarefa 6.1: Criar um volume usando o snapshot
No Console de Gerenciamento do EC2, selecione My Snapshot.

No menu Ações, selecione Criar volume com o snapshot.

Em Zona de disponibilidade, selecione a mesma Zona de Disponibilidade usada anteriormente.

Na seção Tags (opcional), escolha Adicionar tag e configure as seguintes opções:

Chave: insira Name.

Valor: insira Restored Volume.

Selecione Criar volume.

Para ver o novo volume, na navegação à esquerda, selecione Volumes.

O Status do volume do seu novo volume é Disponível.

Ao restaurar um snapshot para um novo volume, você também pode modificar a configuração, como alterar o tipo de volume, o tamanho ou a Zona de Disponibilidade.

Tarefa 6.2: Anexar o volume restaurado à instância do EC2
Selecione Restored Volume (Volume restaurado).

No menu Ações, escolha Anexar volume.

Na lista suspensa Instância, escolha a instância Lab.

O campo Nome do dispositivo está definido como /dev/sdg. Você usará esse identificador de dispositivo em uma tarefa posterior.

Selecione Associar volume.

O Status do volume do seu volume agora é Em uso.

Tarefa 6.3: Montar o volume restaurado
Para criar um diretório e montar o novo volume de armazenamento, no terminal do EC2 Instance Connect, execute o seguinte comando:

sudo mkdir /mnt/data-store2
Para montar o novo volume, execute o seguinte comando:

sudo mount /dev/sdg /mnt/data-store2
Para verificar se o volume que você montou tem o arquivo criado anteriormente, execute o seguinte comando:

ls /mnt/data-store2/file.txt
Você deve ver o arquivo file.txt.

 

Conclusão
Parabéns! Você concluiu as seguintes tarefas com êxito:

Criou um volume do EBS.

Anexou e montou o volume do EBS em uma instância do EC2.

Criou um snapshot de um volume do EBS.

Criou um volume do EBS com base em um snapshot.
