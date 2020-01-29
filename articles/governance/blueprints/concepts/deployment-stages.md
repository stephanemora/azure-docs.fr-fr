---
title: Phases du déploiement d’un blueprint
description: Découvrez les étapes liées à la sécurité et aux artefacts que les services Azure Blueprints suivent lors de la création d'une attribution de blueprint.
ms.date: 11/13/2019
ms.topic: conceptual
ms.openlocfilehash: daa7722fa37547929aa21b76b870f70143ae71ab
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156622"
---
# <a name="stages-of-a-blueprint-deployment"></a>Phases du déploiement d’un blueprint

Quand un blueprint est déployé, une série d’actions sont effectuées par le service Azure Blueprints pour déployer les ressources définies dans le blueprint. Cet article fournit des détails sur ce que chaque étape implique.

Le déploiement de blueprint est déclenché en affectant un blueprint à un abonnement ou en [mettant à jour une affectation existante](../how-to/update-existing-assignments.md). Lors du déploiement, Blueprints effectue les grandes étapes suivantes :

> [!div class="checklist"]
> - Droits de propriétaire accordés à Blueprints
> - L’objet d’affectation de blueprint est créé.
> - Facultatif - Blueprints crée une identité managée **affectée par le système**
> - L’identité managée déploie les artefacts du blueprint
> - Les droits du service Blueprints et de l’identité managée **affectée par le système** sont révoqués

## <a name="blueprints-granted-owner-rights"></a>Droits de propriétaire accordés à Blueprints

Le principal du service Azure Blueprints dispose de droits de propriétaire sur le ou les abonnements affectés lorsqu’une [identité managée affectée par le système](../../../active-directory/managed-identities-azure-resources/overview.md) est utilisée. Le rôle accordé permet à Blueprints de créer et ultérieurement de révoquer l’identité managée **affectée par le système**. Si vous utilisez une identité managée **affectée par l’utilisateur**, le principal du service Azure Blueprints n’obtient pas et ne nécessite pas de droits de propriétaire sur l’abonnement.

Les droits sont accordés automatiquement si l’affectation est effectuée via le portail. Cependant, si l’affectation est effectuée via l’API REST, l’octroi des droits doit être fait avec un appel d’API distinct. L'ID d'application d'Azure Blueprints est `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`, mais le principal du service varie en fonction du locataire. Utilisez l’[API Graph d’Azure Active Directory](../../../active-directory/develop/active-directory-graph-api.md) et le point de terminaison REST [servicePrincipals](/graph/api/resources/serviceprincipal) pour obtenir le principal du service. Ensuite, accordez à Azure Blueprints le rôle _Propriétaire_ via le [portail](../../../role-based-access-control/role-assignments-portal.md), [Azure CLI](../../../role-based-access-control/role-assignments-cli.md), [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md), l’[API REST](../../../role-based-access-control/role-assignments-rest.md) ou un [modèle Resource Manager](../../../role-based-access-control/role-assignments-template.md).

Le service Blueprints ne déploie pas directement les ressources.

## <a name="the-blueprint-assignment-object-is-created"></a>L’objet d’affectation de blueprint est créé.

Un utilisateur, un groupe ou un principal du service affecte un blueprint à un abonnement. L’objet d’affectation existe au niveau de l’abonnement où le blueprint a été affecté. Les ressources créées par le déploiement ne le sont pas dans le contexte de l’entité de déploiement.

Lors de la création de l’affectation de blueprint, le type d’[identité managée](../../../active-directory/managed-identities-azure-resources/overview.md) est sélectionné. L’option par défaut est une identité managée **affectée par le système**. Vous pouvez choisir une identité managée **affectée par l’utilisateur**. Lors de l’utilisation d’une identité managée **affectée par l’utilisateur**, elle doit être définie et recevoir les autorisations avant la création de l’affectation de blueprint. Les rôles intégrés [Propriétaire](../../../role-based-access-control/built-in-roles.md#owner) et [Opérateur Blueprint](../../../role-based-access-control/built-in-roles.md#blueprint-operator) disposent des autorisations `blueprintAssignment/write` requises pour créer une attribution qui utilise une identité managée **affectée par l’utilisateur**.

## <a name="optional---blueprints-creates-system-assigned-managed-identity"></a>Facultatif - Blueprints crée une identité managée affectée par le système

Quand [identité managée affectée par le système](../../../active-directory/managed-identities-azure-resources/overview.md) est sélectionnée lors de l’affectation, Blueprints crée l’identité et accorde à l’identité managée le rôle de [propriétaire](../../../role-based-access-control/built-in-roles.md#owner). Si une [affectation existante est mise à niveau](../how-to/update-existing-assignments.md), Blueprints utilise l’identité managée créée précédemment.

L’identité managée liée à l’affectation de blueprint est utilisée pour déployer ou redéployer les ressources définies dans le blueprint. Cette conception évite que les affectations interfèrent entre elles par inadvertance.
Cette conception prend également en charge la fonctionnalité de [verrouillage de ressources](./resource-locking.md) en contrôlant la sécurité de chaque ressource déployée à partir du blueprint.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>L’identité managée déploie les artefacts du blueprint

L’identité managée déclenche ensuite les déploiements Resource Manager des artefacts au sein du blueprint dans l’[ordre de séquentiel](./sequencing-order.md) défini. L’ordre peut être ajusté pour garantir que les artefacts dépendants d’autres artefacts sont déployés dans le bon ordre.

Un échec d’accès par un déploiement est souvent le résultat du niveau d’accès accordé à l’identité managée. Le service Blueprints gère le cycle de vie de la sécurité de l’**identité managée affectée par le système**. Cependant, l’utilisateur est chargé de gérer les droits et le cycle de vie d’une identité managée **affectée par l’utilisateur**.

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Les droits du service Blueprints et de l’identité managée affectée par le système sont révoqués

Une fois les déploiements terminés, Blueprints révoque les droits de l’identité managée **affectée par le système** pour l’abonnement. Ensuite, le service Blueprints révoque ses propres droits pour l’abonnement. La suppression des droits empêche Blueprints de devenir propriétaire permanent sur un abonnement.

## <a name="next-steps"></a>Étapes suivantes

- Comprendre comment utiliser les [paramètres statiques et dynamiques](parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../how-to/update-existing-assignments.md).
- Résoudre les problèmes durant l’affectation d’un blueprint en suivant les étapes de [dépannage général](../troubleshoot/general.md).
