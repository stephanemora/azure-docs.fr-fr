---
title: Phases du déploiement d’un blueprint
description: Découvrez les étapes que les services du programme Azure Blueprint traverse lors d’un déploiement.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d7000813b51fb9c9aae9a21cbded3ae0028e83f4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59796631"
---
# <a name="stages-of-a-blueprint-deployment"></a>Phases du déploiement d’un blueprint

Quand un plan est déployé, une série d’actions est effectuée par le service de plans d’Azure pour déployer les ressources définies dans le plan. Cet article fournit des détails sur ce que chaque étape implique.

Déploiement de solution Blueprint est déclenché en assignant un plan à un abonnement ou [une attribution existante de la mise à jour](../how-to/update-existing-assignments.md). Lors du déploiement, les plans prend les étapes générales suivantes :

> [!div class="checklist"]
> - Plans d’accorder des droits de propriétaire
> - L’objet d’affectation de plan est créé.
> - Facultatif - plans crée **attribué par le système** identité gérée
> - L’identité gérée déploie des artefacts de plan
> - Plan de service et **attribué par le système** identité gérée droits sont révoqués

## <a name="blueprints-granted-owner-rights"></a>Plans d’accorder des droits de propriétaire

Le principal du service de plans d’Azure dispose de droits de propriétaire sur l’abonnement attribué ou les abonnements. Le rôle accordé permet de plans créer et révoquez ultérieurement, le [attribué par le système d’identité gérée](../../../active-directory/managed-identities-azure-resources/overview.md).

Les droits sont accordés automatiquement si l’affectation est effectuée via le portail. Toutefois, si l’affectation est effectuée via l’API REST, l’octroi de droits doit être avec une API distincte appeler. L’ID d’application Azure Blueprint est `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`, mais le principal du service varie selon le client. Utilisez [API Graph Azure Active Directory](../../../active-directory/develop/active-directory-graph-api.md) et le point de terminaison REST [principaux du service](/graph/api/resources/serviceprincipal) pour obtenir le principal du service. Accordez ensuite les plans Azure le _propriétaire_ rôle via le [Portal](../../../role-based-access-control/role-assignments-portal.md), [Azure CLI](../../../role-based-access-control/role-assignments-cli.md), [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md), [REST API](../../../role-based-access-control/role-assignments-rest.md), ou un [modèle Resource Manager](../../../role-based-access-control/role-assignments-template.md).

Le service de plans ne déployer directement les ressources.

## <a name="the-blueprint-assignment-object-is-created"></a>L’objet d’affectation de plan est créé.

Un utilisateur, un groupe ou un principal de service affecte un plan à un abonnement. L’objet d’attribution existe au niveau de l’abonnement où le plan a été attribué. Ressources créées par le déploiement ne sont pas effectuées dans le contexte de l’entité de déploiement.

Lors de la création de l’affectation de plan, le type de [identité gérée](../../../active-directory/managed-identities-azure-resources/overview.md) est sélectionné. La valeur par défaut est un **attribué par le système** identité gérée. Un **affectée à l’utilisateur** identité gérée peut être choisie. Lorsque vous utilisez un **affectée à l’utilisateur** gérés d’identité, il doit être défini et avant la création de l’affectation de plan des autorisations accordée.

## <a name="optional---blueprints-creates-system-assigned-managed-identity"></a>Facultatif - plans crée attribué par le système d’identité gérée

Lorsque [attribué par le système d’identité gérée](../../../active-directory/managed-identities-azure-resources/overview.md) est sélectionné lors de l’attribution, plans crée l’identité et accorde l’identité gérée la [propriétaire](../../../role-based-access-control/built-in-roles.md#owner) rôle. Si un [attribution existante est mise à niveau](../how-to/update-existing-assignments.md), plans utilise l’identité gérée créée précédemment.

L’identité gérée liée à l’affectation de plan est utilisée pour déployer ou de redéployer les ressources définies dans le plan. Cette conception évite les affectations par inadvertance interférer entre eux.
Cette conception prend également en charge la [verrouillage de ressources](./resource-locking.md) fonctionnalité en contrôlant la sécurité de chaque ressource déployée à partir du modèle.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>L’identité gérée déploie des artefacts de plan

L’identité gérée déclenche ensuite les déploiements Resource Manager des artefacts dans le plan dans le texte défini [ordre de classement](./sequencing-order.md). L’ordre peut être ajustée pour garantir des artefacts dépendants sur les autres artefacts sont déployés dans le bon ordre.

Un échec de l’accès par un déploiement est souvent le résultat du niveau d’accès accordé à l’identité gérée. Le service de plans gère le cycle de vie de sécurité de la **attribué par le système** identité gérée. Toutefois, l’utilisateur est chargé de gérer les droits et le cycle de vie d’un **affectée à l’utilisateur** identité gérée.

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Service de plan et les droits d’attribué par le système d’identité gérée sont révoqués.

Une fois que les déploiements terminés, plans révoque les droits de le **attribué par le système** identité gérée à partir de l’abonnement. Ensuite, le service de plans révoque ses droits de l’abonnement. Suppression des droits empêche des plans de devenir un propriétaire permanent sur un abonnement.

## <a name="next-steps"></a>Étapes suivantes

- Comprendre comment utiliser les [paramètres statiques et dynamiques](parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../how-to/update-existing-assignments.md).
- Résoudre les problèmes durant l’affectation d’un blueprint en suivant les étapes de [dépannage général](../troubleshoot/general.md).