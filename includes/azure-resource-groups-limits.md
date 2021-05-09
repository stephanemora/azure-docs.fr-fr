---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 09/01/2020
ms.author: tomfitz
ms.openlocfilehash: c29abc456c5825a2f22b20a4893bb14eb09058af
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107984319"
---
| Ressource | Limite |
| --- | --- |
| Ressources par [groupe de ressources](../articles/azure-resource-manager/management/overview.md#resource-groups) | Les ressources ne sont pas limitées par groupe de ressources. Au lieu de cela, elle sont limités par le type de ressource dans un groupe de ressources. Reportez-vous à la ligne suivante. |
| Ressources par groupe de ressources par type de ressource |800 - Certains types de ressources peuvent dépasser la limite de 800. Voir la section relative aux [ressources non limitées à 800 instances par groupe de ressources](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Déploiements par groupe de ressources dans l’historique des déploiements |800<sup>1</sup> |
| Ressources par déploiement |800 |
| Verrous de gestion par [étendue](../articles/azure-resource-manager/management/overview.md#understand-scope) unique  |20 |
| Nombre de balises par ressource ou groupe de ressources |50 |
| Longueur de clé de la balise |512 |
| Longueur de valeur de la balise |256 |

<sup>1</sup> Quand vous vous approchez de la limite, les déploiements sont automatiquement supprimés de l’historique. La suppression d’une entrée à partir de l’historique des déploiements n’affecte pas les ressources déployées. Pour plus d’informations, consultez [Suppressions automatiques de l’historique de déploiement](../articles/azure-resource-manager/templates/deployment-history-deletions.md).

#### <a name="template-limits"></a>Limites de modèle

| Valeur | Limite |
| --- | --- |
| Paramètres |256 |
| Variables |256 |
| Ressources (incluant le nombre de copies) |800 |
| Sorties |64 |
| Expression de modèle |24 576 caractères |
| Ressources dans les modèles exportés |200 |
| Taille du modèle |4 Mo |
| Taille du fichier de paramètres |4 Mo |

Vous pouvez dépasser certaines limites de modèle en utilisant un modèle imbriqué. Pour plus d’informations, consultez l’article [Utilisation de modèles liés lors du déploiement des ressources Azure](../articles/azure-resource-manager/templates/linked-templates.md). Pour réduire le nombre de paramètres, de variables ou de sorties, vous pouvez combiner plusieurs valeurs dans un même objet. Pour plus d’informations, consultez l’article [Objects as parameters](/azure/architecture/guide/azure-resource-manager/advanced-templates/objects-as-parameters) (Utiliser un objet en tant que paramètre).

Vous pouvez obtenir une erreur avec un fichier modèle ou de paramètres de moins de 4 Mo, si la taille totale de la requête est trop élevée. Pour plus d’informations sur la simplification de votre modèle afin d’éviter les requêtes de trop grande taille, consultez [Résoudre les erreurs de dépassement de taille de la tâche](../articles/azure-resource-manager/templates/error-job-size-exceeded.md).