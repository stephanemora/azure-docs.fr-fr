---
title: Fichier Include
description: Fichier Include
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: ee564accd0eaf53f0089e0dba3bb00e3eb1665e1
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112575630"
---
| Ressource | Limite |
| --- | --- |
| Groupes d’administration par locataire Azure AD | 10 000 |
| Abonnements par groupe d’administration | Illimité. |
| Niveaux de hiérarchie des groupes d’administration | Niveau racine plus six niveaux<sup>1</sup> |
| Groupe d’administration parent direct par groupe d’administration | Un |
| [Déploiements au niveau du groupe d’administration](../articles/azure-resource-manager/templates/deploy-to-management-group.md) par emplacement | 800<sup>2</sup> |
| Emplacements de [déploiements au niveau du groupe d’administration](../articles/azure-resource-manager/templates/deploy-to-management-group.md) | 10 |

<sup>1</sup>Les six niveaux n’incluent pas le niveau d’abonnement.

<sup>2</sup> Si vous atteignez la limite des 800 déploiements, supprimez les déploiements inutiles dans l’historique. Pour supprimer des déploiements au niveau du groupe d’administration, utilisez [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) ou [az deployment mg delete](/cli/azure/deployment/mg#az_deployment_mg_delete).
