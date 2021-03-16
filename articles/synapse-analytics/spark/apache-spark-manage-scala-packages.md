---
title: Gérer des bibliothèques Scala et Java pour Apache Spark
description: Découvrez comment ajouter et gérer des bibliothèques Scala et Java dans Azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: c70ecc4fc5469d728bc12d47024585ccf00ff98e
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098704"
---
# <a name="manage-scala-and-java-packages-for-apache-spark-in-azure-synapse-analytics"></a>Gérer des packages Scala et Java pour Apache Spark dans Azure Synapse Analytics

Les bibliothèques fournissent du code réutilisable que vous pouvez inclure dans vos programmes ou projets. 

Plusieurs raisons peuvent vous amener à mettre à jour l’environnement de votre pool Apache Spark serverless. Par exemple, il se peut que :
- l’une de vos dépendances de base ait publié une nouvelle version.
- vous ayez besoin d’un autre package pour entraîner votre modèle Machine Learning ou préparer vos données.
- vous ayez trouvé un package plus performant et n’ayez plus besoin de l’ancien.

Pour que vos applications disposent d’un code tiers ou généré en local, vous pouvez installer une bibliothèque sur l’un de vos pools Apache Spark serverless ou sur votre session de notebook. Dans cet article, vous découvrirez comment gérer les packages Scala et Java.

## <a name="default-installation"></a>Installation par défaut
Apache Spark dans Azure Synapse Analytics possède un ensemble complet de bibliothèques pour les tâches courantes d’engineering données, de préparation des données, d’apprentissage automatique et de visualisation des données. Pour la liste complète des bibliothèques, consultez la [prise en charge des versions d’Apache Spark](apache-spark-version-support.md). 

Lors du démarrage d’une instance Spark, ces bibliothèques sont automatiquement incluses. Vous pouvez ajouter des packages Scala/Java supplémentaires au niveau du pool Spark et de la session.

## <a name="workspace-packages"></a>Packages d’espace de travail
Les packages de l’espace de travail peuvent être des fichiers jar personnalisés ou privés. Vous pouvez télécharger ces packages dans votre espace de travail et les attribuer par la suite à un pool Spark spécifique.

Pour ajouter des packages d’espace de travail :
1. Accédez à l’onglet **Gérer** > **Packages d’espace de travail**.
2. Chargez vos fichiers jar à l’aide du sélecteur de fichiers.
3. Une fois les fichiers chargés dans l’espace de travail Azure Synapse, vous pouvez ajouter ces fichiers jar à un pool Apache Spark donné.

![Capture d’écran mettant en évidence les packages d’espace de travail.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Afficher les packages d’espace de travail")

## <a name="pool-libraries"></a>Bibliothèques de pools
Une fois que vous avez identifié les packages Scala et Java que vous souhaitez utiliser pour votre application Spark, vous pouvez les installer dans un pool Spark. Les bibliothèques de pools sont disponibles pour tous les notebooks et toutes les tâches qui s’exécutent sur le pool.

Pour mettre à jour les bibliothèques du pool Spark, accédez à Azure Synapse Studio ou au Portail Azure. Ici, vous pouvez sélectionner les bibliothèques d’espace de travail à installer. 

Une fois les modifications enregistrées, un travail Spark exécute l’installation et met en cache l’environnement obtenu pour une réutilisation ultérieure. Une fois le travail terminé, les nouveaux travaux Spark ou les nouvelles sessions de notebook utilisent les bibliothèques de pools mises à jour. 

> [!IMPORTANT]
> - Si le package que vous installez est volumineux ou si son installation prend beaucoup de temps, cela affecte le temps de démarrage de l’instance Spark.
> - La modification de la version de PySpark, Python, Scala/Java, .NET ou Spark n’est pas prise en charge.

#### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>Gérer les packages à partir d’Azure Synapse Studio ou du Portail Azure
Vous pouvez gérer les bibliothèques de pools Spark depuis Azure Synapse Studio ou le Portail Azure. 

Pour mettre à jour un pool Spark ou y ajouter des bibliothèques :
1. Accédez à votre espace de travail Azure Synapse Analytics à partir du portail Azure.

    Si vous effectuez une mise à jour depuis le **Portail Azure** :

    - Sous la section **Ressources Synapse**, sélectionnez l’onglet **Pools Apache Spark** et sélectionnez un pool Spark dans la liste.
     
    - Sélectionnez les **packages** dans la section **Paramètres** du pool Spark.
  
    ![Capture d’écran mettant en évidence le bouton Charger le fichier config de l’environnement.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Ajouter des bibliothèques Python")
   
    Si vous effectuez une mise à jour depuis **Synapse Studio** :
    - Sélectionnez **Gérer** à partir du volet de navigation principal, puis sélectionnez **Pools Apache Spark**.

    - Sélectionnez la section **Packages** pour un pool Spark spécifique.
    ![Capture d’écran mettant en évidence l’option de téléchargement de la configuration de l’environnement depuis Studio.](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "Ajouter des bibliothèques Python à partir de Studio")
   
2. Pour ajouter des fichiers jar, accédez à la section **Packages de l’espace de travail** à ajouter à votre pool. 
3. Une fois vos modifications enregistrées, une tâche système se déclenche pour installer et mettre en cache les bibliothèques spécifiées. Ce processus permet de réduire le temps de démarrage global de la session. 
4. Une fois la tâche terminée, toutes les nouvelles sessions récupèrent les bibliothèques de pool mises à jour.

> [!IMPORTANT]
> En sélectionnant l’option **Forcer les nouveaux paramètres**, vous terminez toutes les sessions actives pour le pool Spark sélectionné. Une fois les sessions terminées, vous devez attendre le redémarrage du pool. 
>
> Si ce paramètre est désactivé, vous devez attendre que la session Spark en cours se termine ou l’arrêter manuellement. Une fois la session terminée, vous devez laisser le pool redémarrer.

#### <a name="track-installation-progress-preview"></a>Vérifier la progression de l’installation (préversion)
Un travail Spark réservé au système démarre chaque fois qu’un pool est mis à jour avec un nouvel ensemble de bibliothèques. Ce travail Spark permet de surveiller l’état de l’installation de la bibliothèque. En cas d’échec de l’installation suite à des conflits avec la bibliothèque ou à d’autres problèmes, l’état précédent ou l’état par défaut du pool Spark est rétabli. 

De plus, les utilisateurs peuvent consulter les journaux d’installation pour identifier les conflits de dépendance ou vérifier quelles bibliothèques ont été installées lors de la mise à jour du pool.

Pour afficher ces journaux :
1. Accédez à la liste des applications Spark dans l’onglet **Surveiller**. 
2. Sélectionnez la tâche de l’application Spark du système qui correspond à la mise à jour de votre pool. Ces tâches système s’exécutent sous le titre *SystemReservedJob-LibraryManagement*.
   ![Capture d’écran mettant en évidence la tâche de bibliothèque réservée au système.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Afficher la tâche de bibliothèque système")
3. Affichez les journaux du **pilote** et **stdout**. 
4. Parmi les résultats, vous verrez les journaux relatifs à l’installation de vos packages.
    ![Capture d’écran mettant en évidence les résultats de la tâche de bibliothèque réservée au système.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Afficher la progression de la tâche de bibliothèque système")

## <a name="session-scoped-libraries"></a>Bibliothèques avec étendue de session 
Outre les bibliothèques de pools, vous pouvez également spécifier des bibliothèques avec étendue de session au démarrage d’une session de notebook.  Les bibliothèques avec étendue de session vous permettent de spécifier et d’utiliser des packages jar dans une session de notebook uniquement. 

Lorsque vous utilisez des bibliothèques avec étendue de session, il est important de garder à l’esprit les points suivants :
   - Lorsque vous installez des bibliothèques avec étendue de session, seul le notebook actif peut accéder aux bibliothèques spécifiées. 
   - Ces bibliothèques n’ont aucune incidence sur les autres sessions ou tâches qui utilisent le même pool Spark. 
   - Ces bibliothèques sont installées en plus du runtime de base et des bibliothèques de pools. 
   - Les bibliothèques de notebooks sont prioritaires.

Pour spécifier des packages Java ou Scala avec étendue de session, vous pouvez utiliser l’option ```%%configure``` :

```scala
%%configure -f
{
    "conf": {
        "spark.jars": "abfss://<<file system>>@<<storage account>.dfs.core.windows.net/<<path to JAR file>>",
    }
}
```

Nous vous recommandons d’exécuter la fonctionnalité %%configure au début de votre notebook. Consultez ce [document](https://github.com/cloudera/livy#request-body) pour obtenir la liste complète des paramètres valides.

## <a name="next-steps"></a>Étapes suivantes
- Afficher les bibliothèques par défaut : [Prise en charge des versions d’Apache Spark](apache-spark-version-support.md)
- Résoudre les erreurs d’installation de bibliothèque : [Résoudre les erreurs de bibliothèque](apache-spark-troubleshoot-library-errors.md)
