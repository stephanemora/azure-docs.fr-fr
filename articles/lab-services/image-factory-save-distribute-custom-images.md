---
title: Enregistrer et distribuer des images dans Azure DevTest Labs | Microsoft Docs
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
ms.openlocfilehash: feabd055833e5f0d850138af528cce1da82cae49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622612"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Enregistrer les images personnalisées et les distribuer à plusieurs laboratoires
Cet article couvre vous donne les étapes pour enregistrer des images personnalisées à partir des machines virtuelles déjà créées (VM). Il décrit également comment distribuer ces images personnalisées pour les autres dev/test dans l’organisation.

## <a name="prerequisites"></a>Conditions préalables
Les éléments suivants doivent être déjà en place :

- Un laboratoire pour la fabrique d’images dans Azure DevTest Labs.
- Un projet Azure DevOps qui est utilisé pour automatiser la fabrique d’images.
- Emplacement du code source contenant les scripts et la configuration (dans notre exemple, dans le même projet DevOps mentionné à l’étape précédente).
- La définition de build pour orchestrer les tâches d’Azure Powershell.

Si nécessaire, suivez les étapes de la [exécuter une fabrique d’images à partir d’Azure DevOps](image-factory-set-up-devops-lab.md) pour créer ou configurer ces éléments. 

## <a name="save-vms-as-generalized-vhds"></a>Enregistrer des machines virtuelles en tant que disques durs virtuels généralisées
Enregistrer les machines virtuelles existantes en tant que disques durs virtuels généralisées.  Voici un exemple de script PowerShell pour enregistrer les machines virtuelles existantes en tant que disques durs virtuels généralisées. Pour l’utiliser, tout d’abord, ajoutez un autre **Azure Powershell** de tâches à la définition de build comme indiqué dans l’image suivante :

![Ajouter une étape d’Azure PowerShell](./media/save-distribute-custom-images/powershell-step.png)

Une fois que vous avez la nouvelle tâche dans la liste, sélectionnez l’élément afin que nous pouvons remplir dans tous les détails comme indiqué dans l’image suivante : 

![Paramètres de PowerShell](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Généralisées et spécialisées images personnalisées
Dans le [Azure portal](https://portal.azure.com), lorsque vous créez une image personnalisée à partir d’une machine virtuelle, vous pouvez choisir d’avoir un généralisée ou une image personnalisée spécialisée.

- **Image personnalisée spécialisée :** Sysprep/annuler le déploiement n’a pas été exécuté sur l’ordinateur. Cela signifie que l’image est une copie exacte du disque du système d’exploitation sur l’ordinateur virtuel existant (un instantané).  Le même fichiers, applications, des comptes d’utilisateur, nom de l’ordinateur et ainsi de suite, sont tous présents lorsque nous créons un nouvel ordinateur à partir de cette image personnalisée.
- **Image personnalisée généralisée :** Sysprep/annuler le déploiement a été exécuté sur l’ordinateur.  Lorsque ce processus est exécuté, il supprime les comptes d’utilisateur, supprime le nom d’ordinateur, supprime les ruches du Registre, etc., dans le but de généraliser l’image, donc il peut être personnalisé lors de la création d’une autre machine virtuelle.  Lorsque vous généralisez une machine virtuelle (en exécutant sysprep), le processus détruit l’ordinateur virtuel actuel : il ne sera plus fonctionnelle.

Le script pour l’alignement des images personnalisées dans la fabrique d’images enregistrera les disques durs virtuels pour les machines virtuelles créées à l’étape précédente (identifié fondée sur une balise sur la ressource dans Azure).

## <a name="update-configuration-for-distributing-images"></a>Configuration de mise à jour pour la distribution d’images
L’étape suivante du processus consiste à transmettre les images personnalisées à partir de l’atelier de fabrique d’image out pour tous les laboratoires qui en ont besoin. La partie principale de ce processus est le **labs.json** fichier de configuration. Vous trouverez ce fichier dans le **Configuration** dossier inclus dans la fabrique d’images.

Il existe deux éléments clés répertoriés dans le fichier de configuration labs.json :

- Identifiant de manière unique un laboratoire de destination spécifique à l’aide de l’ID d’abonnement et le nom de laboratoire.
- L’ensemble spécifique d’images qui doivent être basculés vers le laboratoire en tant que chemins d’accès relatifs à la racine de configuration. Vous pouvez spécifier un dossier entier (pour obtenir toutes les images dans ce dossier) trop.

Voici un exemple de fichier labs.json avec deux laboratoires répertoriés. Dans ce cas, vous voulez distribuer des images aux deux laboratoires différents.

```json
{
   "Labs": [
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2012R2",
               "Win2016/Datacenter.json"
         ]
      },
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2016/Datacenter.json"
         ]
      }
   ]
}
```

## <a name="create-a-build-task"></a>Créer une tâche de génération
À l’aide de la même procédure que vous avez vu précédemment dans cet article, ajoutez un autre **Azure Powershell** définition de build de tâche de génération. Renseignez les détails comme indiqué dans l’image suivante : 

![Tâche pour distribuer des images de génération](./media/save-distribute-custom-images/second-build-task-powershell.png)

Les paramètres sont : `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

Cette tâche prend les images personnalisées présentes dans la fabrique d’images et les transfère en dehors de tous les laboratoires définis dans le fichier Labs.json.

## <a name="queue-the-build"></a>File d’attente la build
Une fois que la tâche de génération de distribution est terminée, la file d’attente une build pour vous assurer que tout fonctionne. Une fois la build terminée, les nouvelles images personnalisées seront afficheront dans le laboratoire de destination qui a été entré dans le fichier de configuration Labs.json.

## <a name="next-steps"></a>Étapes suivantes
Dans l’article suivant de la série, vous mettez à jour la fabrique d’images avec un étapes de nettoyage et de la stratégie de rétention : [Définir la stratégie de rétention et exécuter des scripts de nettoyage](image-factory-set-retention-policy-cleanup.md).
