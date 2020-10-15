---
title: Fichier include
description: Fichier include
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 94fd7e692be31ba247e3342246d3940ed08ef9b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80334738"
---
| Ressource | Limite |
| --- | --- |
| Abonnements par locataire Azure Active Directory | Illimité. |
| [Coadministrateurs](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) par abonnement |Illimité. |
| [Groupes de ressources](../articles/azure-resource-manager/management/overview.md) par abonnement |980 |
| Taille de la requête d’API Resource Manager |4 194 304 octets. |
| Balises par abonnement<sup>1</sup> |50 |
| Calculs de balise unique par abonnement<sup>1</sup> | 10 000 |
| [Déploiements de niveau d’abonnement](../articles/azure-resource-manager/templates/deploy-to-subscription.md) par emplacement | 800<sup>2</sup> |

<sup>1</sup>Vous pouvez appliquer jusqu’à 50 étiquettes directement à un abonnement. Toutefois, l’abonnement peut contenir un nombre illimité d’étiquettes appliquées aux groupes de ressources et aux ressources au sein de l’abonnement. Le nombre de balises par ressource ou groupe de ressources est limité à 50. Le Gestionnaire des ressources retourne une [liste comportant le nom et les valeurs de balise unique](/rest/api/resources/tags) dans l’abonnement uniquement lorsque le nombre de balises est inférieur ou égal à 10 000. Vous pouvez toujours trouver une ressource par balise lorsque le nombre de balises est supérieur à 10 000.  

<sup>2</sup> Si vous atteignez la limite des 800 déploiements, supprimez les déploiements inutiles dans l’historique. Pour supprimer les déploiements au niveau de l’abonnement, utilisez [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) ou [az deployment sub delete](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-delete).
