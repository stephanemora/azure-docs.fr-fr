---
title: Quotas et limites du service Azure Batch AI | Microsoft Docs
description: En savoir plus sur les contraintes, les limites et les quotas par défaut d’Azure Batch AI, et comment demander une augmentation de quota
services: batch-ai
documentationcenter: ''
author: johnwu10
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/08/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 16032ec5ba1e613462f92b86281ce93153b70923
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409715"
---
# <a name="batch-ai-service-quotas-and-limits"></a>Quotas et limites du service Batch AI

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Comme avec d’autres services Azure, il existe des limites concernant certaines ressources associées au service Batch AI. Dans Batch AI, ces limites sont des quotas par défaut appliqués au niveau de l’abonnement pour chaque région où le service est [disponible](https://azure.microsoft.com/global-infrastructure/services/). Cet article décrit ces paramètres par défaut, et comment vous pouvez demander une augmentation de ces quotas.

Gardez ces quotas à l’esprit quand vous concevez et que vous augmentez vos ressources Batch AI. Par exemple, si votre cluster n’atteint pas le nombre cible de nœuds que vous avez spécifié, vous avez peut-être atteint la limite de cœurs Batch AI pour votre abonnement.

Si vous envisagez d’exécuter des charges de travail de production dans Batch AI, vous devrez peut-être affecter à un ou plusieurs des quotas une valeur supérieure à la valeur par défaut.

> [!NOTE]
> Un quota est une limite de crédit, pas une garantie de capacité. Si vous avez des besoins de capacité à grande échelle, contactez le support Azure.
> 
> 

## <a name="resource-quotas"></a>Quotas de ressources

[!INCLUDE [azure-batch-ai-limits](../../includes/azure-batch-ai-limits.md)]

## <a name="other-limits"></a>Autres limites

Les éléments suivants sont des limites strictes qui ne peuvent être dépassées une fois atteintes.

| **Ressource** | **Limite maximale** |
| --- | --- |
| Nombre maximum d’espaces de travail par groupe de ressources | 800 |
| Taille maximale des clusters | 100 nœuds |
| Nombre maximum de processus MPI GPU par nœud | 1-4 |
| Nombre maximum de travailleurs GPU par nœud | 1-4 |
| Durée de vie maximale des travaux | 7 jours<sup>1</sup> |
| Nombre maximum de serveurs de paramètre par nœud | 1 |

<sup>1</sup> La durée de vie maximale fait référence au temps du début à la fin de l’exécution d’un travail. Les travaux terminés sont conservés indéfiniment ; les données de travaux non terminés pendant la durée de vie maximale ne sont pas accessibles.

## <a name="view-batch-ai-quotas"></a>Afficher les quotas Batch AI

Affichez vos quotas d’abonnement Batch AI actuels dans le [portail Azure][portal].

1. Dans le volet gauche, cliquez sur **Tous les services**. Recherchez ensuite **Batch AI** et cliquez pour ouvrir le service.
2. Cliquez sur **Utilisation + quotas** dans le menu de Batch AI.
3. Sélectionnez votre abonnement pour afficher les limites de quota.

## <a name="increase-a-batch-ai-cores-quota"></a>Augmenter un quota de cœurs Batch AI

Suivez ces étapes pour demander une augmentation de quota pour votre abonnement Batch AI à l’aide du [portail Azure][portal]. 

1. Dans le volet gauche, cliquez sur **Tous les services**. Recherchez ensuite **Batch AI** et cliquez pour ouvrir le service.
2. Cliquez sur **Nouvelle demande de support** dans le menu de Batch AI.
3. Dans **De base** :
   
    a. **Type de problème** > **Quota**
   
    b. **Abonnement** > sélectionnez votre abonnement.
   
    c. **Type de quota** > **Batch AI**
   
    d. **Plan de support** > sélectionnez votre plan de support.

    Cliquez sur **Suivant**.
4. Dans **Problème** :
   
    a. Sélectionnez un **niveau de gravité** en fonction de [l’impact sur votre activité][support_sev].
   
    b. Dans **Détails du quota**, spécifiez l’emplacement, le type de quota et le type de ressource. Spécifiez la nouvelle limite que vous souhaitez demander. Cliquez sur **Enregistrer et continuer**.

    c. Facultatif : téléchargez des fichiers pertinentes avec plus d’informations sur le motif de l’augmentation.
   
    Cliquez sur **Suivant**.
5. Dans **Informations de contact** :
   
    a. Sélectionnez une **méthode de contact préférée**.
   
    b. Vérifiez et entrez les informations de contact requises.
   
    Cliquez sur **Créer** pour envoyer la demande de support.

Une fois que vous avez envoyé votre demande de support, le support Azure vous contactera. Le traitement de la demande peut prendre jusqu’à 2 jours ouvrés.


## <a name="next-steps"></a>Étapes suivantes

Une fois familiarisé avec les limites de quota, consultez les articles suivants pour la prise en main de Batch AI.

> [!div class="nextstepaction"]
> [Didacticiel de démarrage rapide de Batch AI](quickstart-tensorflow-training-cli.md)
> [Recettes Batch AI](https://github.com/Azure/BatchAI/tree/master/recipes)
> [En savoir plus sur les ressources Batch AI](resource-concepts.md)

[portal]: https://portal.azure.com
[support_sev]: http://aka.ms/supportseverity