---
title: Fichier Include
description: Fichier Include
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 06/04/2021
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 1197b15f313746c0a8542bfadeb4e0123818fca8
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112575629"
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
| Emplacements de [déploiements de niveau d’abonnement](../articles/azure-resource-manager/templates/deploy-to-subscription.md) | 10 |

<sup>1</sup>Vous pouvez appliquer jusqu’à 50 étiquettes directement à un abonnement. Toutefois, l’abonnement peut contenir un nombre illimité d’étiquettes appliquées aux groupes de ressources et aux ressources au sein de l’abonnement. Le nombre de balises par ressource ou groupe de ressources est limité à 50. Resource Manager retourne une [liste comportant le nom de balise unique et les valeurs](/rest/api/resources/tags) de l’abonnement uniquement lorsque le nombre de balises est inférieur ou égal à 80 000. Vous pouvez toujours trouver une ressource par balise quand le nombre de balises est supérieur à 80 000.

<sup>2</sup>Quand vous vous approchez de la limite, les déploiements sont automatiquement supprimés de l’historique. Pour plus d’informations, consultez [Suppressions automatiques de l’historique de déploiement](../articles/azure-resource-manager/templates/deployment-history-deletions.md).