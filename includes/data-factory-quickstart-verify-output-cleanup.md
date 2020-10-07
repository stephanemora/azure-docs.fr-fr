---
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
author: linda33wj
ms.author: jingwang
ms.openlocfilehash: c0fcdf1cf69c55f63288138bc7377a78295cb2be
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "86544393"
---
## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

Le pipeline crée automatiquement le dossier de sortie dans le conteneur d’objets blob adftutorial. Ensuite, il copie le fichier emp.txt à partir du dossier d’entrée dans le dossier de sortie. 

1. Dans le portail Azure, dans la page du conteneur **adftutorial**, sélectionnez **Actualiser** pour voir le dossier de sortie. 
    
    ![Actualiser](media/data-factory-quickstart-verify-output-cleanup/output-refresh.png)

2. Dans la liste des dossiers, sélectionnez **output**. 

3. Vérifiez que le fichier **emp.txt** a été copié dans le dossier de sortie. 

    ![Actualiser](media/data-factory-quickstart-verify-output-cleanup/output-file.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Vous disposez de deux moyens de supprimer les ressources que vous avez créées dans le guide de démarrage rapide. Vous pouvez supprimer le [groupe de ressources Azure](../articles/azure-resource-manager/management/overview.md) qui inclut toutes les ressources du groupe de ressources. Si vous souhaitez conserver les autres ressources, supprimez uniquement la fabrique de données créée dans ce tutoriel.

Si vous supprimez un groupe de ressources, toutes les ressources qu’il contient, y compris les fabriques de données, seront supprimées. Exécutez la commande suivante pour supprimer l’intégralité du groupe de ressources : 

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

> [!Note]
> La suppression d’un groupe de ressources peut prendre un certain temps. Veuillez patienter pendant ce processus.

Si vous souhaitez supprimer uniquement la fabrique de données, et non pas l’intégralité du groupe de ressources, exécutez la commande suivante : 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```