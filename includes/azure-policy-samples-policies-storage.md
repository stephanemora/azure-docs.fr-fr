---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 7aa6abb1a5fa9c969ca5e6d0730bed3b461fe81b
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170287"
---
|Name |Description |Effet(s) |Version |
|---|---|---|---|
|[Références SKU de compte de stockage autorisées](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/AllowedStorageSkus_Deny.json) |Cette stratégie vous permet de spécifier un ensemble de références de compte de stockage que votre organisation peut déployer. |Deny |1.0.0 |
|[Auditer l’accès réseau illimité aux comptes de stockage](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |Auditer l’accès illimité au réseau dans les paramètres de pare-feu de votre compte de stockage. Au lieu de cela, configurer les règles du réseau de telle manière que seules les applications des réseaux autorisés puissent accéder au compte de stockage. Pour autoriser les connexions de clients Internet ou locaux spécifiques, l’accès au trafic peut être autorisé à partir de réseaux virtuels Azure spécifiques ou vers des plages d’adresses IP Internet publiques |Audit, Désactivé |1.0.0 |
|[Déployer Advanced Threat Protection sur les comptes de stockage](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/StorageAdvancedThreatProtection_Deploy.json) |Cette stratégie active Advanced Threat Protection sur les comptes de stockage. |DeployIfNotExists, Désactivé |1.0.0 |
|[Le stockage géoredondant doit être activé pour les comptes de stockage](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/GeoRedundant_StorageAccounts_Audit.json) |Cette stratégie permet d’auditer un compte de stockage si le stockage géoredondant n’est pas activé. |Audit, Désactivé |1.0.0 |
|[La sécurisation du transfert vers des comptes de stockage doit être activée](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |Auditer l’exigence de transfert sécurisé dans votre compte de stockage. L’option de sécurisation du transfert oblige votre compte de stockage à accepter uniquement des requêtes provenant de connexions sécurisées (HTTPS). L’utilisation de HTTPS garantit l’authentification entre le serveur et le service et protège les données en transit contre les attaques de la couche réseau (attaque de l’intercepteur ou « man-in-the-middle », écoute clandestine, détournement de session) |Audit, Refuser, Désactivé |1.0.0 |
|[Les comptes de stockage doivent autoriser l’accès à partir des services Microsoft approuvés](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |Certains services Microsoft qui interagissent avec les comptes de stockage fonctionnent à partir de réseaux qui ne peuvent pas obtenir l’accès par le biais des règles de réseau. Pour que ce type de service fonctionne comme prévu, autorisez l’ensemble des services Microsoft approuvés à contourner les règles de réseau. Ces services utilisent alors une authentification forte pour accéder au compte de stockage. |Audit, Refuser, Désactivé |1.0.0 |
|[Les comptes de stockage doivent être migrés vers de nouvelles ressources Azure Resource Manager](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Classic_AuditForClassicStorages_Audit.json) |Profitez des nouveautés d’Azure Resource Manager pour renforcer la sécurité de vos comptes de stockage : contrôle d’accès plus puissant, audit amélioré, déploiement et gouvernance basés sur Azure Resource Manager, accès aux identités managées, accès au coffre de clés pour les secrets, authentification basée sur Azure AD, prise en charge des étiquettes et des groupes de ressources pour faciliter la gestion de la sécurité, etc. |Audit, Refuser, Désactivé |1.0.0 |
