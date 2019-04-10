---
title: Créer une fabrique d’images dans Azure DevTest Labs | Microsoft Docs
description: Découvrez comment créer une fabrique d’images personnalisées dans Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 8190c2043d7d3daae91c93fd3b66126d0941710b
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361852"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Créez une fabrique d’image personnalisée dans Azure DevTest Labs
Cet article traite de définition d’une stratégie de rétention, nettoyage de la fabrique et retrait d’anciennes images à partir de tous les autres dev/test dans l’organisation. 

## <a name="prerequisites"></a>Conditions préalables
Assurez-vous que vous avez suivi ces articles avant de continuer :

- [Créer une fabrique d’images](image-factory-create.md)
- [Exécuter une fabrique d’images depuis Azure DevOps](image-factory-set-up-devops-lab.md)
- [Enregistrer les images personnalisées et les distribuer à plusieurs laboratoires](image-factory-save-distribute-custom-images.md)

Les éléments suivants doivent être déjà en place :

- Un laboratoire pour la fabrique d’images dans Azure DevTest Labs
- Une ou plusieurs ciblent où la fabrique de distribuer des images golden Azure DevTest Labs
- Un projet Azure DevOps permettent d’automatiser la fabrique d’images.
- Emplacement du code source contenant les scripts et la configuration (dans notre exemple, dans le même projet DevOps ci-dessus)
- Une définition de build pour orchestrer les tâches d’Azure Powershell
 
## <a name="setting-the-retention-policy"></a>Définition de la stratégie de rétention
Avant de configurer les étapes de nettoyage, définissez le nombre d’images historique à conserver dans les DevTest Labs. Lorsque vous avez suivi le [exécuter une fabrique d’images à partir d’Azure DevOps](image-factory-set-up-devops-lab.md) article, vous avez configuré différentes Variables de génération. Un d’eux a été **ImageRetention**. Vous définissez cette variable sur `1`, ce qui signifie que le dev/test ne met pas à jour un historique des images personnalisées. Seuls les dernières images distribuées seront disponibles. Si vous modifiez cette variable sur `2`, la dernière version distributed image, ainsi que les précédentes seront conservées. Vous pouvez définir cette valeur pour définir le nombre d’images historiques, que vous souhaitez conserver dans votre dev/test.

## <a name="cleaning-up-the-factory"></a>Nettoyage de la fabrique
La première étape de nettoyage de la fabrique consiste à supprimer l’Image finale des machines virtuelles à partir de la fabrique d’images. Il existe un script pour effectuer cette tâche, tout comme nos scripts précédents. La première étape consiste à ajouter un autre **Azure Powershell** de tâches à la définition de build comme indiqué dans l’image suivante :

![Étape de PowerShell](./media/set-retention-policy-cleanup/powershell-step.png)

Une fois que vous avez la nouvelle tâche dans la liste, sélectionnez l’élément, puis renseignez tous les détails comme indiqué dans l’image suivante :

![Nettoyage des anciennes tâches de PowerShell images](./media/set-retention-policy-cleanup/configure-powershell-task.png)

Les paramètres de script sont : `-DevTestLabName $(devTestLabName)`.

## <a name="retire-old-images"></a>Mettre hors service les anciennes images 
Cette tâche supprime les anciennes images, en conservant uniquement l’historique correspondante le **ImageRetention** générer la variable. Ajouter un autre **Azure Powershell** tâche à notre définition de build de génération. Une fois qu’elle est ajoutée, sélectionnez la tâche, puis renseignez les détails comme indiqué dans l’image suivante : 

![Mettre hors service des anciennes tâches de PowerShell images](./media/set-retention-policy-cleanup/retire-old-image-task.png)

Les paramètres de script sont : `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>File d’attente la build
Maintenant que vous avez terminé la définition de build, la file d’attente une build pour vous assurer que tout fonctionne. Une fois la build termine avec succès les nouvelles images personnalisées s’affichent dans le laboratoire de destination et si vous vérifiez le laboratoire de fabrique d’image, aucune machines virtuelles configurées. En outre, si vous la file d’attente des builds supplémentaires, vous voir les tâches de nettoyage mise hors service les anciennes images personnalisées à partir de la dev/test conformément à la valeur de rétention définie dans les variables de build.

> [!NOTE]
> Si vous avez exécuté le pipeline de build à la fin de l’article de la série, supprimez manuellement les ordinateurs virtuels qui ont été créés dans le laboratoire de fabrique d’image avant d’y placer une nouvelle build.  L’étape de nettoyage manuel est nécessaire uniquement pendant que nous tout configurer et vérifiez qu’il fonctionne.



## <a name="summary"></a>Résumé
Vous disposez maintenant d’une fabrique d’images en cours d’exécution qui peut générer et distribuer des images personnalisées pour vos laboratoires à la demande. À ce stade, il suffit d’obtention de vos images correctement configuré et identifie les laboratoires de cible. Comme mentionné dans l’article précédent, le **Labs.json** fichier situé dans votre **Configuration** dossier spécifie les images doivent être accessibles dans chacune des laboratoires de la cible. Lorsque vous ajoutez d’autres DevTest Labs à votre organisation, vous devez simplement ajouter une entrée dans le Labs.json pour le nouveau laboratoire.

Ajout d’une nouvelle image à votre fabrique est également simple. Lorsque vous souhaitez inclure une nouvelle image dans votre fabrique que vous ouvrez le [Azure portal](https://portal.azure.com), accédez à votre fabrique de DevTest Labs, sélectionnez le bouton pour ajouter une machine virtuelle et choisissez l’image de votre choix de la place de marché et les artefacts. Au lieu de sélectionner le **créer** bouton pour rendre la nouvelle machine virtuelle, sélectionnez **modèle vue Azure Resource Manager**» et enregistrez le modèle dans un fichier .json quelque part dans le **GoldenImages** dossier dans votre référentiel. La prochaine fois que vous exécutez votre fabrique d’images, il créera votre image personnalisée.


## <a name="next-steps"></a>Étapes suivantes
1. [Planifier votre build/mise en production](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer) exécution périodique de la fabrique d’images. Comment actualiser vos images générés par la fabrique de manière régulière.
2. Mettre des images de golden plus de votre fabrique. Vous pouvez également envisager [création d’artefacts](devtest-lab-artifact-author.md) pour générer un script des éléments supplémentaires de vos tâches de configuration de machine virtuelle et inclure les artefacts dans vos images de la fabrique.
4. Créer un [séparer build/mise en production](/azure/devops/pipelines/overview.md?view=azure-devops-2019) pour exécuter le **DistributeImages** script séparément. Vous pouvez exécuter ce script lorsque vous apportez des modifications à Labs.json et obtenez des images copiés dans les laboratoires de la cible sans avoir à recréer toutes les images à nouveau.

