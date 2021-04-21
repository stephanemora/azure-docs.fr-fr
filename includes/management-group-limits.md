---
title: Fichier include
description: Fichier include
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 53e3f37d14153f3a2d7b5886a49b08ca9052b128
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800355"
---
| Ressource | Limite |
| --- | --- |
| Groupes d’administration par locataire Azure AD | 10 000 |
| Abonnements par groupe d’administration | Illimité. |
| Niveaux de hiérarchie des groupes d’administration | Niveau racine plus six niveaux<sup>1</sup> |
| Groupe d’administration parent direct par groupe d’administration | Un |
| [Déploiements au niveau du groupe d’administration](../articles/azure-resource-manager/templates/deploy-to-management-group.md) par emplacement | 800<sup>2</sup> |

<sup>1</sup>Les six niveaux n’incluent pas le niveau d’abonnement.

<sup>2</sup> Si vous atteignez la limite des 800 déploiements, supprimez les déploiements inutiles dans l’historique. Pour supprimer des déploiements au niveau du groupe d’administration, utilisez [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) ou [az deployment mg delete](/cli/azure/deployment/mg#az_deployment_mg_delete).
