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
ms.openlocfilehash: 60ea97ea2df271f867febec3fa0f0826a18dbbbf
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100416491"
---
# <a name="troubleshoot-library-installation-errors"></a>Résoudre les erreurs d’installation de bibliothèque 
Pour que vos applications disposent d’un code tiers ou généré en local, vous pouvez installer une bibliothèque sur l’un de vos pools Apache Spark serverless. Les packages répertoriés dans le fichier requirements.txt sont téléchargés à partir de PyPi au démarrage du pool. Ce fichier de configuration requise est utilisé chaque fois qu’une instance Spark est créée à partir de ce pool Spark. Une fois qu’une bibliothèque est installée pour un pool Spark, elle est disponible pour toutes les sessions utilisant le même pool. 

Dans certains cas, vous constaterez peut-être que la bibliothèque que vous essayez d’installer n’apparaît pas dans votre pool Apache Spark. Ce cas se produit souvent lorsqu’une erreur est survenue dans le fichier requirements.txt fourni ou dans les bibliothèques spécifiées. En cas d’erreur dans le processus d’installation de la bibliothèque, le pool Apache Spark restaure les bibliothèques spécifiées dans le runtime de base Synapse.

L’objectif de ce document est de fournir des problèmes courants et de vous aider à déboguer les erreurs d’installation de bibliothèque.

## <a name="force-update-your-apache-spark-pool"></a>Forcer la mise à jour de votre pool Apache Spark
Lorsque vous mettez à jour les bibliothèques dans votre pool Apache Spark, ces modifications sont récupérées une fois que le pool a redémarré. Si vous avez des travaux actifs, ces travaux continuent à s’exécuter sur la version d’origine du pool Spark.

Vous pouvez forcer l’application des modifications en sélectionnant l’option **Forcer les nouveaux paramètres**. Ce paramètre met fin à toutes les sessions actives pour le pool Spark sélectionné. Une fois les sessions terminées, vous devez attendre le redémarrage du pool. 

![Ajouter des bibliothèques Python](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Ajouter des bibliothèques Python")

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
Si une erreur s’affiche, vous ne disposez probablement pas des autorisations requises. Pour découvrir comment obtenir les autorisations requises, consultez ce document: [Attribuez des autorisations Contributeur aux données Blob du stockage ou Propriétaire des données Blob du stockage](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal#assign-an-azure-built-in-role).

En outre, si vous exécutez un pipeline, le fichier MSI de l’espace de travail doit également posséder des autorisations Propriétaire des données Blob du stockage ou Contributeur aux données Blob du stockage. Pour apprendre à accorder cette autorisation à l’identité de votre espace de travail, consultez : [Octroyez des autorisations à une identité managée de l’espace de travail](../security/how-to-grant-workspace-managed-identity-permissions.md).

## <a name="check-the-requirements-file"></a>Consulter le fichier de spécifications
Un fichier ***requirements.txt*** (sortie de la commande pip freeze) permet de mettre à niveau l’environnement virtuel. Ce fichier est au format décrit dans la documentation de référence [pip freeze](https://pip.pypa.io/en/stable/reference/pip_freeze/).

Il est important de noter les restrictions suivantes :
   -  Le nom du package PyPI doit être mentionné avec une version exacte. 
   -  Le contenu du fichier de spécifications ne doit pas inclure de lignes ou de caractères vides supplémentaires. 
   -  Le [runtime Synapse](apache-spark-version-support.md) comprend un ensemble de bibliothèques qui sont préinstallées sur chaque pool Apache Spark serverless. Les packages préinstallés sur le runtime de base ne peuvent pas passer à une version antérieure. Les packages ne peuvent être qu’ajoutés ou mis à niveau.
   -  La modification de la version de PySpark, Python, Scala/Java, .NET ou Spark n’est pas prise en charge.

L’extrait de code suivant montre le format obligatoire du fichier de configuration requise.

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

## <a name="validate-wheel-files"></a>Valider des fichiers de roues
Les pools Apache Spark serverless Synapse sont basés sur la distribution Linux. Lors du téléchargement et de l’installation de fichiers de roues directement à partir de PyPI, veillez à sélectionner la version qui repose sur Linux et qui s’exécute sur la même version de Python que le pool Spark.

>[!IMPORTANT]
>Des packages personnalisés peuvent être ajoutés ou modifiés entre les sessions. Toutefois, vous devrez attendre le redémarrage du pool et de la session pour voir le package mis à jour.

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

## <a name="next-steps"></a>Étapes suivantes
- Afficher les bibliothèques par défaut : [Prise en charge des versions d’Apache Spark](apache-spark-version-support.md)

