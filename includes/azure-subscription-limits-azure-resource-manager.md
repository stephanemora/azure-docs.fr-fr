---
title: Fichier Include
description: Fichier Include
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 02/10/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 32d39493e526a4b95369e2998d3f9ade9f2964dc
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133715"
---
| Ressource | Limite par défaut | Limite maximale |
| --- | --- | --- |
| [Groupes de ressources](../articles/azure-resource-manager/management/overview.md) par abonnement |980 |980 |
| Taille de la requête d’API Resource Manager |4 194 304 octets. |4 194 304 octets. |
| Balises par abonnement<sup>1</sup> |Illimité. |Illimité. |
| Calculs de balise unique par abonnement<sup>1</sup> | 10 000 | 10 000 |
| [Déploiements de niveau d’abonnement](../articles/azure-resource-manager/templates/deploy-to-subscription.md) par emplacement | 800<sup>2</sup> | 800 |
| Abonnements par locataire Azure Active Directory | Illimité. | Illimité. |
| [Coadministrateurs](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) par abonnement |Illimité. |Illimité. |

<sup>1</sup> Vous pouvez appliquer un nombre illimité de balises par abonnement. Le nombre de balises par ressource ou groupe de ressources est limité à 50. Le Gestionnaire des ressources retourne une [liste comportant le nom et les valeurs de balise unique](/rest/api/resources/tags) dans l’abonnement uniquement lorsque le nombre de balises est inférieur ou égal à 10 000. Vous pouvez toujours trouver une ressource par balise lorsque le nombre de balises est supérieur à 10 000.  

<sup>2</sup> Si vous atteignez la limite des 800 déploiements, supprimez les déploiements inutiles dans l’historique. Pour supprimer les déploiements au niveau de l’abonnement, utilisez [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) ou [az deployment delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).
