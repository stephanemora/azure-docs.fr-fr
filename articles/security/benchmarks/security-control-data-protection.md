---
title: Azure Security Control – Protection des données
description: Security Control – Protection des données
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 5482495f87e87e5d05d8adca6b053810a62dcb4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934517"
---
# <a name="security-control-data-protection"></a>Security Control : Protection des données

Les recommandations relatives à la protection des données se concentrent sur les problèmes de chiffrement, les listes de contrôle d’accès, le contrôle d’accès basé sur l’identité et l’enregistrement d’audit pour l’accès aux données.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 : Conserver un inventaire des informations sensibles

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 4,1 | 13.1 | Customer |

Utilisez des balises pour faciliter le suivi des ressources Azure qui stockent ou traitent des informations sensibles.

Créer et utiliser des balises :

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2 : Isoler les systèmes qui stockent ou traitent les informations sensibles

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 4,2 | 13.2 | Customer |

Implémentez des abonnements et/ou des groupes d’administration distincts pour le développement, les tests et la production. Les ressources doivent être séparées par un réseau virtuel/sous-réseau, marquées correctement et sécurisées par un groupe de sécurité réseau ou un pare-feu Azure. Les ressources de stockage ou de traitement des données sensibles doivent être suffisamment isolées. Pour les machines virtuelles qui stockent ou traitent des données sensibles, implémentez la stratégie et les procédures pour les désactiver lorsqu’elles ne sont pas utilisées.

Créer des abonnements Azure supplémentaires :

https://docs.microsoft.com/azure/billing/billing-create-subscription

Créer des groupes d’administration :

https://docs.microsoft.com/azure/governance/management-groups/create

Créer et utiliser des balises :

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Créer un réseau virtuel :

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Créer un groupe de sécurité réseau (NSG) avec une configuration de sécurité :

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Déployer le Pare-feu Azure :

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Comment configurer Alerter ou une Alerte et refuser avec le pare-feu Azure :

https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3. : Surveiller et bloquer le transfert non autorisé d’informations sensibles

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 4.3 | 13.3 | Customer |

Déployez un outil automatisé sur les périmètres du réseau qui surveille le transfert non autorisé d’informations sensibles et bloque ces transferts tout en alertant les professionnels de la sécurité des informations.

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 : Chiffrer toutes les informations sensibles en transit

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 4.4 | 14.4 | Partagé |

Chiffrer toutes les informations sensibles en transit. Assurez-vous que les clients qui se connectent à vos ressources Azure peuvent négocier TLS 1.2 ou une version ultérieure.

Suivez les recommandations d’Azure Security Center relatives au chiffrement au repos et au chiffrement en transit, le cas échéant.

Comprendre le chiffrement en transit avec Azure :

https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 : Utiliser un outil de découverte actif pour identifier les données sensibles

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 4.5 | 14.5 | Customer |

Si aucune fonctionnalité n’est disponible pour votre service spécifique dans Azure, utilisez un outil de découverte actif tiers pour identifier toutes les informations sensibles stockées, traitées ou transmises par les systèmes technologiques de l’organisation, y compris celles situées sur site ou à un fournisseur de services à distance et mettez à jour l’inventaire des informations sensibles de l’organisation.

Utilisez Azure Information Protection pour identifier les informations sensibles dans les documents Office 365.

Utilisez Azure SQL Information Protection pour faciliter la classification et l’étiquetage des informations stockées dans les bases de données SQL Azure.

Comment implémenter Azure SQL Data Discovery :

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

Comment implémenter Azure Information Protection :

https://docs.microsoft.com/azure/information-protection/deployment-roadmap

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6 : Utiliser Azure RBAC pour contrôler l’accès aux ressources

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 4.6 | 14.6 | Customer |

Utilisez Azure AD RBAC pour contrôler l’accès aux données et aux ressources, sinon utilisez des méthodes de contrôle d’accès spécifiques au service.

Comprendre Azure RBAC :

https://docs.microsoft.com/azure/role-based-access-control/overview

Configuration de RBAC dans Azure :

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7 : Utiliser la protection contre la perte de données basée sur l’hôte pour appliquer le contrôle d’accès

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 4,7 | 14.7 | Customer |

Implémentez un outil tiers, tel qu’une solution automatisée de prévention contre la perte de données basée sur l’hôte, pour appliquer des contrôles d’accès aux données même lorsque des données sont copiées à partir d’un système.

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 : Chiffrer des informations sensibles au repos

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 4.8 | 14.8 | Customer |

Utilisez le chiffrement au repos sur toutes les ressources Azure. Microsoft recommande d’autoriser Azure à gérer vos clés de chiffrement, mais il existe une option vous permettant de gérer vos propres clés dans certains cas. 

Comprendre le chiffrement au repos dans Azure :

https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Configurer des clés de chiffrement gérées par le client :

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 : Consigner et alerter les modifications apportées aux ressources Azure critiques

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 4,9 | 14.9 | Customer |

Utilisez Azure Monitor avec le journal des activités Azure pour créer des alertes lorsque des modifications sont apportées à des ressources Azure critiques.

Comment créer des alertes pour les événements du journal des activités Azure :

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

## <a name="next-steps"></a>Étapes suivantes

Reportez-vous au contrôle de sécurité suivant : [Gestion des vulnérabilités](security-control-vulnerability-management.md)
