---
title: Contrôle de sécurité Azure - Configuration sécurisée
description: Configuration sécurisée du contrôle de sécurité
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 03564effeee36ddb3316d48329ccab8ccfce75b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934293"
---
# <a name="security-control-secure-configuration"></a>Contrôle de sécurité : Configuration sécurisée

Établissez, implémentez et gérez activement (suivi, création de rapports, correction) de la configuration de sécurité des ressources Azure afin d’empêcher les attaquants de tirer parti des services et des paramètres vulnérables.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1 : Établir des configurations sécurisées pour toutes les ressources Azure

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 7.1 | 5,1 | Customer |

Utilisez Azure Policy ou Azure Security Center pour gérer les configurations de sécurité pour toutes les ressources Azure.

Configurer et gérer Azure Policy :

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="72-establish-secure-operating-system-configurations"></a>7.2 : Établir des configurations sécurisées du système d’exploitation

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 7.2 | 5,1 | Customer |

Utilisez la recommandation &quot;Corriger les vulnérabilités dans les configurations de sécurité sur vos machines virtuelles&quot; Azure Security Center pour gérer les configurations de sécurité sur toutes les ressources de calcul.

Gérer les recommandations relatives à Azure Security Center :

https://docs.microsoft.com/azure/security-center/security-center-recommendations

Comment corriger les recommandations Azure Security Center :

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 : Gérer les configurations de ressources Azure sécurisées

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 7.3 | 5.2 | Customer |

Utilisez Azure Policy [refuser] et [déployer s’il n’existe pas] pour appliquer des paramètres sécurisés à vos ressources Azure.

Configurer et gérer Azure Policy :

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Comprendre les effets d’Azure Policy :

https://docs.microsoft.com/azure/governance/policy/concepts/effects

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4 : Préserver la sécurité des configurations du système d'exploitation

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 7.4 | 5.2 | Partagé |

Les images de base du système d’exploitation sont gérées et entretenues par Microsoft.

Toutefois, vous pouvez appliquer les paramètres de sécurité requis par votre organisation à l’aide des modèles Azure Resource Manager et/ou de la Desired State Configuration.

Création d’une machine virtuelle Azure à partir d’un modèle de Azure Resource Manager :

https://docs.microsoft.com/azure/virtual-machines/windows/ps-template

Comprendre la Desired State Configuration pour les machines virtuelles Azure :

https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-overview

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5 : Stocker en toute sécurité la configuration des ressources Azure

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 7.5 | 5.3 | Customer |

Si vous utilisez des définitions de stratégie Azure personnalisées, utilisez Azure DevOps ou Azure Repos pour stocker et gérer votre code en toute sécurité.

Comment stocker du code dans Azure DevOps :

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentation Azure Repos

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

## <a name="76-securely-store-custom-operating-system-images"></a>7.6 Stocker en toute sécurité des images de système d’exploitation personnalisées

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 7.6 | 5.3 | Customer |

Si vous utilisez des images personnalisées, utilisez RBAC pour vous assurer que seuls les utilisateurs autorisés peuvent accéder aux images. Pour les images de conteneur, stockez-les dans Azure Container Registry et tirez parti de RBAC pour vous assurer que seuls les utilisateurs autorisés peuvent accéder aux images.

Comprendre RBAC dans Azure :

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Comprendre RBAC pour Container Registry :

https://docs.microsoft.com/azure/container-registry/container-registry-roles

Configuration de RBAC dans Azure :

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

## <a name="77-deploy-system-configuration-management-tools"></a>7.7 : Déployer les outils de gestion de configuration système

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 7,7 | 5.4 | Customer |

Utilisez Azure Policy pour alerter, auditer et appliquer des configurations système. En outre, développez un processus et un pipeline pour la gestion des exceptions de stratégie.

Configurer et gérer Azure Policy :

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8 : Déployer les outils de gestion de la configuration système pour les systèmes d’exploitation

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 7.8 | 5.4 | Customer |

Utilisez les extensions de calcul Azure, telles que la configuration d’état souhaité PowerShell pour l’extension Windows Compute ou Linux Chef pour Linux.

Comment installer des extensions de machine virtuelle dans Azure :

https://docs.microsoft.com/azure/virtual-machines/extensions/overview#how-can-i-install-an-extension

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9 : Mettre en place la surveillance de la configuration automatique pour les services Azure

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 7.9 | 5.5 | Customer |

Utiliser Azure Security Center pour effectuer des analyses de ligne de base pour vos ressources Azure

Comment corriger les recommandations dans Azure Security Center :

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10 : Implémenter la surveillance de la configuration automatique pour les systèmes d’exploitation

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 7.1 | 5.5 | Customer |

Utilisez Azure Security Center pour effectuer des analyses de base pour les paramètres de système d’exploitation et d’ancrage des conteneurs.

Comprendre les recommandations concernant les conteneurs dans Azure Security Center :

https://docs.microsoft.com/azure/security-center/security-center-container-recommendations

## <a name="711-manage-azure-secrets-securely"></a>7.11 : Gérer les secrets Azure en toute sécurité 

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 7.11 | 13.1 | Customer |

Utilisez Managed Service Identity conjointement avec Azure Key Vault pour simplifier et sécuriser la gestion des secrets pour vos applications Cloud.

Intégration aux identités managées Azure :

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Pour créer un coffre de clés :

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Fournir une authentification Key Vault avec une identité managée :

https://docs.microsoft.com/azure/key-vault/managed-identity

## <a name="712-manage-identities-securely-and-automatically"></a>7.12 : Gérer les identités de façon sécurisée et automatique

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 7.12 | 4,1 | Customer |

Utilisez des identités managées pour fournir aux services Azure une identité gérée automatiquement dans Azure AD. Les identités managées vous permettent de vous authentifier auprès d’un service qui prend en charge l’authentification Azure AD, y compris Key Vault, sans informations d’identification dans votre code.

Configuration d’identités managées :

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13 : Éliminer l’exposition involontaire des informations d’identification

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 7.13 | 13.3 | Customer |

Exécuter le moteur d’analyse des informations d’identification pour identifier les informations d’identification dans le code. Le moteur d’analyse des informations d’identification encourage également le déplacement des informations d’identification découvertes vers des emplacements plus sécurisés, tels qu’Azure Key Vault. 

Configuration de Credential Scanner :

https://secdevtools.azurewebsites.net/helpcredscan.html

## <a name="next-steps"></a>Étapes suivantes

Reportez-vous au contrôle de sécurité suivant : [Défense contre les programmes malveillants](security-control-malware-defense.md)
