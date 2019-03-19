---
title: Utiliser Apache Pig avec PowerShell dans HDInsight - Azure
description: Découvrez comment envoyer des travaux Apache Pig à un cluster Apache Hadoop sur HDInsight à l’aide d’Azure PowerShell.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 69a45a0c2c21ffafde8a4b366e1f3e90b7c8f59a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58012621"
---
# <a name="use-azure-powershell-to-run-apache-pig-jobs-with-hdinsight"></a>Utiliser Azure PowerShell pour exécuter des tâches Apache Pig avec HDInsight

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Ce document fournit un exemple d’utilisation d’Azure PowerShell pour envoyer des travaux Apache Pig à un Hadoop Apache sur un cluster HDInsight. Pig vous permet d’écrire des tâches MapReduce en utilisant un langage (Pig Latin) qui modélise les transformations de données, plutôt que de mapper et de réduire les fonctions.

> [!NOTE]  
> Ce document ne fournit pas une description détaillée de ce que font les instructions Pig Latin utilisées dans les exemples. Pour plus d’informations sur le langage Pig Latin utilisé dans cet exemple, consultez [Utiliser Apache Pig avec Apache Hadoop sur HDInsight](hdinsight-use-pig.md).

## <a id="prereq"></a>Configuration requise

* **Un cluster Azure HDInsight**

  > [!IMPORTANT]  
  > Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Un poste de travail sur lequel est installé Azure PowerShell**.

## <a id="powershell"></a>Exécuter un travail Apache Pig

Azure PowerShell propose des *applets de commande* qui vous permettent d'exécuter à distance des tâches Pig sur HDInsight. En interne, PowerShell utilise des appels REST vers [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) en cours d’exécution sur le cluster HDInsight.

Les applets de commande suivantes sont utilisées lors de l’exécution des tâches Pig sur un cluster HDInsight à distance :

* **Connect-AzureRmAccount** : Authentifie Azure PowerShell dans votre abonnement Azure.
* **New-AzureRmHDInsightPigJobDefinition** : Crée une *définition de travail* à l’aide des instructions Pig Latin spécifiées.
* **Start-AzureRmHDInsightJob** : Envoie la définition de travail à HDInsight et démarre le travail. Un objet *job* est retourné.
* **Wait-AzureRmHDInsightJob** : Utilise l’objet de travail pour vérifier l’état du travail. Il attend que la tâche soit terminée ou que le délai d’attente soit dépassé.
* **Get-AzureRmHDInsightJobOutput** : Utilisé pour récupérer la sortie du travail.

Les étapes suivantes montrent comment utiliser ces cmdlets pour exécuter une tâche sur votre cluster HDInsight.

1. À l’aide d’un éditeur, enregistrez le code suivant sous **pigjob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-pig/use-pig.ps1?range=5-51)]

1. Ouvrez une invite de commandes Windows PowerShell. Accédez au répertoire du fichier **pigjob.ps1** , puis utilisez la commande suivante pour exécuter le script :

        .\pigjob.ps1

    Vous êtes invité à vous connecter à votre abonnement Azure. Ensuite, vous devez fournir le nom et le mot de passe du compte HTTPs/Admin pour le cluster HDInsight.

2. Une fois la tâche terminée, un texte similaire à celui présenté ci-dessous doit s’afficher :

        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="troubleshooting"></a>Résolution des problèmes

Si aucune information n’est retournée lorsque la tâche est terminée, affichez les journaux d’erreurs. Pour afficher les informations d’erreur pour cette tâche, ajoutez la commande suivante à la fin du fichier **pigjob.ps1** , enregistrez-le et exécutez-le à nouveau.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Cette applet de commande retourne les informations écrites sur STDERR lors de l’exécution de la tâche.

## <a id="summary"></a>Résumé
Comme vous pouvez le voir, Azure PowerShell offre un moyen facile d’exécuter des tâches Pig sur un cluster HDInsight, de surveiller l’état de la tâche et de récupérer la sortie.

## <a id="nextsteps"></a>Étapes suivantes
Pour obtenir des informations générales sur Pig dans HDInsight :

* [Utiliser Apache Pig avec Apache Hadoop sur HDInsight](hdinsight-use-pig.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utiliser Apache Hive avec Apache Hadoop sur HDInsight](hdinsight-use-hive.md)
* [Utiliser MapReduce avec Apache Hadoop sur HDInsight](hdinsight-use-mapreduce.md)
