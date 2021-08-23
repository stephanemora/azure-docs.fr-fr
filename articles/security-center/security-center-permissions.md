---
title: Autorisations dans Azure Security Center | Microsoft Docs
description: Cet article explique comment Azure Security Center utilise le contrôle d’accès en fonction du rôle pour affecter des autorisations aux utilisateurs et identifier les actions autorisées pour chaque rôle.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 07/04/2021
ms.author: memildin
ms.openlocfilehash: 50a907625d9ef2db84289165f377b9ebf5f5d56e
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2021
ms.locfileid: "113297493"
---
# <a name="permissions-in-azure-security-center"></a>Autorisations dans Azure Security Center

Security Center utilise le [contrôle d’accès en fonction du rôle d’Azure (RBAC)](../role-based-access-control/role-assignments-portal.md), qui fournit des [rôles intégrés](../role-based-access-control/built-in-roles.md) susceptibles d’être affectés à des utilisateurs, des groupes et des services dans Azure.

Security Center évalue la configuration de vos ressources pour identifier les vulnérabilités et les problèmes de sécurité. Dans Security Center, vous ne voyez les informations relatives à une ressource que lorsque vous avez reçu le rôle de propriétaire, de collaborateur ou de lecteur pour l’abonnement ou le groupe de ressources auquel appartient la ressource.

Outre ces rôles intégrés, il existe deux rôles propres à Security Center :

* **Lecteur Sécurité** : l’utilisateur ayant ce rôle dispose de droits d’affichage dans Security Center. Il peut afficher les recommandations, les alertes, la stratégie de sécurité actuelle et les états de sécurité, mais ne peut pas apporter de modifications.
* **Administrateur de la sécurité** : l’utilisateur ayant ce rôle dispose des mêmes droits que le Lecteur Sécurité. Il peut en outre modifier la stratégie de sécurité et ignorer les alertes et les recommandations.

> [!NOTE]
> Les rôles de sécurité que sont le lecteur Sécurité et l’administrateur de la sécurité ont uniquement accès à Security Center. Les rôles de sécurité n’ont pas accès aux autres services d’Azure (par exemple, Stockage, Web et mobile ou Internet des objets).

## <a name="roles-and-allowed-actions"></a>Rôles et actions autorisées

Le tableau suivant affiche les rôles et les actions autorisées dans Security Center.

| **Action**                                                                                                                      | [Lecteur de sécurité ](../role-based-access-control/built-in-roles.md#security-reader) / <br> [Lecteur](../role-based-access-control/built-in-roles.md#reader) | [Administrateur de la sécurité](../role-based-access-control/built-in-roles.md#security-admin) | [Contributeur](../role-based-access-control/built-in-roles.md#contributor) / [Propriétaire](../role-based-access-control/built-in-roles.md#owner)| [Contributeur](../role-based-access-control/built-in-roles.md#contributor)| [Propriétaire](../role-based-access-control/built-in-roles.md#owner)|
|:----------------------------------------------------------------------------------------------------------------------------|:-----------------------------:|:--------------:|:------------------------------------------------------:|:------------------------:|:------------------:|
||||**(Au niveau du groupe de ressources)**|**(Au niveau de l’abonnement)**|**(Au niveau de l’abonnement)**|
| Ajouter/attribuer des initiatives (y compris) des standards de conformité réglementaire)                                                         | -                             | -              | -                                                      | -                        | ✔                 |
| Modifier une stratégie de sécurité                                                                                                        | -                             | ✔             | -                                                      | -                        | ✔                 |
| Activer/désactiver les plans Azure Defender                                                                                             | -                             | ✔             | -                                                      | -                        | ✔                 |
| Activer/désactiver le provisionnement automatique                                                                                          | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| Ignorer les alertes                                                                                                              | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| Appliquer des recommandations de sécurité à une ressource</br> (et utiliser [Corriger](security-center-remediate-recommendations.md#fix-button)) | -                             | -              | ✔                                                     | ✔                        | ✔                 |
| Afficher les alertes et les recommandations                                                                                             | ✔                            | ✔              | ✔                                                     | ✔                        | ✔                 |
||||||

> [!NOTE]
> Nous vous recommandons d’attribuer le rôle le moins permissif permettant aux utilisateurs d’effectuer leurs tâches. Par exemple, affectez le rôle Lecteur aux utilisateurs qui n’ont besoin que de consulter des informations sur l’intégrité de la sécurité d’une ressource sans effectuer aucune action, telles que l’application des recommandations ou la modification des stratégies.

## <a name="next-steps"></a>Étapes suivantes
Cet article vous a expliqué comment Security Center utilise Azure RBAC pour affecter des autorisations aux utilisateurs et a identifié les actions autorisées pour chaque rôle. Maintenant que vous êtes familiarisé avec les affectations de rôles nécessaires pour surveiller l’état de sécurité de votre abonnement, modifier les stratégies de sécurité et appliquer les recommandations, découvrez comment :

- [Définir des stratégies de sécurité dans Security Center](tutorial-security-policy.md)
- [Gérer les recommandations de sécurité dans Security Center](security-center-recommendations.md)
- [Gérer et répondre aux alertes de sécurité dans Security Center](security-center-managing-and-responding-alerts.md)
- [Surveiller les solutions de sécurité des partenaires](./security-center-partner-integration.md)