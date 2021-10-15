---
title: Meilleures pratiques pour la gestion des secrets - Azure Key Vault | Microsoft Docs
description: En savoir plus sur les meilleures pratiques pour la gestion des secrets Azure Key Vault.
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: mbaldwin
ms.openlocfilehash: 4735fcfd28830c8469ddfbed7b521a507b3ca521
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534668"
---
# <a name="best-practices-for-secrets-management-in-key-vault"></a>Meilleures pratiques de gestion des secrets dans Key Vault

Azure Key Vault vous permet de stocker en toute sécurité des informations d’identification de services ou d’applications, comme des mots de passe et des clés d’accès, sous forme de secrets. Tous les secrets de votre coffre de clés sont chiffrés à l’aide d’une clé logicielle. Lorsque vous utilisez Key Vault, vous n’avez plus besoin de stocker des informations de sécurité dans vos applications. Ne pas avoir à stocker les informations de sécurité dans les applications élimine le besoin d’intégrer ces informations au code.

Exemples de secrets qui doivent être stockés dans Key Vault :

- Secrets des applications clientes
- Chaînes de connexion
- Mots de passe
- Clés d’accès (Redis Cache, Azure Event Hubs, Azure Cosmos DB)
- Clés SSH

Toutes les autres informations sensibles, comme les adresses IP, les noms de service et d’autres paramètres de configuration, doivent être stockées dans [Azure App Configuration](../../azure-app-configuration/overview.md) plutôt que dans Key Vault.

Chaque coffre de clés individuel définit les limites de sécurité pour les secrets. Pour un coffre de clés unique par application, par région et par environnement, nous vous recommandons de prévoir une isolation granulaire des secrets pour une application.

Pour plus d’informations sur les meilleures pratiques concernant Key Vault, consultez [Meilleures pratiques pour utiliser Key Vault](../general/best-practices.md).

## <a name="configuration-and-storing"></a>Configuration et stockage 

Stockez les parties dynamiques des informations d’identification générées lors du renouvellement sous forme de valeurs. Il peut s’agir, par exemple, de secrets d’applications clientes, de mots de passe et de clés d’accès. Tous les attributs et identifiants statiques associés, comme les noms d’utilisateur, les identifiants d’application et les URL de service, doivent être stockés sous forme de balises de secret et copiés dans la nouvelle version d’un secret lors du renouvellement.

Pour plus d’informations sur les secrets, consultez [À propos des secrets Azure Key Vault](about-secrets.md).

## <a name="secrets-rotation"></a>Rotation des secrets
Les secrets sont souvent stockés dans la mémoire de l’application en tant que variables d’environnement ou paramètres de configuration pendant tout le cycle de vie de l’application, ce qui les rend sensibles à une exposition indésirable. Étant donné que les secrets sont sensibles à la fuite ou à l’exposition, il est important de les renouveler souvent, au moins tous les 60 jours.

Pour plus d’informations sur le processus de renouvellement des secrets, consultez [Automatiser la permutation d’un secret pour des ressources qui ont deux jeux d’informations d’authentification](tutorial-rotation-dual.md). 

## <a name="access-and-network-isolation"></a>Accès et isolement réseau

Vous pouvez réduire l’exposition de vos coffres en spécifiant les adresses IP qui y ont accès. Configurez votre pare-feu de manière à n’autoriser que les applications et les services connexes à accéder aux secrets du coffre afin de réduire la capacité des attaquants à accéder aux secrets.

Pour plus d’informations sur la sécurité du réseau, consultez [Configurer les paramètres de mise en réseau d’Azure Key Vault](../general/how-to-azure-key-vault-network-security.md).

En outre, les applications doivent utiliser le droit d’accès minimal en ne disposant que de l’accès en lecture des secrets. L’accès aux secrets peut être contrôlé soit par des stratégies d’accès, soit par le contrôle d’accès en fonction du rôle Azure. 

Pour plus d’informations sur le contrôle d’accès dans Azure Key Vault, consultez :
- [Donner accès aux clés, certificats et secrets du coffre de clés avec le contrôle d’accès en fonction du rôle Azure](../general/rbac-guide.md)
- [Attribuer une stratégie d’accès Key Vault](../general/assign-access-policy.md)
 
## <a name="service-limits-and-caching"></a>Limites et mise en cache du service
Key Vault a initialement été créé avec les limitations décrites dans [Limites du service Azure Key Vault](../general/service-limits.md). Pour maximiser vos débits, voici deux meilleures pratiques recommandées :
- Mettez les secrets en cache dans votre application pendant au moins huit heures.
- Implémentez une logique de nouvelle tentative avec backoff exponentiel pour gérer les scénarios lorsque les limites de service sont dépassées.

Pour plus d’informations sur les limitations, consultez [Aide sur la limitation de requêtes Azure Key Vault](../general/overview-throttling.md).

## <a name="monitoring"></a>Surveillance
Pour surveiller l’accès à vos secrets et leur cycle de vie, activez la journalisation de Key Vault. Utilisez [Azure Monitor](../../azure-monitor/overview.md) pour surveiller sur la même plateforme toutes les activités liées aux secrets dans tous vos coffres. Vous pouvez aussi utiliser [Azure Event Grid](../../event-grid/overview.md) pour surveiller le cycle de vie des secrets, car il s’intègre facilement à Azure Logic Apps et Azure Functions.

Pour plus d’informations, consultez :
- [Azure Key Vault en tant que source Event Grid](../../event-grid/event-schema-key-vault.md?tabs=event-grid-event-schema.md)
- [Journalisation d’Azure Key Vault](../general/logging.md)
- [Surveillance et alertes Azure Key Vault](../general/alert.md)

## <a name="backup-and-purge-protection"></a>Sauvegarde et protection contre le vidage
Activez la [protection contre le vidage](../general/soft-delete-overview.md#purge-protection) pour bénéficier d’une protection contre la suppression forcée du secret. Faites des sauvegardes régulières de votre coffre lorsque vous mettez à jour, supprimez ou créez des secrets dans un coffre.

- Pour en savoir plus sur la commande de sauvegarde Azure PowerShell, consultez [Sauvegarder un secret](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret).
- Pour en savoir plus sur la commande de sauvegarde Azure CLI, consultez [Sauvegarder un secret](/cli/azure/keyvault/secret#az_keyvault_secret_backup).

## <a name="learn-more"></a>En savoir plus
- [À propos des secrets Azure Key Vault](about-secrets.md)
- [Meilleures pratiques pour utiliser Key Vault](../general/best-practices.md)
