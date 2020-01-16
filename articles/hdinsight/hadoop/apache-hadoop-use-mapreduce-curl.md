---
title: Utiliser MapReduce et Curl avec Apache Hadoop dans HDInsight - Azure
description: Découvrez comment exécuter à distance des tâches MapReduce avec Apache Hadoop sur HDInsight à l’aide de Curl.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/27/2018
ms.openlocfilehash: 274d8dc80d9318aa3ddf4a904a5b623319ea01f4
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645002"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>Exécuter des tâches MapReduce avec Apache Hadoop sur HDInsight avec REST

Découvrez comment utiliser l’API REST WebHCat Apache Hive pour exécuter des tâches MapReduce sur un cluster Apache Hadoop sur HDInsight. Curl est utilisé pour illustrer comment interagir avec HDInsight en utilisant des requêtes HTTP brutes pour exécuter des tâches MapReduce.

> [!NOTE]  
> Si vous vous êtes déjà familiarisé avec l’utilisation de serveurs Hadoop sous Linux, mais que vous découvrez HDInsight, consultez le document [Ce qu’il faut savoir sur Apache Hadoop dans HDInsight sous Linux](../hdinsight-hadoop-linux-information.md).


## <a id="prereq"></a>Configuration requise

* Un cluster Hadoop sur HDInsight
* Windows PowerShell ou [Curl](https://curl.haxx.se/) et [jq](https://stedolan.github.io/jq/)

## <a id="curl"></a>Exécuter une tâche MapReduce

> [!NOTE]  
> Lorsque vous utilisez Curl ou toute autre communication REST avec WebHCat, vous devez authentifier les requêtes en fournissant le nom d’utilisateur et le mot de passe d’administrateur de cluster HDInsight. Vous devez utiliser le nom du cluster dans l’URI utilisé pour envoyer les requêtes au serveur.
>
> L’API REST est sécurisée à l’aide de l’ [authentification d’accès de base](https://en.wikipedia.org/wiki/Basic_access_authentication). Vous devez toujours effectuer les requêtes à l’aide de HTTPS pour vous assurer que vos informations d’identification sont envoyées en toute sécurité sur le serveur.

1. Pour définir la connexion de cluster qui est utilisée par les scripts de ce document, utilisez l’une des commandes suivantes :

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. Pour définir le nom du cluster, utilisez l’une des commandes suivantes :

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

    ```powershell
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    ```

3. À partir d’une ligne de commande, exécutez la commande suivante pour vérifier que vous pouvez vous connecter à votre cluster HDInsight.

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    La réponse reçue est similaire au JSON suivant :

        {"status":"ok","version":"v1"}

    Les paramètres utilisés dans cette commande sont les suivants :

   * **-u** : Indique le nom d’utilisateur et le mot de passe utilisés pour authentifier la demande
   * **-G** : Indique que cette opération est une requête GET

   Le début de l’URI, `https://CLUSTERNAME.azurehdinsight.net/templeton/v1`, est le même pour toutes les demandes.

4. Pour envoyer une tâche MapReduce, utilisez la commande suivante :

    ```bash
    JOBID=`curl -u $LOGIN -d user.name=$LOGIN -d jar=/example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar | jq .id`
    echo $JOBID
    ```

    ```powershell
    $reqParams = @{}
    $reqParams."user.name" = "admin"
    $reqParams.jar = "/example/jars/hadoop-mapreduce-examples.jar"
    $reqParams.class = "wordcount"
    $reqParams.arg = @()
    $reqParams.arg += "/example/data/gutenberg/davinci.txt"
    $reqparams.arg += "/example/data/output"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/mapreduce/jar" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    La fin de l’URI (/mapreduce/jar) indique à WebHCat que cette demande lance une tâche MapReduce à partir d’une classe dans un fichier jar. Les paramètres utilisés dans cette commande sont les suivants :

   * **-d** : étant donné que `-G` n’est pas utilisé, la demande passe par défaut à la méthode POST. `-d` spécifie les valeurs de données envoyées avec la demande.
     * **user.name** : Utilisateur qui exécute la commande
     * **jar** : L’emplacement du fichier jar contenant la classe à exécuter
     * **class** : La classe contenant la logique MapReduce
     * **arg** : Les arguments à transmettre à la tâche MapReduce. Dans le cas présent, le fichier texte d’entrée et le répertoire utilisés pour la sortie

   Cette commande doit retourner un ID de tâche qui peut être utilisé pour vérifier le statut de la tâche :

       job_1415651640909_0026

5. Pour vérifier le statut de la tâche, utilisez la commande suivante :

    ```bash
    curl -G -u $LOGIN -d user.name=$LOGIN https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/$JOBID | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    Si la tâche est terminée, l’état est `SUCCEEDED`.

   > [!NOTE]  
   > Cette requête Curl renvoie un document JSON avec des informations sur la tâche. Jq est utilisé pour récupérer uniquement la valeur d’état.

6. Lorsque l’état de la tâche passe à `SUCCEEDED`, vous pouvez récupérer les résultats depuis le Stockage Blob Azure. Le paramètre `statusdir` transmis avec la requête contient l’emplacement du fichier de sortie. Dans cet exemple, l’emplacement est `/example/curl`. Cette adresse stocke le résultat de la tâche dans le stockage par défaut du cluster : `/example/curl`.

Vous pouvez répertorier et télécharger ces fichiers à l’aide de l' [interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Pour plus d’informations sur l’utilisation d’objets blob à partir d’Azure CLI, consultez le document [Utiliser Azure CLI avec Stockage Azure](../../storage/common/storage-azure-cli.md#create-and-manage-blobs).

## <a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur les tâches MapReduce dans HDInsight :

* [Utiliser MapReduce avec Apache Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utiliser Apache Hive avec Apache Hadoop sur HDInsight](hdinsight-use-hive.md)
* [Utiliser Apache Pig avec Apache Hadoop sur HDInsight](hdinsight-use-pig.md)

Pour plus d’informations sur l’interface REST utilisée dans cet article, consultez la [Référence WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).
