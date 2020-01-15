---
title: Ajouter des comptes de stockage Azure supplémentaires à HDInsight
description: Découvrez comment ajouter des comptes de stockage Azure supplémentaires à un cluster HDInsight existant.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 86b9230dbdca82c5599c1839fd64bd3df4725051
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435573"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Ajouter des comptes de stockage supplémentaires à HDInsight

Découvrez comment utiliser des actions de script pour ajouter des *comptes* de stockage Azure supplémentaires à HDInsight. Les étapes décrites dans ce document permettent d’ajouter un *compte* de stockage à un cluster HDInsight existant basé sur Linux. Cet article s’applique au *comptes* de stockage (différents des comptes de stockage en cluster par défaut) et au stockage de base comme [Azure Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) et [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> Les informations contenues dans ce document portent sur l’ajout d’un ou de plusieurs comptes de stockage supplémentaires à un cluster après sa création. Pour plus d’informations sur l’ajout de comptes de stockage lors de la création du cluster, consultez [Configurer des clusters dans HDInsight avec Apache Hadoop, Apache Spark, Apache Kafka, etc](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Conditions préalables requises

* Un cluster Hadoop sur HDInsight. Consultez [Bien démarrer avec HDInsight sur Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Nom et clé du compte de stockage. Consultez [Gérer les clés d’accès au compte de stockage](../storage/common/storage-account-keys-manage.md).
* [Utilisez la bonne casse pour le nom du cluster](hdinsight-hadoop-manage-ambari-rest-api.md#identify-correctly-cased-cluster-name).
* Si vous utilisez PowerShell, vous avez besoin du module AZ.  Consultez [Vue d’ensemble d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
* Si vous n’avez pas installé Azure CLI, consultez [Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).
* Si vous utilisez une invite de commandes Bash ou Windows, vous aurez également besoin de **jq**, un processeur JSON en ligne de commande.  Voir [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/). Pour bash sur Ubuntu sur Windows 10, consultez [Sous-système Windows pour Linux - Guide d’installation pour Windows 10](https://docs.microsoft.com/windows/wsl/install-win10).

## <a name="how-it-works"></a>Fonctionnement

Le script utilise les paramètres suivants :

* __Nom du compte de Stockage Azure__ : nom du compte de stockage à ajouter au cluster HDInsight. Une fois le script exécuté, HDInsight est en mesure de lire et d’écrire des données stockées dans ce compte de stockage.

* __Clé du compte de Stockage Azure__ : clé qui donne accès au compte de stockage.

* __-p__ (facultatif) : si ce paramètre est spécifié, la clé n’est pas chiffrée et est stockée en texte brut dans le fichier core-site.xml.

Pendant le traitement, le script effectue les opérations suivantes :

* Si le compte de stockage existe déjà dans la configuration core-site.xml du cluster, le script s’arrête et aucune action supplémentaire n’est effectuée.

* Il vérifie que le compte de stockage existe et est accessible à l’aide de la clé.

* Il chiffre la clé à l’aide des informations d’identification du cluster.

* Il ajoute le compte de stockage dans le fichier core-site.xml.

* Arrête et redémarre les services [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) et [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html). L’arrêt et le redémarrage de ces services leur permettent d’utiliser le nouveau compte de stockage.

> [!WARNING]  
> L’utilisation d’un compte de stockage dans un autre emplacement que le cluster HDInsight n’est pas prise en charge.

## <a name="the-script"></a>Le script

__Emplacement du script__ : [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Exigences__ :  Le script doit être appliqué sur les __nœuds principaux__. Il n’est pas nécessaire de marquer ce script comme __Persistant__, car il met directement à jour la configuration Ambari du cluster.

## <a name="to-use-the-script"></a>Pour utiliser le script

Ce script peut être utilisé avec Azure PowerShell, Azure CLI ou le portail Azure.

### <a name="powershell"></a>PowerShell

En utilisant [Submit-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/submit-azhdinsightscriptaction). Remplacez `CLUSTERNAME`, `ACCOUNTNAME`, et `ACCOUNTKEY` par les valeurs appropriées.

```powershell
# Update these parameters
$clusterName = "CLUSTERNAME"
$parameters = "ACCOUNTNAME ACCOUNTKEY"

$scriptActionName = "addStorage"
$scriptActionUri = "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh"

# Execute script
Submit-AzHDInsightScriptAction `
    -ClusterName $clusterName `
    -Name $scriptActionName `
    -Uri $scriptActionUri `
    -NodeTypes "headnode" `
    -Parameters $parameters
```

### <a name="azure-cli"></a>Azure CLI

En utilisant [Submit-AzHDInsightScriptAction](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute).  Remplacez `CLUSTERNAME`, `RESOURCEGROUP`, `ACCOUNTNAME`, et `ACCOUNTKEY` par les valeurs appropriées.

```cli
az hdinsight script-action execute ^
    --name CLUSTERNAME ^
    --resource-group RESOURCEGROUP ^
    --roles headnode ^
    --script-action-name addStorage ^
    --script-uri "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh" ^
    --script-parameters "ACCOUNTNAME ACCOUNTKEY"
```

### <a name="azure-portal"></a>Portail Azure

Consultez [Appliquer une action de script sur un cluster en cours d’exécution](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster).

## <a name="known-issues"></a>Problèmes connus

### <a name="storage-firewall"></a>Pare-feu de stockage

Si vous choisissez de sécuriser votre compte de stockage à l’aide des restrictions de **pare-feu et réseaux virtuels** sur des **réseaux sélectionnés**, veillez à activer l’exception **Autoriser les services approuvés de Microsoft...** afin que HDInsight puisse accéder à votre compte de stockage.

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Comptes de stockage non affichés dans le portail ou les outils Azure

Lorsque vous affichez le cluster HDInsight sur le Portail Azure, les comptes de stockage ajoutés par cette action de script ne s’affichent pas quand vous sélectionnez l’entrée __Comptes de stockage__ sous __Propriétés__. Azure PowerShell et Azure CLI n’affichent pas non plus les comptes de stockage supplémentaires.

Les informations de stockage n’apparaissent pas car le script modifie uniquement la configuration core-site.xml du cluster. Ces informations ne sont pas utilisées pour récupérer les informations du cluster avec les API de gestion Azure.

Pour afficher les informations des comptes de stockage ajoutés au cluster à l’aide de ce script, utilisez l’API REST Ambari. Pour récupérer ces informations pour votre cluster, utilisez les commandes suivantes :

### <a name="powershell"></a>PowerShell

Remplacez `CLUSTERNAME` par le nom du cluster, avec la bonne casse. Remplacez `ACCOUNTNAME` par le nom réel. Lorsque vous y êtes invité, entrez le mot de passe de connexion au cluster.

```powershell
# Update values
$clusterName = "CLUSTERNAME"
$accountName = "ACCOUNTNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$accountName.blob.core.windows.net"
```

### <a name="bash"></a>bash

Remplacez `CLUSTERNAME` par le nom du cluster, avec la bonne casse. Remplacez `PASSWORD` par le mot de passe de l’administrateur du cluster. Remplacez `STORAGEACCOUNT` par le nom réel du compte de stockage.

```bash
export clusterName="CLUSTERNAME"
export password='PASSWORD'
export storageAccount="STORAGEACCOUNT"

export ACCOUNTNAME='"'fs.azure.account.key.$storageAccount.blob.core.windows.net'"'

export configVersion=$(curl --silent -u admin:$password -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_service_config_versions/HDFS" \
| jq ".Clusters.desired_service_config_versions.HDFS[].service_config_version")

curl --silent -u admin:$password -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" \
| jq ".items[].configurations[].properties[$ACCOUNTNAME] | select(. != null)"
```

### <a name="cmd"></a>cmd

Remplacez `CLUSTERNAME` par le nom du cluster avec la bonne casse, dans les deux scripts. Tout d’abord, identifiez la version de configuration du service en cours d’utilisation en entrant la commande suivante :

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" | ^
jq-win64 ".Clusters.desired_service_config_versions.HDFS[].service_config_version"
```

Remplacez `ACCOUNTNAME` par le nom réel du compte de stockage. Puis, remplacez `4` par la version de configuration du service réelle et entrez la commande :

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=4" | ^
jq-win64 ".items[].configurations[].properties["""fs.azure.account.key.ACCOUNTNAME.blob.core.windows.net"""] | select(. != null)"
```

---

Les informations renvoyées par cette commande sont semblables au texte suivant :

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Ce texte est un exemple de clé chiffrée, utilisée pour accéder au compte de stockage.

### <a name="unable-to-access-storage-after-changing-key"></a>Impossible d’accéder au stockage après avoir modifié la clé

Si vous modifiez la clé d’un compte de stockage, HDInsight ne peut plus accéder au compte de stockage. HDInsight utilise une copie de la clé mise en cache dans le fichier core-site.xml pour le cluster. Cette copie mise en cache doit être mise à jour pour correspondre à la nouvelle clé.

Si vous exécutez de nouveau l’action de script, la clé n’est __pas__ mise à jour, car le script vérifie s’il existe déjà une entrée pour le compte de stockage. S’il existe déjà une entrée, aucune modification n’est apportée.

Pour contourner ce problème, vous devez supprimer l’entrée existante pour le compte de stockage. Procédez comme suit pour supprimer l’entrée existante :

> [!IMPORTANT]  
> La rotation de la clé de stockage pour le compte de stockage principal attaché à un cluster n’est pas prise en charge.

1. Dans un navigateur web, ouvrez l’interface utilisateur web Ambari pour votre cluster HDInsight. L’URI est `https://CLUSTERNAME.azurehdinsight.net`. Remplacez `CLUSTERNAME` par le nom de votre cluster.

    Lorsque vous y êtes invité, saisissez le nom d’utilisateur et le mot de passe de connexion HTTP de votre cluster.

2. Dans la liste des services située à gauche de la page, sélectionnez __HDFS__. Ensuite, sélectionnez l’onglet __Configurations__ au centre de la page.

3. Dans la __Filtrer...__ , entrez une valeur de __fs.azure.account__. Des entrées sont renvoyées pour tous les comptes de stockage supplémentaires qui ont été ajoutés au cluster. Il existe deux types d’entrées : __keyprovider__ et __key__. Les deux contiennent le nom du compte de stockage dans le nom de la clé.

    Voici des exemples d’entrées pour un compte de stockage nommé __mystorage__ :

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Après avoir identifié les clés du compte de stockage à supprimer, utilisez l’icône « - » rouge à droite de l’entrée pour procéder à la suppression. Ensuite, cliquez sur le bouton __Enregistrer__ pour enregistrer vos modifications.

5. Une fois les modifications enregistrées, utilisez l’action de script pour ajouter le compte de stockage et la nouvelle valeur de clé du cluster.

### <a name="poor-performance"></a>Problèmes de performances

Si le compte de stockage se trouve dans une région différente de celle du cluster HDInsight, il se peut que vous rencontriez des problèmes de performances. Lorsque vous accédez à des données stockées dans une autre région, le trafic réseau sort du centre de données Azure régional, puis est acheminé via l’Internet public, ce qui peut entraîner de la latence.

### <a name="additional-charges"></a>Frais supplémentaires

Si le compte de stockage se trouve dans une région différente de celle du cluster HDInsight, vous remarquerez peut-être des frais de sortie supplémentaires sur votre facture Azure. Des frais de sortie sont appliqués lorsque des données quittent un centre de données régional. Ces frais sont appliqués même si le trafic est destiné à un autre centre de données Azure situé dans une autre région.

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à ajouter des comptes de stockage supplémentaires à un cluster HDInsight existant. Pour plus d’informations sur les actions de script, consultez [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une d’action de script](hdinsight-hadoop-customize-cluster-linux.md).
