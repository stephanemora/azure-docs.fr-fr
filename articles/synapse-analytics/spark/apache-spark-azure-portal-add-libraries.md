---
title: Ajouter et gérer des bibliothèques pour Apache Spark
description: Découvrez comment ajouter et gérer des bibliothèques utilisées par Apache Spark dans Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: c0d34d80df77b5c6fcdefc39b3bc3b1619a93705
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496251"
---
# <a name="add-and-manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Ajouter et gérer des bibliothèques pour Apache Spark dans Azure Synapse Analytics

Apache Spark dépend de nombreuses bibliothèques pour fournir des fonctionnalités. Ces bibliothèques peuvent être augmentées ou remplacées par des bibliothèques supplémentaires ou des versions mises à jour de bibliothèques plus anciennes.

Des packages Python peuvent être ajoutés au niveau du pool Spark (préversion), et des packages. jar au niveau de la définition de travail Spark.

## <a name="add-or-update-python-libraries"></a>Ajouter ou mettre à jour des bibliothèques Python

Apache Spark dans Azure Synapse Analytics propose une installation complète d’Anacondas, ainsi que de bibliothèques supplémentaires. Pour la liste complète des bibliothèques, consultez la [prise en charge des versions d’Apache Spark](apache-spark-version-support.md).

Lors du démarrage d’une instance Spark, un nouvel environnement virtuel est créé en utilisant cette installation comme base. De plus, un fichier *Requirements.txt* (sortie de la commande `pip freeze`) permet de mettre à niveau l’environnement virtuel. Les packages répertoriés dans ce fichier pour l’installation ou la mise à niveau sont téléchargés à partir de PyPi au moment du démarrage du cluster. Ce fichier de configuration requise est utilisé chaque fois qu’une instance Spark est créée à partir de ce pool Spark.

> [!IMPORTANT]
>
> - Si le package que vous installez est volumineux ou si son installation prend beaucoup de temps, cela affecte le temps de démarrage de l’instance Spark.
> - Les packages qui requièrent une prise en charge par le compilateur au moment de l’installation, tels que GCC, ne sont pas pris en charge.
> - Il n’est pas possible de ramener les packages à une version antérieure, mais uniquement de les ajouter ou de les mettre à niveau.

### <a name="requirements-format"></a>Format de la configuration requise

L’extrait de code suivant montre le format du fichier de configuration requise. Le nom du package PyPi est mentionné avec une version exacte. Ce fichier est au format décrit dans la documentation de référence [pip freeze](https://pip.pypa.io/en/stable/reference/pip_freeze/). Cet exemple épingle une version spécifique. 

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

### <a name="python-library-user-interface"></a>Interface utilisateur de la bibliothèque Python

L’interface utilisateur pour l’ajout de bibliothèques figure sous l’onglet **Paramètres supplémentaires** de la page **Créer un pool Apache Spark** sur le Portail Azure.

Chargez le fichier de configuration de l’environnement à l’aide du sélecteur de fichiers dans la section **Packages** de la page.

![Ajouter des bibliothèques Python](./media/apache-spark-azure-portal-add-libraries/add-python-libraries.png "Ajouter des bibliothèques Python")

### <a name="verify-installed-libraries"></a>Vérifier les bibliothèques installées

Pour vérifier si les bonnes versions des bibliothèques appropriées sont installées, exécutez le code suivant :

```python
import pip #needed to use the pip functions
for i in pip.get_installed_distributions(local_only=True):
    print(i)
```

## <a name="next-steps"></a>Étapes suivantes

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentation Apache Spark](https://spark.apache.org/docs/2.4.4/)
