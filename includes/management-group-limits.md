---
title: Fichier include
description: Fichier include
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: cdcf6215973755444da9e513761de7ac71e479d4
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98738600"
---
| Ressource | Limite |
| --- | --- |
| Groupes d’administration par locataire Azure AD | 10 000 |
| Abonnements par groupe d’administration | Illimité. |
| Niveaux de hiérarchie des groupes d’administration | Niveau racine plus six niveaux<sup>1</sup> |
| Groupe d’administration parent direct par groupe d’administration | Un |
| [Déploiements au niveau du groupe d’administration](../articles/azure-resource-manager/templates/deploy-to-management-group.md) par emplacement | 800<sup>2</sup> |

<sup>1</sup>Les six niveaux n’incluent pas le niveau d’abonnement.

<sup>2</sup> Si vous atteignez la limite des 800 déploiements, supprimez les déploiements inutiles dans l’historique. Pour supprimer des déploiements au niveau du groupe d’administration, utilisez [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) ou [az deployment mg delete](/cli/azure/deployment/mg#az-deployment-mg-delete).
