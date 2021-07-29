---
title: Convertir un modèle du portail en specs de modèle
description: Décrit comment convertir un modèle existant de la galerie du portail Azure en specs de modèle.
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 324fcb85bfe1be87dc10736c8b5c233432e07146
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026271"
---
# <a name="convert-template-gallery-in-portal-to-template-specs"></a>Convertir la galerie de modèles du portail en specs de modèle

Le portail Azure offre un moyen de stocker des modèles Azure Resource Manager (modèles ARM) dans votre compte. Toutefois, les [specs de modèle](template-specs.md) offrent un moyen plus simple de partager vos modèles avec les utilisateurs de votre organisation et de les lier à d’autres modèles. Cet article explique comment convertir des modèles existants de la galerie de modèles en specs de modèle.

Pour voir si vous avez des modèles à convertir, consultez la [galerie de modèles dans le portail](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems). Le type de ressource de ces modèles est `Microsoft.Gallery/myareas/galleryitems`.

## <a name="convert-with-powershell-script"></a>Convertir avec un script PowerShell

Pour simplifier la conversion des modèles dans la galerie de modèles, utilisez un script PowerShell du référentiel de modèles de démarrage rapide Azure. Lorsque vous exécutez le script, vous pouvez créer une spec de modèle pour chaque modèle ou télécharger un modèle qui crée la spec de modèle. Le script ne supprime pas le modèle de la galerie de modèles.

1. Copiez le [script de migration](https://github.com/Azure/azure-quickstart-templates/blob/master/201-templatespec-migrate-create/Migrate-GalleryItems.ps1). Enregistrez une copie locale sous le nom *Migrate-GalleryItems.ps1*.
1. Pour créer des specs de modèle, fournissez des valeurs pour les paramètres `-ResourceGroupName` et `-Location`.

   Définissez `ItemsToExport` sur `MyGalleryItems` pour exporter vos modèles. Définissez sa valeur sur `AllGalleryItems` pour exporter tous les modèles auxquels vous avez accès.

   L’exemple suivant crée des spécifications de modèle pour chaque modèle dans un groupe de ressources nommé **migratedRG**. Le script crée le groupe de ressources s’il n’existe pas.

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ResourceGroupName migratedRG -Location westus2 -ItemsToExport MyGalleryItems
   ```

1. Pour télécharger des modèles que utilisables pour créer les specs de modèle, ne fournissez pas de valeurs pour le groupe de ressources ou l’emplacement. Au lieu de cela, spécifiez `-ExportToFile`. Le modèle n’est pas le même que votre modèle dans la galerie. Au lieu de cela, celle-ci contient une ressource de spec de modèle qui crée la spec de modèle pour votre modèle.

   L’exemple suivant télécharge les modèles sans créer de spec de modèle.

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ItemsToExport MyGalleryItems -ExportToFile
   ```

   Pour savoir comment déployer le modèle qui crée la spec de modèle, consultez [Démarrage rapide : Créer et déployer une spec de modèle](quickstart-create-template-specs.md).

Pour plus d’informations sur le script et ses paramètres, consultez [Créer des specs de modèle à partir de modèles de la galerie de modèles](https://github.com/Azure/azure-quickstart-templates/tree/master/201-templatespec-migrate-create).

## <a name="manually-convert-through-portal"></a>Convertir manuellement via le portail

Vous pouvez copier manuellement des modèles de la galerie vers une nouvelle spec de modèle.

1. Ouvrez [Modèles (préversion)](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems) dans le portail.
1. Sélectionnez le modèle à migrer.
1. Sélectionnez **Afficher le modèle**.
1. Copiez le contenu du modèle.
1. Dans la barre de recherche du portail, recherchez **Specs de modèle**. Sélectionnez cette option.
1. Sélectionnez **Créer une spécification de modèle**.
1. Fournissez des valeurs pour le nom, l’abonnement, le groupe de ressources, l’emplacement et la version.
1. Sélectionnez **Suivant : Modifier un modèle**.
1. Pour le contenu du modèle, collez le modèle que vous avez copié à partir de la galerie de modèles.
1. Sélectionnez **Vérifier + créer**.
1. Une fois la validation terminée, sélectionnez **Créer**.

Si vous devez partager la spec de modèle avec d’autres utilisateurs au sein de votre organisation, [définissez un contrôle d’accès en fonction du rôle](../../role-based-access-control/tutorial-role-assignments-group-powershell.md) pour le groupe ou les utilisateurs qui ont besoin d’un accès.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les specs de modèle, consultez [Créer et déployer des specs de modèle](template-specs.md).
