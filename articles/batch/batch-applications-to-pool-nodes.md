---
title: Copier des applications et des données dans des nœuds de pool
description: Découvrez comment copier des applications et des données dans des nœuds de pool.
ms.topic: how-to
ms.date: 02/18/2021
ms.openlocfilehash: 0109171fd78dc11058daa30bf4604bebc1eeb857
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101703645"
---
# <a name="copy-applications-and-data-to-pool-nodes"></a>Copier des applications et des données dans des nœuds de pool

Azure Batch prend en charge plusieurs façons de récupérer des données et des applications sur des nœuds de calcul afin que les tâches puissent y accéder et les utiliser.

La méthode à choisir peut dépendre de la portée du fichier ou de l’application. Dans certains cas, les données et les applications sont nécessaires pour exécuter l’intégralité du travail et doivent donc être installées sur chaque nœud. D’autres fichiers et applications ne sont requis que pour une tâche spécifique. D’autres encore doivent être installés pour le travail, mais pas nécessairement sur chaque nœud. Batch dispose d’outils pour chacun de ces scénarios.

## <a name="determine-the-scope-required-of-a-file"></a>Déterminer l’étendue requise d’un fichier

Vous devez déterminer l’étendue d’un fichier : il s’agit du fichier requis pour un pool, un travail ou une tâche. Les fichiers dont l’étendue est limitée au pool doivent utiliser des packages d’application de pool ou une tâche de démarrage. Les fichiers dont l’étendue correspond au travail doivent utiliser une tâche de préparation du travail. Les applications sont un bon exemple de fichiers étendus au niveau du pool ou du travail. Les fichiers dont l’étendue est limitée à la tâche doivent utiliser des fichiers de ressources de tâche.

## <a name="pool-start-task-resource-files"></a>Fichiers de ressources de la tâche de démarrage du pool

Pour les applications et les données qui doivent être installées sur chaque nœud du pool, utilisez les fichiers de ressources de la tâche de démarrage du pool. Appliquez cette méthode avec un [package d’application](batch-application-packages.md) ou le regroupement de fichiers de ressources de la tâche de démarrage afin d’exécuter une commande d’installation.  

Par exemple, vous pouvez utiliser la ligne de commande de la tâche de démarrage pour déplacer ou installer des applications. Vous avez également la possibilité de spécifier une liste de fichiers ou de conteneurs dans un compte de stockage Azure. Pour plus d’informations, consultez [Ajout de #ResourceFile dans la documentation REST](/rest/api/batchservice/pool/add#resourcefile).

Si chacun des travaux exécutés sur le pool exécute une application (.exe) qui doit d’abord être installée avec un fichier .msi, vous devez définir la propriété **waitForSuccess** sur **true**. Pour plus d’informations, consultez [Ajout de #StartTask dans la documentation REST](/rest/api/batchservice/pool/add#starttask).

## <a name="application-package-references"></a>Références du package d’application

Pour les applications et les données qui doivent être installées sur chaque nœud du pool, vous pouvez utiliser des [packages d’application](batch-application-packages.md). Il n’existe aucune commande d’installation associée à un package d’application, mais vous pouvez utiliser une tâche de démarrage pour exécuter une commande d’installation. Si votre application ne nécessite pas d’installation ou si elle comprend un grand nombre de fichiers, vous pouvez utiliser cette méthode.

Les packages d’application se révèlent utiles en présence d’un grand nombre de fichiers, car ils peuvent combiner de nombreuses références de fichiers en une petite charge utile. Si vous essayez d’inclure plus de 100 fichiers de ressources distincts dans une seule tâche, le service Batch peut rencontrer des limitations système internes pour une tâche unique. Les packages d’application conviennent également si vous avez plusieurs versions différentes de la même application et que vous devez en choisir une.

## <a name="job-preparation-task-resource-files"></a>Fichiers de ressources de la tâche de préparation du travail

Pour les applications et les données qui doivent être installées pour le travail à exécuter, mais qui n’ont pas besoin d’être installées sur l’ensemble du pool, vous pouvez utiliser les [fichiers de ressources de la tâche de préparation du travail](./batch-job-prep-release.md).

Par exemple, si votre pool présente de nombreux types différents de travaux dont un seul a besoin d’un fichier .msi pour s’exécuter, il est pertinent de placer l’étape d’installation dans une tâche de préparation du travail.

## <a name="task-resource-files"></a>Fichiers de ressources de tâche

Les fichiers de ressources de la tâche sont appropriés lorsque l’application ou les données ne s’appliquent qu’à une tâche individuelle.

Supposons par exemple que vous ayez cinq tâches, chacune traitant un fichier différent, puis écrivant la sortie dans le Stockage Blob. Dans ce cas, le fichier d’entrée doit être spécifié sur le regroupement de fichiers de ressources de la tâche, car chaque tâche possède son propre fichier d’entrée.

## <a name="additional-ways-to-get-data-onto-nodes"></a>Autres moyens de récupérer des données sur les nœuds

Étant donné que vous contrôlez les nœuds Azure Batch et que vous pouvez exécuter des exécutables personnalisés, vous avez la possibilité d’extraire des données de toutes les sources personnalisées que vous souhaitez. Veillez à ce que le nœud Batch dispose d’une connectivité à la cible et assurez-vous de disposer des informations d’identification pour cette source sur le nœud.

Voici quelques exemples de méthodes de transfert de données vers des nœuds Batch :

- Téléchargement de données à partir de SQL
- Téléchargement de données à partir d’autres services web ou emplacements personnalisés
- Mappage d’un partage réseau

## <a name="azure-storage"></a>Stockage Azure

N’oubliez pas que le Stockage Blob présente des objectifs de scalabilité du téléchargement. Les objectifs d’extensibilité des partages de fichiers du stockage Azure sont les mêmes que pour un blob unique. La taille a un impact sur le nombre de nœuds et de pools nécessaires.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur l’utilisation de [packages d’application avec Batch](batch-application-packages.md).
- En savoir plus sur [l’utilisation de nœuds et de pools](nodes-and-pools.md).
