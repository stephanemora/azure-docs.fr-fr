---
title: Meilleures pratiques pour la gestion des secrets - Azure Key Vault | Microsoft Docs
description: En savoir plus sur les meilleures pratiques pour la gestion des secrets Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: mbaldwin
ms.openlocfilehash: ae4d2bd17dc1a233be71f48ebd106f1db2a31de6
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094459"
---
# <a name="best-practices-for-secrets-management-in-key-vault"></a>Meilleures pratiques de gestion des secrets dans Key Vault

Azure Key Vault permet de stocker en toute sécurité les informations d’identification du service ou de l’application, comme les mots de passe, les clés d’accès en tant que secrets. Tous les secrets de votre Key Vault sont stockés sous forme chiffrée avec une clé de logiciel. Grâce à Key Vault, les développeurs d’applications n’ont plus besoin de stocker les informations de sécurité dans leur application. Ne pas avoir à stocker les informations de sécurité dans les applications élimine le besoin d’intégrer ces informations au code. 

Exemples de secrets qui doivent être stockés dans Key Vault :

- Secrets des applications clientes
- Chaînes de connexion
- Mots de passe
- Clés d’accès (Cache Redis, Event Hub, Cosmos DB)
- Clés SSH

Toutes les autres informations sensibles, comme les adresses IP, les noms de service et d’autres paramètres de configuration, doivent être stockées dans [Azure App Configuration](../../azure-app-configuration/overview.md).

Chaque coffre de clés individuel définit la limite de sécurité pour les secrets. Un coffre de clés unique par application, par région et par environnement est recommandé pour fournir une isolation granulaire des secrets pour l’application. 

Pour plus d’informations sur les meilleures pratiques pour le coffre de clés, consultez :
- [Meilleures pratiques pour utiliser Key Vault](../general/best-practices.md)

## <a name="configuration-and-storing"></a>Configuration et stockage 

Il est recommandé de stocker des parties dynamiques des informations d’identification générées lors de la rotation, comme les secrets d’application cliente, les mots de passe, les clés d’accès dans le secret comme valeur. Tous les attributs et identificateurs statiques associés tels que les noms d’utilisateur, les AppId, les URL de service doivent être stockés en tant que balises secrètes et copiés dans la nouvelle version d’un secret lors de la rotation.

Pour plus d’informations sur les secrets, consultez :
- [À propos des secrets Azure Key Vault](about-secrets.md)

## <a name="secrets-rotation"></a>Rotation des secrets
Les secrets sont souvent stockés dans la mémoire de l’application en tant que variables d’environnement ou paramètres de configuration pour l’ensemble du cycle de vie de l’application, ce qui les rend sensibles à une exposition indésirable. Étant donné que les secrets sont sensibles à la fuite ou à l’exposition, il est important de permuter souvent les clés secrètes, au moins tous les 60 jours. 

Pour plus d’informations sur le processus de permutation des secrets, consultez :
- [Automatiser la permutation d’un secret pour des ressources qui ont deux jeux d’informations d’authentification](tutorial-rotation-dual.md) 

## <a name="access-and-network-isolation"></a>Accès et isolement réseau

Vous pouvez réduire l’exposition de vos coffres en spécifiant les adresses IP qui y ont accès. Configurez le pare-feu pour autoriser uniquement les applications et les services connexes à accéder aux secrets dans le coffre afin de réduire la capacité des attaquants à accéder aux secrets. 

Pour plus d'informations sur la sécurité réseau, consultez :
- [Configurer les paramètres de mise en réseau Azure Key Vault](../general/how-to-azure-key-vault-network-security.md)

En outre, les applications doivent utiliser le droit d’accès minimal en ne disposant que de l’accès en lecture des secrets. L’accès aux secrets peut être contrôlé soit avec des stratégies d’accès ou le contrôle d’accès en fonction du rôle Azure. 

Pour plus d’informations sur le contrôle d’accès dans Azure Key Vault, consultez :
- [Donnez accès aux clés, certificats et secrets du coffre de clés avec un contrôle d’accès en fonction du rôle Azure](../general/rbac-guide.md)
- [Attribuer une stratégie d’accès Key Vault](../general/assign-access-policy.md)
 
## <a name="service-limits-and-caching"></a>Limites et mise en cache du service
Key Vault a initialement été créé avec les limites décrites dans [Limites du service Azure Key Vault](../general/service-limits.md). Pour optimiser votre débit Key Vault, voici quelques conseils et bonnes pratiques :
- Mettez en cache les secrets dans l’application pendant au moins 8 heures
- Implémenter une logique de nouvelle tentative de backoff exponentiel pour gérer les scénarios lorsque les limites de service sont dépassées

Pour plus d’informations sur le guide de la limitation, consultez :
- [Aide sur la limitation de requêtes Azure Key Vault](../general/overview-throttling.md)

## <a name="monitoring"></a>Surveillance
Activez la journalisation de votre coffre pour surveiller l’accès à vos secrets et à leur cycle de vie. Vous pouvez utiliser [Azure Monitor](https://docs.microsoft.com//azure/azure-monitor/overview) pour surveiller toutes les activités de secrets de tous vos coffres dans un même emplacement ou [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) pour surveiller le cycle de vie des secrets avec une intégration facile avec Logic Apps et Azure Functions.

Pour plus d’informations, consultez :
- [Azure Key Vault en tant que source Event Grid](https://docs.microsoft.com/azure/event-grid/event-schema-key-vault?tabs=event-grid-event-schema)
- [Journalisation d’Azure Key Vault](../general/logging.md)
- [Surveillance et alertes Azure Key Vault](../general/alert.md)

## <a name="backup-and-purge-protection"></a>Sauvegarde et protection contre le vidage
Activez la [protection contre le vidage](../general/soft-delete-overview.md#purge-protection) pour bénéficier d’une protection contre la suppression forcée du secret. Veillez à effectuer des sauvegardes régulières de votre coffre lors de la mise à jour, de la suppression ou de la création de secrets au sein d’un coffre.

### <a name="azure-powershell-backup-commands"></a>Utilisation de commandes de sauvegarde Azure PowerShell

* [Secret de sauvegarde](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)

### <a name="azure-cli-backup-commands"></a>Interface de ligne de commande Azure de sauvegarde

* [Secret de sauvegarde](/cli/azure/keyvault/secret#az_keyvault_secret_backup)

## <a name="learn-more"></a>En savoir plus
- [À propos des secrets Azure Key Vault](about-secrets.md)
- [Meilleures pratiques pour utiliser Key Vault](../general/best-practices.md)
