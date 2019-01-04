---
title: Utiliser Apache Hadoop Pig avec REST dans HDInsight - Azure
description: Découvrez comment utiliser REST pour exécuter des tâches Pig Latin sur un cluster Apache Hadoop dans HDInsight Azure.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: 855ee1b7396be97c6529480b8fa8200bb8167ee6
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53434006"
---
# <a name="run-apache-pig-jobs-with-apache-hadoop-on-hdinsight-by-using-rest"></a>Exécuter des travaux Apache Pig avec Apache Hadoop sur HDInsight à l’aide de REST

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Découvrez comment exécuter des tâches Apache Pig Latin en effectuant des demandes REST pour un cluster Azure HDInsight. Curl est utilisé pour illustrer comment interagir avec HDInsight en utilisant l’API REST WebHCat.

> [!NOTE]  
> Si vous connaissez déjà l’utilisation de serveurs Apache Hadoop basés sur Linux, mais pas HDInsight, consultez la rubrique [Informations sur l’utilisation de HDInsight sur Linux](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Configuration requise

* Un cluster Azure HDInsight (Hadoop sur HDInsight) Windows ou Linux

  > [!IMPORTANT]  
  > Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [Curl](https://curl.haxx.se/)

* [jq](https://stedolan.github.io/jq/)

## <a id="curl"></a>Exécuter des travaux Apache Pig à l’aide de Curl


> [!NOTE]
> L’API REST est sécurisée à l’aide de l’ [authentification d’accès de base](https://en.wikipedia.org/wiki/Basic_access_authentication). Pour garantir l’envoi en toute sécurité de vos informations d’identification sur le serveur, procédez toujours aux requêtes via le protocole HTTP sécurisé (HTTPS).
>
> Lorsque vous utilisez les commandes de cette section, remplacez `USERNAME` par l’utilisateur à authentifier sur le cluster et `PASSWORD` par le mot de passe du compte d’utilisateur. Remplacez `CLUSTERNAME` par le nom de votre cluster.
>


1. À partir d’une ligne de commande, exécutez la commande suivante pour vérifier que vous pouvez vous connecter à votre cluster HDInsight.

    ```bash
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    La réponse JSON suivante doit s’afficher :

        {"status":"ok","version":"v1"}

    Les paramètres utilisés dans cette commande sont les suivants :

    * **-u** : Nom d’utilisateur et mot de passe utilisés pour authentifier la demande
    * **-G** : Indique que la demande est une demande GET

     Le début de l’URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, est le même pour toutes les requêtes. Le chemin d’accès, **/status**, indique que la demande doit retourner le statut de WebHCat (également appelé Templeton) au serveur.

2. Utilisez le code suivant pour soumettre une tâche Pig Latin au cluster :

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'/example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="/example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig
    ```

    Les paramètres utilisés dans cette commande sont les suivants :

    * **-d** : Comme `-G` n’est pas utilisé, la demande passe par défaut à la méthode POST. `-d` spécifie les valeurs de données envoyées avec la demande.

    * **user.name** : Utilisateur qui exécute la commande
    * **execute** : Instructions Pig Latin à exécuter
    * **statusdir** : Répertoire dans lequel l’état de ce travail est écrit

    > [!NOTE]  
    > Notez que les espaces dans les instructions Pig Latin sont remplacées par le caractère `+` avec Curl.

    Cette commande doit retourner un ID de tâche qui peut être utilisé pour vérifier le statut de la tâche, par exemple :

        {"id":"job_1415651640909_0026"}

3. Pour vérifier le statut de la tâche, utilisez la commande suivante

     ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

     Remplacez `JOBID` par la valeur renvoyée à l’étape précédente. Par exemple, si la valeur de retour était `{"id":"job_1415651640909_0026"}`, alors `JOBID` est `job_1415651640909_0026`.

    Si la tâche est terminée, l’état est **TERMINÉ**.

    > [!NOTE]  
    > Cette requête Curl retourne un document JSON (JavaScript Object Notation) avec des informations sur la tâche et jq est utilisé pour récupérer uniquement la valeur d’état.

## <a id="results"></a>Affichage des résultats

Une fois que le statut de la tâche est passé à **TERMINÉ**, vous pouvez récupérer les résultats. Le paramètre `statusdir` transmis avec la requête contient l’emplacement du fichier de sortie. Dans notre cas `/example/pigcurl`.

HDInsight peut utiliser le stockage Azure ou Azure Data Lake Storage comme magasin de données par défaut. Il existe différentes façons d’obtenir les données en fonction de celles que vous souhaitez utiliser. Pour plus d’informations, consultez la section relative au stockage, dans le document [Informations sur HDInsight sous Linux](../hdinsight-hadoop-linux-information.md#hdfs-azure-storage-and-data-lake-store).

## <a id="summary"></a>Résumé

Comme illustré dans ce document, vous pouvez utiliser les demandes HTTP brutes pour exécuter, surveiller et afficher les résultats de tâches Pig sur votre cluster HDInsight.

Pour plus d’informations sur l’interface REST utilisée dans cet article, consultez la [Référence WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur Pig dans HDInsight :

* [Utiliser Apache Pig avec Apache Hadoop sur HDInsight](hdinsight-use-pig.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utiliser Apache Hive avec Apache Hadoop sur HDInsight](hdinsight-use-hive.md)
* [Utiliser MapReduce avec Apache Hadoop sur HDInsight](hdinsight-use-mapreduce.md)
