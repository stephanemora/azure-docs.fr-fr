---
title: Contrôle de sécurité Azure - Gestion des stocks et des ressources
description: Gestion des stocks et des ressources de contrôle de sécurité
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 2f24012a69a7673426644a8ded6162cd1021938e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75930060"
---
# <a name="security-control-inventory-and-asset-management"></a>Contrôle de sécurité : Gestion des stocks et des ressources

Les recommandations en matière de gestion des stocks et des ressources visent à résoudre les problèmes liés à la gestion active (stocks, suivi et correction) de toutes les ressources Azure, de sorte que seules les ressources autorisées disposent d’un accès et que les ressources non autorisées et non gérées soient identifiées et supprimées.

## <a name="61-use-azure-asset-discovery"></a>6.1 : Utiliser la découverte de ressources Azure

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 6.1 | 1.1, 1.2, 1.3, 1.4, 9.1, 12.1 | Customer |

Utilisez Azure Resource Graph pour interroger/découvrir toutes les ressources (telles que calcul, stockage, réseau, ports et protocoles) dans vos abonnements.  Vérifiez les autorisations (lecture) appropriées dans votre locataire et répertoriez tous les abonnements Azure, ainsi que les ressources dans vos abonnements.

Bien que les ressources Azure classiques puissent être découvertes via Resource Graph, il est vivement recommandé de créer et d’utiliser des ressources Azure Resource Manager à l’avenir.

Créer des requêtes avec Azure Resource Graph :

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Afficher vos abonnements Azure :

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Comprendre Azure RBAC :

https://docs.microsoft.com/azure/role-based-access-control/overview

## <a name="62-maintain-asset-metadata"></a>6.2 : Gérer les métadonnées de ressources

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 6.2 | 1.5 | Customer |

Appliquez des balises aux ressources Azure en fournissant des métadonnées pour les organiser de façon logique par catégories.

Créer et utiliser des balises :

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="63-delete-unauthorized-azure-resources"></a>6.3 : Supprimer des ressources Azure non autorisées

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 6.3 | 1.6 | Customer |

Utilisez des étiquettes, des groupes d’administration et diviser des abonnements, le cas échéant, pour organiser et suivre les ressources. Rapprochez régulièrement l’inventaire et assurez-vous que les ressources non autorisées sont supprimées de l’abonnement en temps utile.

Créer des abonnements Azure supplémentaires :

https://docs.microsoft.com/azure/billing/billing-create-subscription

Créer des groupes d’administration :

https://docs.microsoft.com/azure/governance/management-groups/create

Créer et utiliser des balises :

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4 : Tenir un inventaire des ressources Azure approuvées et titres des logiciels

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 6.4 | 2.1 | Customer |

Définissez des ressources Azure approuvées et des logiciels approuvés pour les ressources de calcul.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6.5 : Analyser les ressources Azure non approuvées

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 6.5 | 2.3, 2.4 | Customer |

Utilisez Azure Policy pour appliquer des restrictions quant au type de ressources pouvant être créées dans vos abonnements.

Utilisez Azure Resource Graph pour interroger/découvrir des ressources dans leurs abonnements. &nbsp;Assurez-vous que toutes les ressources Azure présentes dans l’environnement sont approuvées.

Configurer et gérer Azure Policy :

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Créer des requêtes avec Azure Graph :

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6 : Analyser les applications logicielles non approuvées dans des ressources de calcul

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 6.6 | 2.3/2.4 | Customer |

Utilisez l’inventaire des machines virtuelles Azure pour automatiser la collecte d’informations sur tous les logiciels présents sur les machines virtuelles. Le nom, la version, l’éditeur et l’heure d’actualisation du logiciel sont disponibles sur le portail Azure. Pour avoir accès à la date d’installation et à d’autres informations, activez les diagnostics au niveau de l’invité et placez les journaux des événements Windows dans un espace de travail Log Analytics.

Activer l’inventaire des machines virtuelles Azure :

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7 : Supprimer des ressources et applications logicielles Azure non approuvées

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 6.7 | 2.5 | Customer |

Utilisez la surveillance de l’intégrité des fichiers d’Azure Security Center (Change Tracking) et l’inventaire des machines virtuelles pour identifier tous les logiciels installés sur les machines virtuelles. Vous pouvez implémenter votre propre processus de suppression des logiciels non autorisés. Vous pouvez également utiliser une solution tierce pour identifier les logiciels non approuvés.

Utiliser la surveillance de l’intégrité des fichiers :

https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring

Comprendre Azure Change Tracking :

https://docs.microsoft.com/azure/automation/change-tracking

Activer l’inventaire des machines virtuelles Azure :

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="68-use-only-approved-applications"></a>6.8 : Utiliser des applications approuvées uniquement

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 6.8 | 2.6 | Customer |

Utilisez les contrôles d’application adaptatifs d’Azure Security Center pour vous assurer que seuls les logiciels autorisés s’exécutent et que l’exécution de tous les logiciels non autorisés est bloquée sur les machines virtuelles Azure.

Utiliser les contrôles d’application adaptatifs d’Azure Security Center :

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="69-use-only-approved-azure-services"></a>6.9 : Utiliser des services Azure approuvés uniquement

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 6.9 | 2.6 | Customer |

Utilisez Azure Policy pour limiter les services que vous pouvez approvisionner dans votre environnement.

Configurer et gérer Azure Policy :

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Refuser un type de ressource spécifique avec Azure Policy :

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

## <a name="610-implement-approved-application-list"></a>6.10 : Implémenter une liste d’applications approuvées

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 6.1 | 2.7 | Customer |

Utilisez les contrôles d’application adaptatifs d’Azure Security Center pour spécifier les types de fichiers auxquels une règle peut ou non s’appliquer.

Implémentez une solution tierce si cela ne répond pas à la configuration requise.

Utiliser les contrôles d’application adaptatifs d’Azure Security Center :

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6.11 : Limiter la capacité des utilisateurs à interagir avec Azure Resource Manager via des scripts

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 6.11 | 2.8 | Customer |

Utilisez l’accès conditionnel Azure pour limiter la capacité des utilisateurs à interagir avec Azure Resource Manager en configurant &quot;Bloquer l’accès&quot; pour l’application &quot;Gestion Microsoft Azure&quot;.

Configurer l’accès conditionnel pour bloquer l’accès à Azure Resource Manager :

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12 : Limiter la capacité des utilisateurs à exécuter des scripts dans des ressources de calcul

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 6.12 | 2.8 | Customer |

Utilisez des configurations de système d’exploitation spécifiques ou des ressources tierces pour limiter la capacité des utilisateurs à exécuter des scripts dans des ressources de calcul Azure.

Par exemple, contrôler l’exécution d’un script PowerShell dans des environnements Windows :

https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13 : Séparer physiquement ou logiquement des applications à risque élevé

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 6.13 | 2,9 | Customer |

Les logiciels nécessaires à des opérations métier, mais qui peuvent poser un risque élevé pour l’organisation, doivent être isolés sur leur propre machine virtuelle et/ou réseau virtuel et être suffisamment sécurisés à l’aide d’un Pare-feu Azure ou d’un groupe de sécurité réseau.

Créer un réseau virtuel :

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Créer un groupe de sécurité réseau (NSG) avec une configuration de sécurité :

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="next-steps"></a>Étapes suivantes

Reportez-vous au contrôle de sécurité suivant : [Configuration sécurisée](security-control-secure-configuration.md)
