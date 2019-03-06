---
title: Déplacer des ressources Azure vers un autre groupe de ressources | Microsoft Docs
description: Découvrez comment déplacer des ressources d’un groupe de ressources vers un autre ou d’un abonnement vers un autre.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 12/19/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 5bc8a408ce247ad2980983c3eb32807074af599f
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820822"
---
# <a name="tutorial-move-azure-resources-to-another-resource-group"></a>Tutoriel : Déplacer des ressources Azure vers un autre groupe de ressources

Découvrez comment déplacer des ressources d’un groupe de ressources vers un autre. Vous pouvez également déplacer des ressources Azure d’un abonnement Azure vers un autre. Dans ce didacticiel, vous allez utiliser un modèle Resource Manager pour déployer deux groupes de ressources et un compte de stockage. Vous déplacerez ensuite le compte de stockage d’un groupe de ressources à l’autre.

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Préparer les ressources.
> * Vérifier que la ressource peut être déplacée.
> * Liste de contrôle avant le déplacement de la ressource.
> * Valider l’opération de déplacement.
> * Déplacer la ressource.
> * Supprimer des ressources.

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prepare-the-resources"></a>Préparer les ressources

Un modèle a été créé et placé sur un [compte de stockage partagé](https://armtutorials.blob.core.windows.net/moveresources/azuredeploy.json). Le modèle définit deux groupes de ressources et un compte de stockage. Lorsque vous déployez le modèle, vous devez fournir un nom de projet. Le nom du projet est utilisé pour générer des noms de ressource uniques.  Le code JSON suivant est extrait du modèle :

```json
"variables": {
  "resourceGroupSource": {
    "name": "[concat(parameters('projectName'), 'rg1')]",
    "location": "eastus"
  },
  "resourceGroupDestination": {
    "name": "[concat(parameters('projectName'), 'rg2')]",
    "location": "westus"      
  },
  "storageAccount": {
    "name": "[concat(parameters('projectName'), 'store')]",
    "location": "eastus"
  }
},
```

Notez les emplacements définis dans le code JSON : les deux groupes de ressources se trouvent dans les régions USA Est et USA Ouest. Le compte de stockage se trouve dans la région USA Est. Lorsque vous déplacez une ressource vers un autre groupe de ressources se trouvant à un emplacement différent, l’opération de déplacement ne modifie pas l’emplacement de la ressource.

Sélectionnez **Essayer** pour ouvrir Cloud Shell, puis exécutez-y le script PowerShell suivant :

```azurepowershell-interactive
$projectName = Read-Host -prompt "Enter a project name"
New-AzDeployment `
    -Name $projectname `
    -Location "centralus" `
    -TemplateUri "https://armtutorials.blob.core.windows.net/moveresources/azuredeploy.json" `
    -projectName $projectName
```

Patientez jusqu’à la fin de l’exécution du script, puis ouvrez le [Portail Azure](https://portal.azure.com)et vérifiez que les groupes de ressources et le compte de stockage sont déployés correctement.

> [!NOTE]
> Étant donné que le modèle définit deux groupes de ressources, ce déploiement est considéré comme le déploiement au niveau de l’abonnement. Le déploiement de modèle du portail ne prend pas en charge les déploiements au niveau de l’abonnement. Par conséquent, Azure PowerShell est utilisé dans ce didacticiel. Azure CLI prend également en charge les déploiements au niveau de l’abonnement. Voir [Créer des groupes de ressources et des ressources pour un abonnement Azure](./deploy-to-subscription.md).

## <a name="verify-the-resource-can-be-moved"></a>Vérifier que la ressource peut être déplacée

Toutes les ressources ne peuvent pas être déplacées. La ressource utilisée dans ce didacticiel est un compte de stockage, qui peut être déplacé. Pour vérifier si une ressource peut être déplacée, voir [Services pouvant être déplacés](./resource-group-move-resources.md#services-that-can-be-moved).

## <a name="checklist-before-moving-resources"></a>Liste de contrôle avant le déplacement de ressources

Cette étape est facultative dans ce didacticiel, car elle a déjà été effectuée précédemment.

Plusieurs étapes importantes doivent être effectuées avant de déplacer une ressource. Voir [Liste de contrôle avant le déplacement de ressources](./resource-group-move-resources.md#checklist-before-moving-resources).

## <a name="validate-the-move"></a>Valider l’opération de déplacement

La validation du déplacement est facultative dans ce didacticiel, car elle a déjà été effectuée précédemment.

L’opération de validation du déplacement vous permet de tester votre scénario de déplacement sans réellement déplacer les ressources. Utilisez cette opération pour déterminer si le déplacement sera effectué. Pour plus d’informations, voir [Valider l’opération de déplacement](./resource-group-move-resources.md#validate-move).

## <a name="move-the-resource"></a>Déplacer la ressource

Le compte de stockage se trouve à l’intérieur du groupe de ressources source (rg1). Exécutez le script PowerShell suivant pour déplacer la ressource vers le groupe de ressources de destination (rg2). Veillez à utiliser le même nom de projet que celui utilisé lors du déploiement des ressources.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

```azurepowershell-interactive
$projectName = Read-Host -prompt "Enter a project name"
$resourceGroupSource = $projectName + "rg1"
$resourceGroupDestination = $projectName + "rg2"
$storageAccountName = $projectName + "store"

$storageAccount = Get-AzResource -ResourceGroupName $resourceGroupSource -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $resourceGroupDestination -ResourceId $storageAccount.ResourceId
```

Ouvrez le [Portail Azure](https://portal.azure.com), vérifiez que le compte de stockage a été déplacé vers l’autre groupe de ressources et que l’emplacement du compte de stockage est toujours USA Est.

Lorsque vous déplacez des ressources, le groupe source et le groupe cible sont verrouillés pendant l’opération. Les opérations d’écriture et de suppression sont bloquées sur les groupes de ressources tant que le déplacement n’est pas terminé. Ce verrou signifie que vous ne pouvez pas ajouter, mettre à jour ou supprimer des ressources dans les groupes de ressources, mais il ne signifie pas que les ressources sont figées. Par exemple, si vous déplacez un serveur SQL Server et sa base de données vers un nouveau groupe de ressources, une application qui utilise la base de données ne rencontre aucune interruption de service. Elle peut toujours lire et écrire dans la base de données.

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, nettoyez les ressources Azure que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources source.  
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.
5. Sélectionnez le nom du groupe de ressources de destination.  
6. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à déplacer un compte de stockage d’un groupe de ressources vers un autre. Jusqu’ici, vous avez utilisé un compte de stockage ou plusieurs instances de compte de stockage. Dans le tutoriel suivant, vous développez un modèle disposant de plusieurs ressources et types de ressources. Certaines ressources comportent des ressources dépendantes.

> [!div class="nextstepaction"]
> [Créer des ressources dépendantes](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
