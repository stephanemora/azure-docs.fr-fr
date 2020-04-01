---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: c883383d3c870689bb95f808f6f60c5185c165c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334578"
---
| Ressource | Limite |
| --- | --- |
| Ressources par [groupe de ressources](../articles/azure-resource-manager/management/overview.md#resource-groups) | Les ressources ne sont pas limitées par groupe de ressources. Au lieu de cela, elle sont limités par le type de ressource dans un groupe de ressources. Reportez-vous à la ligne suivante. |
| Ressources par groupe de ressources par type de ressource |800 - Certains types de ressources peuvent dépasser la limite de 800. Voir la section relative aux [ressources non limitées à 800 instances par groupe de ressources](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Déploiements par groupe de ressources dans l’historique des déploiements |800<sup>1</sup> |
| Ressources par déploiement |800 |
| Verrous de gestion par étendue unique |20 |
| Nombre de balises par ressource ou groupe de ressources |50 |
| Longueur de clé de la balise |512 |
| Longueur de valeur de la balise |256 |

<sup>1</sup>Si vous atteignez la limite des 800 déploiements par groupe de ressources, supprimez les déploiements inutiles dans l’historique. La suppression d’une entrée à partir de l’historique des déploiements n’affecte pas les ressources déployées. Pour plus d’informations, consultez [Résoudre l’erreur de nombre de déploiements supérieur à 800](../articles/azure-resource-manager/templates/deployment-quota-exceeded.md).

#### <a name="template-limits"></a>Limites de modèle

| Valeur | Limite |
| --- | --- |
| Paramètres |256 |
| Variables |256 |
| Ressources (incluant le nombre de copies) |800 |
| Outputs |64 |
| Expression de modèle |24 576 caractères |
| Ressources dans les modèles exportés |200 |
| Taille du modèle |4 Mo |
| Taille du fichier de paramètres |64 Ko |

Vous pouvez dépasser certaines limites de modèle en utilisant un modèle imbriqué. Pour plus d’informations, consultez l’article [Utilisation de modèles liés lors du déploiement des ressources Azure](../articles/azure-resource-manager/templates/linked-templates.md). Pour réduire le nombre de paramètres, de variables ou de sorties, vous pouvez combiner plusieurs valeurs dans un même objet. Pour plus d’informations, consultez l’article [Objects as parameters](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md) (Utiliser un objet en tant que paramètre).
