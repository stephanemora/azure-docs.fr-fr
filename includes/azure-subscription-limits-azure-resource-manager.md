---
title: Fichier include
description: Fichier include
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 01/25/2021
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: ad0c532c2ac80fd8a3bb3e68431ff7fc274d73e0
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792329"
---
| Ressource | Limite |
| --- | --- |
| Abonnements par locataire Azure Active Directory | Illimité |
| [Coadministrateurs](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) par abonnement |Illimité |
| [Groupes de ressources](../articles/azure-resource-manager/management/overview.md) par abonnement |980 |
| Taille de la requête d’API Resource Manager |4 194 304 octets |
| Balises par abonnement<sup>1</sup> |50 |
| Calculs de balise unique par abonnement<sup>1</sup> | 80 000 |
| [Déploiements de niveau d’abonnement](../articles/azure-resource-manager/templates/deploy-to-subscription.md) par emplacement | 800<sup>2</sup> |

<sup>1</sup>Vous pouvez appliquer jusqu’à 50 étiquettes directement à un abonnement. Toutefois, l’abonnement peut contenir un nombre illimité d’étiquettes appliquées aux groupes de ressources et aux ressources au sein de l’abonnement. Le nombre de balises par ressource ou groupe de ressources est limité à 50. Resource Manager retourne une [liste comportant le nom de balise unique et les valeurs](/rest/api/resources/tags) de l’abonnement uniquement lorsque le nombre de balises est inférieur ou égal à 80 000. Vous pouvez toujours trouver une ressource par balise quand le nombre de balises est supérieur à 80 000.

<sup>2</sup> Si vous atteignez la limite des 800 déploiements, supprimez les déploiements inutiles de l’historique. Pour supprimer les déploiements au niveau de l’abonnement, utilisez [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) ou [az deployment sub delete](/cli/azure/deployment/sub#az-deployment-sub-delete).
