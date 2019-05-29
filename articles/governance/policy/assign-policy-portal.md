---
title: Créer une stratégie pour des ressources non conformes
description: Cet article vous guide à travers les étapes de création d’une définition de stratégie pour identifier les ressources non conformes.
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/06/2018
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 291152b46f97c9b6dd10b67c7ce812fcc791ce09
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65979602"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Démarrage rapide : Créer une affectation de stratégie pour identifier les ressources non conformes

La première étape pour comprendre la conformité dans Azure consiste à identifier l’état de vos ressources.
Ce démarrage rapide vous guide pas à pas dans le processus de création d’une attribution de stratégie pour identifier les machines virtuelles qui n’utilisent pas de disques managés.

À la fin de ce processus, vous aurez identifié correctement les machines virtuelles qui n’utilisent pas de disques managés. Elles sont *non conformes* à l’attribution de stratégie.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="create-a-policy-assignment"></a>Créer une affectation de stratégie

Dans ce guide de démarrage rapide, vous créez une attribution de stratégie et affectez la définition de stratégie *Auditer les machines virtuelles qui n’utilisent pas de disques managés*.

1. Lancez le service Azure Policy dans le portail Azure en cliquant sur **Tous les services**, puis en recherchant et en cliquant sur **Stratégie**.

   ![Rechercher Stratégie dans Tous les services](./media/assign-policy-portal/search-policy.png)

1. Sélectionnez **Affectations** du côté gauche de la page Azure Policy. Une affectation est une stratégie qui a été affectée pour être appliquée dans une étendue spécifique.

   ![Page de sélection des affectations à partir de la page de vue d’ensemble de la stratégie](./media/assign-policy-portal/select-assignments.png)

1. Sélectionnez **Assigner une stratégie** en haut de la page**Stratégie - Affectations**.

   ![Affecter une définition de stratégie à partir de la page Affectations](./media/assign-policy-portal/select-assign-policy.png)

1. Dans la page **Assigner une stratégie**, sélectionnez **l’étendue** en cliquant sur les points de suspension et en sélectionnant un groupe d’administration ou un abonnement. Sélectionnez éventuellement un groupe de ressources. Une étendue détermine les ressources ou le regroupement de ressources sur lequel la stratégie est appliquée. Cliquez ensuite sur **Sélectionner** dans le bas de la page **Étendue**.

   Cet exemple utilise l’abonnement **Contoso**. Votre abonnement sera différent.

1. Vous pouvez exclure des ressources en fonction de **l’étendue**. Les **exclusions** commencent à un niveau inférieur à celui de **l’étendue**. Les **exclusions** étant facultatives, laissez ce champ vide pour l’instant.

1. Sélectionnez les points de suspension de **Définition de stratégie** pour ouvrir la liste des définitions disponibles. Azure Policy est fourni avec des définitions de stratégie intégrées que vous pouvez utiliser. De nombreuses définitions de stratégie sont disponibles, par exemple :

   - Enforce tag and its value
   - Apply tag and its value
   - Nécessitent SQL Server version 12.0

   Pour obtenir une liste partielle des stratégies intégrées disponibles, consultez [Exemples Azure Policy](./samples/index.md).

1. Recherchez la définition *Auditer les machines virtuelles qui n’utilisent pas de disques managés* dans la liste des définitions de stratégie. Cliquez sur cette stratégie, puis sur **Sélectionner**.

   ![Rechercher la définition de stratégie appropriée](./media/assign-policy-portal/select-available-definition.png)

1. Le **Nom de l’attribution** est automatiquement rempli avec le nom de stratégie que vous avez sélectionné, mais vous pouvez le modifier. Pour cet exemple, conservez *Auditer les machines virtuelles qui n’utilisent pas de disques managés*. Vous pouvez également ajouter une **Description** (facultatif). La description fournit des détails sur cette affectation de stratégie.
   Le champ **Affectée par** est automatiquement renseigné en fonction de l’utilisateur connecté. Ce champ étant facultatif, vous pouvez entrer des valeurs personnalisées.

1. Laissez la case **Créer une identité managée** non cochée. Vous _devez_ la cocher si la stratégie ou l’initiative inclut une stratégie avec l’effet [deployIfNotExists](./concepts/effects.md#deployifnotexists). La stratégie utilisée dans ce guide de démarrage rapide n'étant pas concernée, ne cochez pas la case. Pour plus d’informations, consultez [Identités managées](../../active-directory/managed-identities-azure-resources/overview.md) et [Fonctionnement de la sécurité par correction](./how-to/remediate-resources.md#how-remediation-security-works).

1. Cliquez sur **Affecter**.

Vous êtes maintenant prêt à identifier les ressources non conformes pour comprendre l’état de conformité de votre environnement.

## <a name="identify-non-compliant-resources"></a>Identifier les ressources non conformes

Sélectionnez **Conformité** dans la partie gauche de la page. Recherchez ensuite l’affectation de stratégie **Auditer les machines virtuelles qui n’utilisent pas de disques managés** que vous avez créée.

![Détails de la conformité dans la page Conformité à la stratégie](./media/assign-policy-portal/policy-compliance.png)

Si des ressources existantes ne sont pas conformes à cette nouvelle affectation, elles apparaissent sous **Ressources non conformes**.

Si une condition est évaluée par rapport à vos ressources existantes et génère la valeur true, ces ressources sont marquées comme non conformes à la stratégie. Le tableau suivant montre comment les différents effets des stratégies fonctionnent avec l’évaluation des conditions pour l’état de conformité résultant. Même si vous ne voyez pas la logique d’évaluation dans le portail Azure, les résultats d’état de conformité sont affichés. Le résultat d’état de conformité est soit conforme, soit non conforme.

| **État de la ressource** | **Effet** | **Évaluation de la stratégie** | **État de conformité** |
| --- | --- | --- | --- |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Non conforme |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Conforme |
| Nouveau | Audit, AuditIfNotExist\* | True | Non conforme |
| Nouveau | Audit, AuditIfNotExist\* | False | Conforme |

\* Les effets Append, DeployIfNotExist et AuditIfNotExist nécessitent que l’instruction IF ait la valeur TRUE.
Les effets nécessitent également que la condition d’existence ait la valeur FALSE pour être non conformes. Lorsque la valeur est TRUE, la condition IF déclenche l’évaluation de la condition d’existence pour les ressources associées.

## <a name="clean-up-resources"></a>Supprimer des ressources

Pour supprimer l’affectation créée, procédez comme suit :

1. Sélectionnez **Conformité** (ou **Affectations**) dans la partie gauche de la page Azure Policy et recherchez l’affectation de stratégie **Auditer les machines virtuelles qui n’utilisent pas de disques managés** que vous avez créée.

1. Cliquez avec le bouton droit sur l’affectation de stratégie **Auditer les machines virtuelles qui n’utilisent pas de disques managés** et sélectionnez **Supprimer l’attribution**.

   ![Supprimer une affectation dans la page Conformité](./media/assign-policy-portal/delete-assignment.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez affecté une définition de stratégie à une étendue et vous avez évalué son rapport de conformité.
La définition de stratégie permet de vérifier que toutes les ressources dans l’étendue sont conformes, ainsi que d’identifier celles qui ne le sont pas.

Pour en savoir plus sur l’affectation de stratégies visant à vérifier que les nouvelles ressources sont conformes, suivez le tutoriel :

> [!div class="nextstepaction"]
> [Création et gestion des stratégies](./tutorials/create-and-manage.md)