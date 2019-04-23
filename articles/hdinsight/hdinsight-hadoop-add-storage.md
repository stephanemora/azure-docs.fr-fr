---
title: Ajouter des comptes de stockage Azure supplémentaires à HDInsight
description: Découvrez comment ajouter des comptes de stockage Azure supplémentaires à un cluster HDInsight existant.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: hrasheed
ms.openlocfilehash: 54d7a0bf0474db4a9f9d74a1f694f10ef1be91cc
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792260"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Ajouter des comptes de stockage supplémentaires à HDInsight

Découvrez comment utiliser les actions de script pour ajouter du stockage Azure supplémentaire *comptes* à HDInsight. Les étapes décrites dans ce document ajoutent un stockage *compte* à un cluster HDInsight basé sur Linux existant. Cet article s’applique au stockage *comptes* (pas par défaut cluster compte de stockage) et le stockage pas supplémentaire comme [Gen1 de stockage Azure Data Lake](hdinsight-hadoop-use-data-lake-store.md) et [Azure Data Lake Storage Gen2 ](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> Ce document porte sur l’ajout de stockage supplémentaire à un cluster après que celui-ci a été créé. Pour plus d’informations sur l’ajout de comptes de stockage lors de la création du cluster, consultez [Configurer des clusters dans HDInsight avec Apache Hadoop, Apache Spark, Apache Kafka, etc](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Conditions préalables

* Un cluster Hadoop sur HDInsight. Consultez [prise en main HDInsight sous Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Nom de compte de stockage et la clé. Consultez [gérer les paramètres de compte de stockage dans le portail Azure](../storage/common/storage-account-manage.md).
* [Nom du cluster correctement casse](hdinsight-hadoop-manage-ambari-rest-api.md#identify-correctly-cased-cluster-name).
* Si vous utilisez PowerShell, vous aurez besoin du module AZ.  Consultez [vue d’ensemble d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
* Si vous n’avez pas installé l’interface CLI, consultez [les Interface de ligne de commande (CLI) Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).
* Si vous utilisez l’interpréteur de commandes ou d’une invite de commandes windows, vous devez également **jq**, un processeur JSON en ligne de commande.  Voir [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/). Pour bash sur Ubuntu sur Windows 10, consultez [sous-système Windows pour Linux Installation Guide pour Windows 10](https://docs.microsoft.com/windows/wsl/install-win10).

## <a name="how-it-works"></a>Fonctionnement

Le script utilise les paramètres suivants :

* __Nom du compte de stockage Azure__ : nom du compte de stockage à ajouter au cluster HDInsight. Une fois le script exécuté, HDInsight est en mesure de lire et d’écrire des données stockées dans ce compte de stockage.

* __Clé du compte de stockage Azure__ : clé qui donne accès au compte de stockage.

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

__Conditions requises__ :  Le script doit être appliqué sur les __nœuds principaux__. Vous n’avez pas besoin de marquer ce script comme __Persistant__, car il met directement à jour la configuration Ambari.

## <a name="to-use-the-script"></a>Pour utiliser le script

Ce script peut être utilisé à partir d’Azure PowerShell, Azure CLI ou le portail Azure.

### <a name="powershell"></a>PowerShell

À l’aide de [AzHDInsightScriptAction soumettre](https://docs.microsoft.com/powershell/module/az.hdinsight/submit-azhdinsightscriptaction). Remplacez `CLUSTERNAME`, `ACCOUNTNAME`, et `ACCOUNTKEY` avec les valeurs appropriées.

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

À l’aide de [az-action de script hdinsight exécuter](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute).  Remplacez `CLUSTERNAME`, `RESOURCEGROUP`, `ACCOUNTNAME`, et `ACCOUNTKEY` avec les valeurs appropriées.

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

Consultez [appliquer une action de script sur un cluster en cours d’exécution](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster).

## <a name="known-issues"></a>Problèmes connus

### <a name="storage-firewall"></a>Pare-feu de stockage

Si vous choisissez votre compte de stockage avec le **les pare-feux et réseaux virtuels** restrictions sur **réseaux sélectionnés**, veillez à activer l’exception **autoriser fiables Microsoft Services...**  afin que HDInsight puisse accéder à votre compte de stockage.

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Comptes de stockage non affichés dans le portail ou les outils Azure

Lorsque vous affichez le cluster HDInsight dans le portail Azure, les comptes de stockage ajoutés à l’aide de cette action de script ne s’affichent pas lorsque vous sélectionnez l’entrée __Comptes de stockage__ sous __Propriétés__. Azure PowerShell et l’interface de ligne de commande Azure n’affichent pas non plus les comptes de stockage supplémentaires.

Les informations de stockage n’apparaissent pas car le script modifie uniquement la configuration core-site.xml du cluster. Ces informations ne sont pas utilisées lors de la récupération des informations du cluster à l’aide des API de gestion Azure.

Pour afficher les informations des comptes de stockage ajoutés au cluster à l’aide de ce script, utilisez l’API REST Ambari. Pour récupérer ces informations pour votre cluster, utilisez les commandes suivantes :

### <a name="powershell"></a>PowerShell

Remplacez `CLUSTERNAME` avec le nom du cluster correctement la casse. Tout d’abord identifier la version de configuration de service en cours d’utilisation en entrant la commande suivante :

```powershell
# getting service_config_version
$clusterName = "CLUSTERNAME"

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version
```

Remplacez `ACCOUNTNAME` avec les noms réels. Puis remplacez `4` avec la version de configuration du service et entrez la commande. Lorsque vous y êtes invité, entrez le mot de passe de connexion de cluster.

```powershell
# Update values
$accountName = "ACCOUNTNAME"
$version = 4

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$version" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$accountName.blob.core.windows.net"
```

### <a name="bash"></a>bash
Remplacez `myCluster` avec le nom du cluster correctement la casse.

```bash
export CLUSTERNAME='myCluster'

curl --silent -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" \
| jq ".Clusters.desired_service_config_versions.HDFS[].service_config_version" 
```

Remplacez `myAccount` par le nom de compte de stockage réelle. Puis remplacez `4` avec la version de configuration du service et entrez la commande :

```bash
export ACCOUNTNAME='"fs.azure.account.key.myAccount.blob.core.windows.net"'
export VERSION='4'

curl --silent -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=$VERSION" \
| jq ".items[].configurations[].properties[$ACCOUNTNAME] | select(. != null)"
```

### <a name="cmd"></a>cmd

Remplacez `CLUSTERNAME` avec le nom du cluster correctement la casse dans les deux scripts. Tout d’abord identifier la version de configuration de service en cours d’utilisation en entrant la commande suivante :

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" | ^
jq-win64 ".Clusters.desired_service_config_versions.HDFS[].service_config_version" 
```

Remplacez `ACCOUNTNAME` par le nom de compte de stockage réelle. Puis remplacez `4` avec la version de configuration du service et entrez la commande :

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

Si vous exécutez de nouveau l’action de script, la clé n’est __pas__ mise à jour, car le script vérifie s’il existe déjà une entrée pour le compte de stockage. Si une entrée existe déjà, aucune modification n’est apportée.

Pour contourner ce problème, vous devez supprimer l’entrée existante pour le compte de stockage. Procédez comme suit pour supprimer l’entrée existante :

1. Dans un navigateur web, ouvrez l’interface utilisateur web Ambari pour votre cluster HDInsight. L’URI est `https://CLUSTERNAME.azurehdinsight.net`. Remplacez `CLUSTERNAME` par le nom de votre cluster.

    Lorsque vous y êtes invité, saisissez le nom d’utilisateur et le mot de passe de connexion HTTP de votre cluster.

2. Dans la liste des services située à gauche de la page, sélectionnez __HDFS__. Ensuite, sélectionnez l’onglet __Configurations__ au centre de la page.

3. Dans la __Filtrer...__, entrez une valeur de __fs.azure.account__. Des entrées sont renvoyées pour tous les comptes de stockage supplémentaires qui ont été ajoutés au cluster. Il existe deux types d’entrées : __keyprovider__ et __key__. Les deux contiennent le nom du compte de stockage dans le nom de la clé.

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

Vous avez découvert comment ajouter des comptes de stockage Azure supplémentaires à un cluster HDInsight existant. Pour plus d’informations sur les actions de script, consultez [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une d’action de script](hdinsight-hadoop-customize-cluster-linux.md).
