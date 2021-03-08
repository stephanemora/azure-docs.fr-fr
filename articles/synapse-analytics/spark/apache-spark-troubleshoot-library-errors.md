---
title: Résoudre les erreurs d’installation de bibliothèque
description: Ce didacticiel fournit une vue d’ensemble de la résolution des erreurs d’installation de bibliothèque.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: 57e9d0c584600a8fac90499d72cfac1620052603
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694918"
---
# <a name="troubleshoot-library-installation-errors"></a>Résoudre les erreurs d’installation de bibliothèque 
Pour que vos applications disposent d’un code tiers ou généré en local, vous pouvez installer une bibliothèque sur l’un de vos pools Apache Spark serverless. Les packages répertoriés dans le fichier requirements.txt sont téléchargés à partir de PyPi au démarrage du pool. Ce fichier de configuration requise est utilisé chaque fois qu’une instance Spark est créée à partir de ce pool Spark. Une fois qu’une bibliothèque est installée pour un pool Spark, elle est disponible pour toutes les sessions utilisant le même pool. 

Dans certains cas, il se peut qu’une bibliothèque n’apparaisse pas dans votre pool Apache Spark. Cela se produit généralement lorsqu’une erreur est survenue dans le fichier requirements.txt fourni ou dans les bibliothèques spécifiées. En cas d’erreur dans le processus d’installation de la bibliothèque, le pool Apache Spark restaure les bibliothèques spécifiées dans le runtime de base Synapse.

L’objectif de ce document est de fournir des problèmes courants et de vous aider à déboguer les erreurs d’installation de bibliothèque.

## <a name="force-update-your-apache-spark-pool"></a>Forcer la mise à jour de votre pool Apache Spark
Lorsque vous mettez à jour les bibliothèques dans votre pool Apache Spark, ces modifications sont récupérées une fois que le pool a redémarré. Si vous avez des travaux actifs, ces travaux continuent à s’exécuter sur la version d’origine du pool Spark.

Vous pouvez forcer l’application des modifications en sélectionnant l’option **Forcer les nouveaux paramètres**. Ce paramètre met fin à toutes les sessions actives pour le pool Spark sélectionné. Une fois les sessions terminées, vous devez attendre le redémarrage du pool. 

![Ajouter des bibliothèques Python](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Ajouter des bibliothèques Python")

## <a name="track-installation-progress"></a>Vérifier la progression de l’installation
Un travail Spark réservé au système démarre chaque fois qu’un pool est mis à jour avec un nouvel ensemble de bibliothèques. Ce travail Spark permet de surveiller l’état de l’installation de la bibliothèque. En cas d’échec de l’installation suite à des conflits avec la bibliothèque ou à d’autres problèmes, l’état précédent ou l’état par défaut du pool Spark est rétabli. 

De plus, les utilisateurs peuvent consulter les journaux d’installation pour identifier les conflits de dépendance ou vérifier quelles bibliothèques ont été installées lors de la mise à jour du pool.

Pour afficher ces journaux :
1. Accédez à la liste des applications Spark dans l’onglet **Surveiller**. 
2. Sélectionnez la tâche de l’application Spark du système qui correspond à la mise à jour de votre pool. Ces tâches système s’exécutent sous le titre *SystemReservedJob-LibraryManagement*.
   ![Capture d’écran mettant en évidence la tâche de bibliothèque réservée au système.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Afficher la tâche de bibliothèque système")
3. Affichez les journaux du **pilote** et **stdout**. 
4. Parmi les résultats, vous verrez les journaux relatifs à l’installation de vos packages.
    ![Capture d’écran mettant en évidence les résultats de la tâche de bibliothèque réservée au système.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Afficher la progression de la tâche de bibliothèque système")

## <a name="validate-your-permissions"></a>Valider vos autorisations
Pour installer et mettre à jour des bibliothèques, vous devez disposer des autorisations **Contributeur aux données Blob du stockage** ou **Propriétaire des données Blob du stockage** sur le compte de stockage Azure Data Lake Storage Gen2 principal qui est lié à l’espace de travail Azure Synapse Analytics.

Pour confirmer que vous disposez de ces autorisations, vous pouvez exécuter le code suivant :

```python
from pyspark.sql.types import StructType,StructField, StringType, IntegerType
data2 = [("James","Smith","Joe","4355","M",3000),
    ("Michael","Rose","Edward","40288","F",4000)
  ]

schema = StructType([ \
    StructField("firstname",StringType(),True), \
    StructField("middlename",StringType(),True), \
    StructField("lastname",StringType(),True), \
    StructField("id", StringType(), True), \
    StructField("gender", StringType(), True), \
    StructField("salary", IntegerType(), True) \
  ])
 
df = spark.createDataFrame(data=data2,schema=schema)

df.write.csv("abfss://<<ENTER NAME OF FILE SYSTEM>>@<<ENTER NAME OF PRIMARY STORAGE ACCOUNT>>.dfs.core.windows.net/validate_permissions.csv")

```
Si une erreur s’affiche, vous ne disposez probablement pas des autorisations requises. Pour découvrir comment obtenir les autorisations requises, consultez ce document: [Attribuez des autorisations Contributeur aux données Blob du stockage ou Propriétaire des données Blob du stockage](../../storage/common/storage-auth-aad-rbac-portal.md#assign-an-azure-built-in-role).

En outre, si vous exécutez un pipeline, le fichier MSI de l’espace de travail doit également posséder des autorisations Propriétaire des données Blob du stockage ou Contributeur aux données Blob du stockage. Pour apprendre à accorder cette autorisation à l’identité de votre espace de travail, consultez : [Octroyez des autorisations à une identité managée de l’espace de travail](../security/how-to-grant-workspace-managed-identity-permissions.md).

## <a name="check-the-environment-configuration-file"></a>Vérifier le fichier config de l’environnement
Vous pouvez utiliser le fichier config d’un environnement pour mettre à niveau l’environnement Conda. Si vous souhaitez obtenir la liste des formats de fichier pris en charge pour la gestion des pools Python, cliquez [ici](./apache-spark-manage-python-packages.md).

Il est important de noter les restrictions suivantes :
   -  Le contenu du fichier de spécifications ne doit pas inclure de lignes ou de caractères vides supplémentaires. 
   -  Le [runtime Synapse](apache-spark-version-support.md) comprend un ensemble de bibliothèques qui sont préinstallées sur chaque pool Apache Spark serverless. Vous ne pouvez pas supprimer ou désinstaller les packages préinstallés sur le runtime de base.
   -  La modification de la version de PySpark, Python, Scala/Java, .NET ou Spark n’est pas prise en charge.
   -  Les bibliothèques Python avec étendue de session prennent uniquement en charge les fichiers avec une extension YML.

## <a name="validate-wheel-files"></a>Valider des fichiers de roues
Les pools Apache Spark serverless Synapse sont basés sur la distribution Linux. Lors du téléchargement et de l’installation de fichiers de roues directement à partir de PyPI, veillez à sélectionner la version qui repose sur Linux et qui s’exécute sur la même version de Python que le pool Spark.

>[!IMPORTANT]
>Des packages personnalisés peuvent être ajoutés ou modifiés entre les sessions. Toutefois, vous devez attendre le redémarrage du pool et de la session pour voir le package mis à jour.

## <a name="check-for-dependency-conflicts"></a>Rechercher des conflits de dépendance
 En général, la résolution de dépendance Python peut être difficile à gérer. Pour vous aider à déboguer les conflits de dépendance localement, vous pouvez créer votre propre environnement virtuel basé sur le runtime Synapse et valider vos modifications.

Pour recréer l’environnement et valider vos mises à jour :
 1. [Téléchargez](https://github.com/Azure-Samples/Synapse/blob/main/Spark/Python/base_environment.yml) le modèle pour recréer localement le runtime Synapse. Il peut y avoir de légères différences entre le modèle et l’environnement Synapse réel.
   
 2. Créez un environnement virtuel en suivant les [instructions ci-dessous](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html). Cet environnement vous permet de créer une installation Python isolée avec la liste de bibliothèques spécifiée. 
    
    ```
    conda myenv create -f environment.yml
    conda activate myenv
    ```
   
 3. Utilisez ``pip install -r <provide your req.txt file>`` pour mettre à jour l’environnement virtuel avec vos packages spécifiés. Si l’installation génère une erreur, il peut y avoir un conflit entre ce qui est préinstallé dans le runtime de base Synapse et ce qui est spécifié dans le fichier d’exigences fourni. Ces conflits de dépendance doivent être résolus afin d’extraire les bibliothèques mises à jour sur votre pool Apache Spark serverless.

>[!IMPORTANT]
>Vous risquez de rencontrer des problèmes en cas d’utilisation simultanée de pip et de conda. Si vous combinez pip et conda, nous vous invitons à suivre les [meilleures pratiques recommandées](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html#using-pip-in-an-environment).

## <a name="next-steps"></a>Étapes suivantes
- Afficher les bibliothèques par défaut : [Prise en charge des versions d’Apache Spark](apache-spark-version-support.md)