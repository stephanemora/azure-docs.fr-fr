---
title: Utiliser Apache Hive avec PowerShell dans HDInsight - Azure
description: Utilisez PowerShell pour exécuter des requêtes Hive dans Apache Hadoop sur HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 77d2d0b5b9f994668abdd02640a9c6d5f463e137
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360756"
---
# <a name="run-apache-hive-queries-using-powershell"></a>Exécuter des requêtes Apache Hive avec PowerShell
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Ce document fournit un exemple d’utilisation d’Azure PowerShell dans le mode Groupe de ressources Azure pour exécuter des requêtes Hive sur un cluster Apache Hadoop sur HDInsight.

> [!NOTE]  
> Ce document ne fournit pas de description détaillée de ce que font les instructions HiveQL utilisées dans les exemples. Pour plus d’informations sur le langage HiveQL utilisé dans cet exemple, consultez [Utiliser Apache Hive avec Apache Hadoop sur HDInsight](hdinsight-use-hive.md).

## <a name="prerequisites"></a>Conditions préalables

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Un cluster Apache Hadoop Linux sur HDInsight version 3.4 ou ultérieure.

  > [!IMPORTANT]  
  > Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Un client sur lequel est installé Azure PowerShell.

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-a-hive-query"></a>Exécution d'une tâche Hive

Azure PowerShell fournit des *cmdlets* qui vous permettent d'exécuter à distance des requêtes Hive sur HDInsight. En interne, les applets de commande effectuent des appels REST à [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) sur le cluster HDInsight.

Les applets de commande suivants sont utilisés lors de l'exécution de requêtes Hive sur un cluster à distance HDInsight :

* `Connect-AzAccount`: Authentifie Azure PowerShell dans votre abonnement Azure.
* `New-AzHDInsightHiveJobDefinition`: Crée une *définition de travail* à l’aide des instructions HiveQL spécifiées.
* `Start-AzHDInsightJob`: Envoie la définition de travail à HDInsight et démarre le travail. Un objet *job* est retourné.
* `Wait-AzHDInsightJob`: Utilise l’objet de travail pour vérifier l’état du travail. Il attend que la tâche soit terminée ou que le délai d’attente soit dépassé.
* `Get-AzHDInsightJobOutput`: Utilisé pour récupérer la sortie du travail.
* `Invoke-AzHDInsightHiveJob`: Utilisé pour exécuter les instructions HiveQL. Cette applet de commande bloque la fin de la requête, puis retourne les résultats.
* `Use-AzHDInsightCluster`: Définit le cluster actuel à utiliser pour la commande `Invoke-AzHDInsightHiveJob`.

Les étapes suivantes montrent comment utiliser ces cmdlets pour exécuter une tâche sur votre cluster HDInsight :

1. À l’aide d’un éditeur, enregistrez le code suivant sous `hivejob.ps1`.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Ouvrez une invite de commandes **Azure PowerShell** . Accédez au répertoire du fichier `hivejob.ps1`, puis utilisez la commande suivante pour exécuter le script :

        .\hivejob.ps1

    Quand le script s’exécute, vous devez entrer le nom du cluster et les informations d’identification du compte HTTPS/Admin. Vous pouvez également être invité à vous connecter à votre abonnement Azure.

3. Lorsque la tâche est terminée, des informations similaires au texte suivant s’affichent :

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Comme mentionné précédemment, `Invoke-Hive` peut être utilisé pour exécuter une requête et attendre la réponse. Pour voir comment fonctionne Invoke-Hive, utilisez le script suivant :

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    La sortie ressemble au texte suivant :

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]  
   > Pour les requêtes HiveQL plus longues, vous pouvez utiliser les fichiers de script HiveQL de PowerShell ou la cmdlet **Here-Strings** Azure PowerShell. L’extrait suivant montre comment utiliser la cmdlet `Invoke-Hive` pour exécuter un fichier de script HiveQL. Ce dernier doit être chargé dans wasb://.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Pour plus d'informations sur **Here-Strings**, consultez la page <a href="https://technet.microsoft.com/library/ee692792.aspx" target="_blank">Utilisation du fichier de script Here-Strings de PowerShell</a>.

## <a name="troubleshooting"></a>Résolution de problèmes

Si aucune information n’est retournée lorsque la tâche est terminée, affichez les journaux d’erreurs. Pour afficher les informations d’erreur relatives à cette tâche, ajoutez les commandes suivantes à la fin du fichier `hivejob.ps1`, enregistrez-le, puis réexécutez-le.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Cette applet de commande retourne les informations écrites sur STDERR lors de l’exécution de la tâche.

## <a name="summary"></a>Résumé

Comme vous pouvez le constater, Azure PowerShell permet d'exécuter facilement des requêtes Hive sur un cluster HDInsight, de surveiller l'état de la tâche et de récupérer le résultat.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des informations générales sur Hive dans HDInsight :

* [Utiliser Apache Hive avec Apache Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utiliser Apache Pig avec Apache Hadoop sur HDInsight](hdinsight-use-pig.md)
* [Utiliser MapReduce avec Apache Hadoop sur HDInsight](hdinsight-use-mapreduce.md)
