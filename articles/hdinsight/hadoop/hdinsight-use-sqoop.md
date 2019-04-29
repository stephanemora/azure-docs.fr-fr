---
title: Exécuter des tâches Apache Sqoop avec Azure HDInsight (Apache Hadoop)
description: Découvrez comment utiliser Azure PowerShell sur une station de travail pour exécuter des importations/exportations de Sqoop entre un cluster Hadoop et une base de données SQL Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: 6764d8d812789c9f54fa59e10b2a3e416e583a9c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129397"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Utiliser Apache Sqoop avec Hadoop dans HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Découvrez comment utiliser Apache Sqoop dans HDInsight pour importer et exporter des données entre un cluster HDInsight et une base de données SQL Azure.

Bien que Apache Hadoop est un choix naturel pour le traitement des données non structurées et semi-structurées, telles que les journaux et les fichiers, il peut également être nécessaire de traiter les données structurées stockées dans les bases de données relationnelles.

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) est un outil conçu pour transférer des données entre des clusters Hadoop et des bases de données relationnelles. Vous pouvez l’utiliser pour importer dans un système de fichiers distribués Hadoop (HDFS) des données depuis un système de gestion de base de données relationnelle (SGBDR), tel que SQ Server, MySQL ou Oracle, transformer ces données dans Hadoop avec MapReduce ou Apache Hive, et exporter à nouveau les données dans un SGBDR. Dans cet article, vous utilisez une base de données SQL Server pour votre base de données relationnelle.

> [!IMPORTANT]  
> Cet article définit un environnement de test pour effectuer le transfert de données. Puis, vous choisissez une méthode de transfert de données pour cet environnement à partir d’une des méthodes dans la section [des tâches Sqoop exécuter](#run-sqoop-jobs), plus loin.

Pour des versions Sqoop prises en charge sur des clusters HDInsight, consultez [quelles sont les nouveautés dans les versions de cluster fournies par HDInsight ?](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>Présentation du scénario

Le cluster HDInsight inclut des exemples de données. Vous utilisez les deux éléments suivants :

* Un fichier journal Apache Log4j, qui se trouve dans `/example/data/sample.log`. Ce fichier inclut les journaux d’activité suivants :

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* Une table Hive nommée `hivesampletable`, qui fait référence au fichier de données situé à `/hive/warehouse/hivesampletable`. Cette table contient des données relatives aux appareils mobiles.
  
  | Champ | Type de données |
  | --- | --- |
  | clientid |string |
  | querytime |string |
  | market |string |
  | deviceplatform |string |
  | devicemake |string |
  | devicemodel |string |
  | state |string |
  | country |string |
  | querydwelltime |double |
  | sessionid |bigint |
  | sessionpagevieworder |bigint |

Dans cet article, vous utilisez ces deux jeux de données pour tester l’importation Sqoop et exporter.

## <a name="create-cluster-and-sql-database"></a>Configuration d’environnement de test
Le cluster, de base de données SQL et d’autres objets sont créés via le portail Azure à l’aide d’un modèle Azure Resource Manager. Le modèle se trouve dans [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). Le modèle Resource Manager appelle un package bacpac pour déployer les schémas de table vers une base de données SQL.  Le package bacpac est situé dans le conteneur d’objets blob public https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Si vous souhaitez utiliser un conteneur privé pour stocker les fichiers bacpac, appliquez les valeurs suivantes au modèle :

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> L’importation à l’aide d’un modèle ou du portail Azure ne prend en charge que l’importation d’un fichier BACPAC à partir du stockage Blob Azure.

1. Sélectionnez l’image suivante pour ouvrir le modèle Resource Manager dans le portail Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Entrez les propriétés suivantes :

    |Champ |Valeur |
    |---|---|
    |Abonnement |Dans la liste déroulante, sélectionnez votre abonnement Azure.|
    |Groupe de ressources |Sélectionnez votre groupe de ressources dans la liste déroulante, ou créez-en un|
    |Lieu |Sélectionnez une région dans la liste déroulante.|
    |Nom du cluster |Entrez un nom pour le cluster Hadoop. Utilisez uniquement des lettres minuscules.|
    |Nom d’utilisateur de connexion au cluster |Conserver la valeur par défaut `admin`.|
    |Mot de passe de connexion au cluster |Entrez un mot de passe.|
    |Nom d’utilisateur SSH |Conserver la valeur par défaut `sshuser`.|
    |SSH mot de passe |Entrez un mot de passe.|
    |Connexion d’administrateur SQL |Conserver la valeur par défaut `sqluser`.|
    |Mot de passe administrateur SQL |Entrez un mot de passe.|
    |_artifacts emplacement | Utilisez la valeur par défaut, sauf si vous souhaitez utiliser votre propre fichier bacpac dans un autre emplacement.|
    |Jeton SAP d’emplacement _artifacts |Laisser vide.|
    |Nom de fichier Bacpac |Utilisez la valeur par défaut, sauf si vous souhaitez utiliser votre propre fichier bacpac.|
    |Lieu |Utilisez la valeur par défaut.|

    Le nom de serveur SQL Azure sera `<ClusterName>dbserver`. Le nom de la base de données sera `<ClusterName>db`. Le nom de compte de stockage par défaut sera `e6qhezrh2pdqu`.

3. **J’accepte les termes et conditions mentionnés ci-dessus**.

4. Sélectionnez **Achat**. Une nouvelle vignette intitulée Envoi du déploiement pour Déploiement de modèle s’affiche. La création du cluster et de la base de données SQL prend environ 20 minutes.

## <a name="run-sqoop-jobs"></a>Exécuter des tâches Sqoop

HDInsight peut exécuter des tâches Sqoop à l’aide de différentes méthodes. Utilisez la table suivante pour choisir la méthode qui vous convient, puis cliquez sur le lien pour obtenir une présentation détaillée.

| **Utilisez-le** si vous souhaitez... | ... un interpréteur de commandes **interactif** | ... un traitement par **lots** | ...depuis ce **système d’exploitation cluster** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, Unix, Mac OS X ou Windows |
| [Kit de développement logiciel (SDK) .NET pour Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (pour l’instant) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Limites

* Exportation en bloc : avec HDInsight sous Linux, le connecteur Sqoop utilisé pour exporter des données vers Microsoft SQL Server ou la base de données SQL Azure ne prend pas en charge les insertions en bloc.
* Traitement par lots : avec HDInsight sous Linux, lorsque vous utilisez le commutateur `-batch` pour effectuer des insertions, Sqoop effectue plusieurs insertions plutôt qu’un traitement par lots des opérations d’insertion.

## <a name="next-steps"></a>Étapes suivantes
Vous maîtrisez à présent l'utilisation de Sqoop. Pour plus d'informations, consultez les rubriques suivantes :

* [Utilisation d’Apache Hive avec HDInsight](../hdinsight-use-hive.md)
* [Utilisation d’Apache Pig avec HDInsight](../hdinsight-use-pig.md)
* [Chargez des données dans HDInsight](../hdinsight-upload-data.md) : Découvrez d’autres méthodes pour charger des données dans HDInsight ou le stockage Blob Azure.
