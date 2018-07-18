---
title: Créer une affectation de stratégie pour identifier les ressources non conformes dans votre environnement Azure
description: Cet article vous guide à travers les étapes de création d’une définition de stratégie pour identifier les ressources non conformes.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 43f069fbd8f4fcc13bbc4d9e75763fa98aec1065
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34601460"
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment"></a>Créer une affectation de stratégie pour identifier les ressources non conformes dans votre environnement Azure

La première étape pour comprendre la conformité dans Azure consiste à identifier l’état de vos ressources. Ce démarrage rapide vous guide pas à pas dans le processus de création d’une affectation de stratégie pour identifier les machines virtuelles qui n’utilisent pas de disques gérés.

À la fin de ce processus, vous aurez identifié correctement les machines virtuelles qui n’utilisent pas de disques gérés. Elles sont *non conformes* avec l’affectation de stratégie.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="create-a-policy-assignment"></a>Créer une affectation de stratégie

Dans ce guide de démarrage rapide, vous créez une affectation de stratégie et attribuez la définition de stratégie *Audit Virtual Machines without Managed Disks* (Auditer des machines virtuelles sans disques gérés).

1. Lancez le service Azure Policy dans le portail Azure en cliquant sur **Tous les services**, puis en recherchant et en cliquant sur **Stratégie**.

   ![Rechercher une stratégie](media/assign-policy-definition/search-policy.png)

2. Sélectionnez **Affectations** du côté gauche de la page Azure Policy. Une affectation est une stratégie qui a été affectée pour être appliquée dans une étendue spécifique.
3. Sélectionnez **Assigner une stratégie** en haut de la page**Stratégie - Affectations**.

   ![Affecter une définition de stratégie](media/assign-policy-definition/select-assign-policy.png)

4. Dans la page **Assigner une stratégie**, sélectionnez **l’étendue** en cliquant sur les points de suspension et en sélectionnant un abonnement (obligatoire) et un groupe de ressources (facultatif). Une étendue détermine les ressources ou le regroupement de ressources sur lequel la stratégie est appliquée.  Cliquez ensuite sur **Sélectionner** dans le bas de la page **Étendue**.

   Cet exemple utilise **l’abonnement Contoso**. Votre abonnement sera différent.

5. Si vous voulez exclure un ou plusieurs groupes de ressources (si vous avez défini l’étendue seulement pour l’abonnement), ou des ressources spécifiques au sein d’un groupe de ressources (dans l’une ou l’autre définition d’étendue), vous pouvez configurer des **Exclusions** pour l’affectation de la stratégie. Laissez cette zone vide pour l’instant.

6. Sélectionnez les points de suspension de **Définition de stratégie** pour ouvrir la liste des définitions disponibles. Azure Policy est fourni avec des définitions de stratégie intégrées que vous pouvez utiliser. De nombreuses définitions de stratégie intégrées sont disponibles, comme :

   - Enforce tag and its value
   - Apply tag and its value
   - Nécessitent SQL Server version 12.0

    Pour obtenir la liste complète de toutes les stratégies intégrées disponibles, consultez [Exemples de stratégie](json-samples.md).

7. Recherchez la définition *Auditer les machines virtuelles qui n’utilisent pas de disques managés* dans la liste des définitions de stratégie. Cliquez sur cette stratégie, puis sur **Sélectionner**.

   ![Rechercher la définition de stratégie appropriée](media/assign-policy-definition/select-available-definition.png)

8. Le **Nom de l’attribution** est automatiquement rempli avec le nom de stratégie que vous avez sélectionné, mais vous pouvez le modifier. Pour cet exemple, conservez *Auditer les machines virtuelles qui n’utilisent pas de disques managés*. Vous pouvez également ajouter une **Description** (facultatif). La description fournit des détails sur cette affectation de stratégie.

9. Cliquez sur **Affecter**.

Vous êtes maintenant prêt à identifier les ressources non conformes pour comprendre l’état de conformité de votre environnement.

## <a name="identify-non-compliant-resources"></a>Identifier les ressources non conformes

Sélectionnez **Conformité** dans la partie gauche de la page et recherchez l’affectation de stratégie **Auditer les machines virtuelles qui n’utilisent pas de disques managés** que vous avez créée.

![Conformité à la stratégie](media/assign-policy-definition/policy-compliance.png)

Si des ressources existantes ne sont pas conformes à cette nouvelle affectation, elles apparaissent sous **Ressources non conformes**.

Si une condition est évaluée par rapport à vos ressources existantes et génère la valeur true, ces ressources sont marquées comme non conformes à la stratégie. Le tableau suivant montre comment les différents effets des stratégies fonctionnent avec l’évaluation des conditions pour l’état de conformité résultant. Même si vous ne voyez pas la logique d’évaluation dans le portail Azure, les résultats d’état de conformité sont affichés. Le résultat d’état de conformité est soit conforme, soit non conforme.

| **État de la ressource** | **Effet** | **Évaluation de la stratégie** | **État de conformité** |
| --- | --- | --- | --- |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Non conforme |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Conforme |
| Nouveau | Audit, AuditIfNotExist\* | True | Non conforme |
| Nouveau | Audit, AuditIfNotExist\* | False | Conforme |

\* Les effets Append, DeployIfNotExist et AuditIfNotExist nécessitent que l’instruction IF ait la valeur TRUE. Les effets nécessitent également que la condition d’existence ait la valeur FALSE pour être non conformes. Lorsque la valeur est TRUE, la condition IF déclenche l’évaluation de la condition d’existence pour les ressources associées.

## <a name="clean-up-resources"></a>Supprimer des ressources

D’autres guides de cette collection sont basés sur ce démarrage rapide. Si vous prévoyez de continuer avec les didacticiels suivants, ne nettoyez pas les ressources créées dans ce démarrage rapide. Sinon, procédez comme suit pour supprimer toutes les ressources créées au cours de ce démarrage rapide dans le portail Azure.

1. Sélectionnez **Conformité** (ou **Affectations**) dans la partie gauche de la page Azure Policy et recherchez l’affectation de stratégie **Auditer les machines virtuelles qui n’utilisent pas de disques managés** que vous avez créée.

2. Cliquez avec le bouton droit sur l’affectation de stratégie **Auditer les machines virtuelles qui n’utilisent pas de disques managés** et sélectionnez **Supprimer l’attribution**.

   ![Supprimer une affectation](media/assign-policy-definition/delete-assignment.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez affecté une définition de stratégie à une étendue et vous avez évalué son rapport de conformité. La définition de stratégie permet de s’assurer que toutes les ressources dans l’étendue sont conformes, ainsi que d’identifier celles qui ne le sont pas.

Pour plus d’informations sur l’affectation de stratégies et garantir que les ressources **futures** qui sont créées sont conformes, continuez avec le didacticiel suivant :

> [!div class="nextstepaction"]
> [Création et gestion des stratégies](create-manage-policy.md)