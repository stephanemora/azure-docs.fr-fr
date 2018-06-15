---
title: 'Démarrage rapide : démarrer avec Apache Kafka - Azure HDInsight | Microsoft Docs'
description: Dans ce guide de démarrage rapide, vous allez apprendre à créer un cluster Apache Kafka sur Azure HDInsight à l’aide d’Azure PowerShell. Vous découvrez également les rubriques, les abonnés et les consommateurs de Kafka.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: mvc,hdinsightactive
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/16/2018
ms.author: larryfr
ms.openlocfilehash: ce5555068ac4a9160657b5c2f204172c828bea50
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33779206"
---
# <a name="quickstart-create-a-kafka-on-hdinsight-cluster"></a>Démarrage rapide : créer un cluster Kafka dans HDInsight

Kafka est une plateforme de streaming distribuée, open source. Elle est souvent utilisée comme répartiteur de messages, car elle fournit des fonctionnalités similaires à une file d’attente de messages de publication/d’abonnement. 

Dans ce guide de démarrage rapide, vous allez apprendre à créer un cluster [Apache Kafka](https://kafka.apache.org) à l’aide d’Azure PowerShell. Vous apprendrez également à utiliser les utilitaires inclus pour envoyer et recevoir des messages à l’aide de Kafka.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

> [!IMPORTANT]
> L’API Kafka est accessible uniquement par les ressources à l’intérieur du même réseau virtuel. Dans ce guide de démarrage rapide, vous accédez au cluster directement à l’aide de SSH. Pour connecter à d’autres services, réseaux ou machines virtuelles à Kafka, vous devez tout d’abord créer un réseau virtuel, puis créer les ressources au sein du réseau.
>
> Pour plus d’informations, consultez le document [Se connecter à Kafka à l’aide d’un réseau virtuel](apache-kafka-connect-vpn-gateway.md).

## <a name="prerequisites"></a>Prérequis


* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

* Azure PowerShell. Pour plus d’informations, consultez le document [Installer et configurer Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

* Un client SSH. Les étapes décrites dans ce document utilisent SSH pour se connecter au cluster.

    La commande `ssh` est fournie par défaut sur les systèmes Linux, Unix et macOS. Sur Windows 10, utilisez l’une des méthodes suivantes pour installer la commande `ssh` :

    * Utilisez [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart). Le shell cloud fournit la commande `ssh` et peut être configuré pour utiliser Bash ou PowerShell comme environnement du shell.

    * [Installez le sous-système Windows pour Linux](https://docs.microsoft.com/windows/wsl/install-win10). Les distributions Linux disponibles via Microsoft Store fournissent la commande `ssh`.

    > [!IMPORTANT]
    > Les étapes décrites dans ce document supposent que vous utilisez un des clients SSH mentionnés ci-dessus. Si vous utilisez un autre client SSH et que vous rencontrez des problèmes, consultez la documentation de votre client SSH.
    >
    > Pour plus d’informations, consultez le document [Utiliser SSH avec HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec l’applet de commande `Login-AzureRmAccount` et suivez les instructions à l’écran.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources Azure avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. L’exemple suivant vous invite à saisir le nom et l’emplacement, puis crée un nouveau groupe de ressources :

```powershell
$resourceGroup = Read-Input -Prompt "Enter the resource group name"
$location = Read-Input -Prompt "Enter the Azure region to use"

New-AzureRmResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

Alors que Kafka sur HDInsight utilise des disques gérés par Azure pour stocker les données Kafka, le cluster utilise également le stockage Azure pour stocker des informations telles que les journaux. Utilisez [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) pour créer un nouveau compte de stockage.

```powershell
$storageName = Read-Host -Prompt "Enter the storage account name"

New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroup `
        -Name $storageName `
        -Type Standard_LRS `
        -Location $location
```

HDInsight stocke les données du compte de stockage dans un conteneur d’objets blob. Utilisez [New-AzureStorageContainer](/powershell/module/Azure.Storage/New-AzureStorageContainer) pour créer un nouveau conteneur.

```powershell
$containerName = Read-Host -Prompt "Enter the container name"

$storageKey = (Get-AzureRmStorageAccountKey `
                -ResourceGroupName $resourceGroup `
                -Name $storageName)[0].Value
$storageContext = New-AzureStorageContext `
                    -StorageAccountName $storageName `
                    -StorageAccountKey $storageKey
New-AzureStorageContainer -Name $containerName -Context $storageContext 
```

## <a name="create-a-kafka-cluster"></a>Création d’un cluster Kafka

Créez un cluster Kafka sur le cluster HDInsight avec [New-AzureRmHDInsightCluster](/powershell/module/AzureRM.HDInsight/New-AzureRmHDInsightCluster).

```powershell
$clusterName = Read-Host -Prompt "Enter the name of the Kafka cluster"
$httpCredential = Get-Credential -Message "Enter the cluster login credentials" `
    -UserName "admin"
$sshCredentials = Get-Credential -Message "Enter the SSH user credentials"

$numberOfWorkerNodes = "4"
$clusterVersion = "3.6"
$clusterType="Kafka"
$clusterOS="Linux"
$disksPerNode=2

New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroup `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $numberOfWorkerNodes `
        -ClusterType $clusterType `
        -OSType $clusterOS `
        -Version $clusterVersion `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$storageAccount.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageKey `
        -DefaultStorageContainer $clusterName `
        -SshCredential $sshCredentials `
        -DisksPerWorkerNode $disksPerNode
```

> [!WARNING]
> La création du cluster HDInsight peut prendre jusqu’à 20 minutes.

> [!TIP]
> Le paramètre `-DisksPerWorkerNode` configure l’évolutivité de Kafka sur HDInsight. Kafka sur HDInsight utilise le disque local des machines virtuelles dans le cluster pour stocker les données. Kafka fait une utilisation intensive des E/S, c’est pourquoi le service [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) est utilisé pour fournir un haut débit et davantage de stockage à chaque nœud. 
>
> Le type de disque géré peut être soit __Standard__ (HDD), soit __Premium__ (SSD). Le type de disque dépend de la taille de la machine virtuelle utilisée par les nœuds de travail (répartiteurs Kafka). Les disques Premium sont utilisés automatiquement avec les machines virtuelles séries DS et GS. Tous les autres types de machines virtuelles utilisent des disques Standard. Vous pouvez définir le type de machine virtuelle à l’aide du paramètre `-WorkerNodeSize`. Pour plus d’informations sur les paramètres, consultez la documentation [New-AzureRmHDInsightCluster](/powershell/module/AzureRM.HDInsight/New-AzureRmHDInsightCluster).


> [!IMPORTANT]
> Si vous envisagez d’utiliser plus de 32 nœuds de travail lors de la création du cluster ou en faisant évoluer le cluster après sa création, vous devez utiliser le paramètre `-HeadNodeSize` pour spécifier une taille de machine virtuelle avec au moins 8 cœurs et 14 Go de RAM.
>
> Pour plus d’informations sur les tailles de nœud et les coûts associés, consultez [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="connect-to-the-cluster"></a>Connexion au cluster

1. Pour vous connecter au nœud principal du cluster Kafka principal, utilisez la commande suivante. Remplacez `sshuser` par le nom d’utilisateur SSH. Remplacer `mykafka` par le nom de votre cluster Kafka

    ```bash
    ssh sshuser@mykafka-ssh.azurehdinsight.net
    ```

2. Lorsque vous vous connectez pour la première fois au cluster, votre client SSH peut afficher un message d’avertissement, indiquant que l’authenticité de l’hôte ne peut pas être établie. À l’invite, tapez __oui__, puis appuyez sur __Entrée__ pour ajouter l’hôte à la liste de serveurs approuvés de votre client SSH.

3. Lorsque vous y êtes invité, entrez le mot de passe de l’utilisateur SSH.

Une fois que vous êtes connecté, des informations similaires au texte suivant s’affichent :

```text
Authorized uses only. All activity may be monitored and reported.
Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.13.0-1011-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

83 packages can be updated.
37 updates are security updates.



Welcome to Kafka on HDInsight.

Last login: Thu Mar 29 13:25:27 2018 from 108.252.109.241
ssuhuser@hn0-mykafk:~$
```

## <a id="getkafkainfo"></a>Obtention des informations sur Zookeeper et l’hôte du répartiteur

Lorsque vous utilisez Kafka, vous devez connaître les hôtes *Zookeeper* et du *répartiteur*. Ces hôtes sont utilisés avec l’API Kafka et la plupart des utilitaires fournis avec Kafka.

Dans cette section, vous obtenez les informations sur l’hôte à partir de l’API REST Ambari sur le cluster.

1. À partir de la connexion SSH avec le cluster, utilisez la commande suivante pour installer l’utilitaire `jq`. Cet utilitaire est utilisé pour analyser des documents JSON et est utile lors de la récupération des informations sur l’hôte :
   
    ```bash
    sudo apt -y install jq
    ```

2. Pour définir une variable d’environnement associée au nom du cluster, utilisez la commande suivante :

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

    Lorsque vous y êtes invité, entrez le nom du cluster Kafka.

3. Pour définir une variable d’environnement avec les informations d’hôte Zookeeper, utilisez la commande suivante :

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Lorsque vous y êtes invité, entrez le mot de passe du compte de connexion au cluster (pas du compte SSH).

    > [!NOTE]
    > Cette commande récupère tous les hôtes Zookeeper, puis retourne uniquement les deux premières entrées. Ceci vous offre une redondance au cas où l’un des hôtes est inaccessible.

4. Pour vérifier que la variable d’environnement est correctement définie, utilisez la commande suivante :

    ```bash
     echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    ```

    Cette commande renvoie des informations semblables au texte suivant :

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

5. Pour définir une variable d’environnement avec les informations d’hôte de répartiteur Kafka, utilisez la commande suivante :

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

    Lorsque vous y êtes invité, entrez le mot de passe du compte de connexion au cluster (pas du compte SSH).

6. Pour vérifier que la variable d’environnement est correctement définie, utilisez la commande suivante :

    ```bash   
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    Cette commande renvoie des informations semblables au texte suivant :
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

## <a name="manage-kafka-topics"></a>Gérer les rubriques Kafka

Kafka stocke les flux de données dans des *rubriques*. Vous pouvez utiliser l’utilitaire `kafka-topics.sh` pour gérer les rubriques.

* **Pour créer une rubrique**, utilisez la commande suivante dans la connexion SSH :

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Cette commande se connecte à Zookeeper par le biais des informations d’hôte stockées dans `$KAFKAZKHOSTS`. Elle crée ensuite une rubrique Kafka nommée **test**. 

    * Les données stockées dans cette rubrique sont partitionnées en huit partitions.

    * Chaque partition est répliquée sur trois nœuds worker du cluster.

        > [!IMPORTANT]
        > Si vous avez créé le cluster dans une région Azure qui fournit trois domaines d’erreur, utilisez un facteur de réplication de 3. Sinon, utilisez un facteur de réplication de 4.
        
        Dans les régions avec trois domaines d’erreur, un facteur de réplication de 3 permet de répartir les réplicas entre les domaines d’erreur. Dans les régions avec deux domaines d’erreur, un facteur de réplication de quatre répartit uniformément les réplicas entre les domaines.
        
        Pour plus d’informations sur le nombre de domaines d’erreur dans une région, consultez le document [Disponibilité des machines virtuelles Linux](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

        > [!IMPORTANT] 
        > Kafka n’est pas informé des domaines d’erreur Azure. Lors de la création de réplicas de partitions pour les rubriques, il ne peut pas distribuer les réplicas correctement pour la haute disponibilité.

        Pour garantir une haute disponibilité, utilisez l’[outil de rééquilibrage de partitions de Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Cet outil doit être exécuté à partir d’une connexion SSH pour le nœud principal de votre cluster Kafka.

        Pour garantir la haute disponibilité de vos données Kafka, vous devez rééquilibrer les réplicas de partition de votre rubrique lorsque :

        * Vous créez une rubrique ou une partition

        * Vous mettez à l’échelle un cluster

* **Pour répertorier les rubriques**, utilisez la commande suivante :

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Cette commande répertorie les rubriques disponibles sur le cluster Kafka.

* **Pour supprimer une rubrique**, utilisez la commande suivante :

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic topicname --zookeeper $KAFKAZKHOSTS
    ```

    Cette commande supprime la rubrique nommée `topicname`.

    > [!WARNING]
    > Si vous supprimez la rubrique `test` créée précédemment, alors vous devez la recréer. Elle sera utilisée ultérieurement dans ce document.

Pour plus d’informations sur les commandes disponibles avec l’utilitaire `kafka-topics.sh`, utilisez la commande suivante :

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh
```

## <a name="produce-and-consume-records"></a>Produire et consommer des enregistrements

Kafka stocke les *enregistrements* dans des rubriques. Les enregistrements sont produits par des *producteurs*, et utilisés par des *consommateurs*. Les producteurs et les consommateurs communiquent avec le service *répartiteur (broker) de Kafka*. Chaque nœud worker dans votre cluster HDInsight est un hôte de répartiteur Kafka.

Pour stocker les enregistrements dans la rubrique test créée précédemment, puis les lire à l’aide d’un consommateur, procédez comme suit :

1. Pour écrire des enregistrements dans la rubrique, vous devez utiliser l’utilitaire `kafka-console-producer.sh` à partir de la connexion SSH :
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    Une fois cette commande exécutée, vous accédez à une ligne vide.

2. Saisissez un message texte sur la ligne vide et appuyez sur Entrée. Entrez quelques messages de cette manière, puis utilisez **Ctrl + C** pour revenir à l’invite de commandes normale. Chaque ligne est envoyée en tant qu’enregistrement distinct vers la rubrique Kafka.

3. Pour lire des enregistrements à partir de la rubrique, vous devez utiliser l’utilitaire `kafka-console-consumer.sh` à partir de la connexion SSH :
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```
   
    Cette commande permet de récupérer les enregistrements à partir de la rubrique et de les afficher. L’utilisation de `--from-beginning` indique au consommateur de démarrer à partir du début du flux, de manière à récupérer tous les enregistrements.

    > [!NOTE]
    > Si vous utilisez une version antérieure de Kafka, remplacez `--bootstrap-server $KAFKABROKERS` par `--zookeeper $KAFKAZKHOSTS`.

4. Utilisez la combinaison __Ctrl + C__ pour arrêter le consommateur.

Vous pouvez également créer les producteurs et consommateurs par programme. Pour obtenir un exemple d’utilisation de cette API, consultez le document [Kafka Producer and Consumer API with HDInsight (API Kafka Producer and Consumer avec HDInsight)](apache-kafka-producer-consumer-api.md).

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, vous pouvez utiliser la commande [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources, HDInsight et toutes les ressources associées.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

> [!WARNING]
> La facturation du cluster HDInsight démarre à la création du cluster et s’arrête à sa suppression. La facturation est effectuée au prorata des minutes écoulées. Par conséquent, vous devez toujours supprimer votre cluster lorsqu’il n’est plus utilisé.
> 
> La suppression d’un cluster Kafka sur HDInsight supprime toutes les données stockées dans Kafka.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Utilisation d’Apache Spark avec Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)