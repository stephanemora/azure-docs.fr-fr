---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: c1a52e1bc2dde74289cb270fcae832be24de5a06
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77592417"
---
| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Ressources par [groupe de ressources](../articles/azure-resource-manager/management/overview.md#resource-groups) | N/A | Les ressources ne sont pas limitées par groupe de ressources. Au lieu de cela, elle sont limités par le type de ressource dans un groupe de ressources. Reportez-vous à la ligne suivante. | 
| Ressources par groupe de ressources par type de ressource |800 |Certains types de ressources peuvent dépasser la limite de 800. Voir la section relative aux [ressources non limitées à 800 instances par groupe de ressources](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Déploiements par groupe de ressources dans l’historique des déploiements |800<sup>1</sup> |800 |
| Ressources par déploiement |800 |800 |
| Verrous de gestion par étendue unique |20 |20 |
| Nombre de balises par ressource ou groupe de ressources |50 |50 |
| Longueur de clé de la balise |512 |512 |
| Longueur de valeur de la balise |256 |256 |

<sup>1</sup>Si vous atteignez la limite des 800 déploiements par groupe de ressources, supprimez les déploiements inutiles dans l’historique. La suppression d’une entrée à partir de l’historique des déploiements n’affecte pas les ressources déployées. Pour plus d’informations, consultez [Résoudre l’erreur de nombre de déploiements supérieur à 800](../articles/azure-resource-manager/templates/deployment-quota-exceeded.md).

#### <a name="template-limits"></a>Limites de modèle

| Valeur | Limite par défaut | Limite maximale |
| --- | --- | --- |
| Paramètres |256 |256 |
| Variables |256 |256 |
| Ressources (incluant le nombre de copies) |800 |800 |
| Outputs |64 |64 |
| Expression de modèle |24 576 caractères |24 576 caractères |
| Ressources dans les modèles exportés |200 |200 | 
| Taille du modèle |4 Mo |4 Mo |
| Taille du fichier de paramètres |64 Ko |64 Ko |

Vous pouvez dépasser certaines limites de modèle en utilisant un modèle imbriqué. Pour plus d’informations, consultez l’article [Utilisation de modèles liés lors du déploiement des ressources Azure](../articles/azure-resource-manager/templates/linked-templates.md). Pour réduire le nombre de paramètres, de variables ou de sorties, vous pouvez combiner plusieurs valeurs dans un même objet. Pour plus d’informations, consultez l’article [Objects as parameters](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md) (Utiliser un objet en tant que paramètre).
