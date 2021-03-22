---
title: Fichier include
description: Fichier include
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/15/2021
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 97d80e999ac61a2c2f8f561dc19213419014beb8
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/15/2021
ms.locfileid: "103470994"
---
| Ressource | Limite |
| --- | --- |
| Abonnements [associés à un locataire Azure Active Directory](../articles/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) | Illimité |
| [Coadministrateurs](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) par abonnement |Illimité |
| [Groupes de ressources](../articles/azure-resource-manager/management/overview.md) par abonnement |980 |
| Taille de la requête d’API Resource Manager |4 194 304 octets |
| Balises par abonnement<sup>1</sup> |50 |
| Calculs de balise unique par abonnement<sup>1</sup> | 80 000 |
| [Déploiements de niveau d’abonnement](../articles/azure-resource-manager/templates/deploy-to-subscription.md) par emplacement | 800<sup>2</sup> |

<sup>1</sup>Vous pouvez appliquer jusqu’à 50 étiquettes directement à un abonnement. Toutefois, l’abonnement peut contenir un nombre illimité d’étiquettes appliquées aux groupes de ressources et aux ressources au sein de l’abonnement. Le nombre de balises par ressource ou groupe de ressources est limité à 50. Resource Manager retourne une [liste comportant le nom de balise unique et les valeurs](/rest/api/resources/tags) de l’abonnement uniquement lorsque le nombre de balises est inférieur ou égal à 80 000. Vous pouvez toujours trouver une ressource par balise quand le nombre de balises est supérieur à 80 000.

<sup>2</sup> Si vous atteignez la limite des 800 déploiements, supprimez les déploiements inutiles de l’historique. Pour supprimer les déploiements au niveau de l’abonnement, utilisez [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) ou [az deployment sub delete](/cli/azure/deployment/sub#az-deployment-sub-delete).
