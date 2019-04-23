---
title: Sécurité d’Azure Key Vault | Microsoft Docs
description: Gérez les autorisations d’accès à Azure Key Vault, aux clés et aux secrets. Couvre le modèle d’authentification et d’autorisation de Key Vault, et explique comment sécuriser votre coffre de clés.
services: key-vault
documentationcenter: ''
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: barclayn
Customer intent: As a key vault administrator, I want to learn the options available to secure my vaults
ms.openlocfilehash: 43847b53fbf84fe42be3efdbd647767904a05fb8
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014281"
---
# <a name="azure-key-vault-security"></a>Sécurité d’Azure Key Vault

Vous devez protéger les clés de chiffrement et des secrets tels que les certificats, les chaînes de connexion et les mots de passe dans le cloud afin que vous utilisez Azure Key Vault. Étant donné que vous stockez sensibles et les données d’entreprise critiques, vous devez prendre des mesures pour optimiser la sécurité de vos coffres et les données stockées dans les. Cet article décrit certains des concepts dont vous devez tenir compte lors de la conception de la sécurité de votre Azure Key Vault.

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

Quand vous créez un coffre de clés dans un abonnement Azure, il est automatiquement associé au locataire Azure AD de l’abonnement. Toute personne qui essaie de gérer ou de récupérer le contenu à partir d’un coffre doit être authentifiée par Azure AD.

- L’authentification établit l’identité de l’appelant.
- L’autorisation détermine les opérations réalisables par l’appelant. Autorisation dans Key Vault utilise une combinaison de [contrôle d’accès en fonction du rôle](../role-based-access-control/overview.md) (RBAC) et les stratégies d’accès Azure Key Vault.

### <a name="access-model-overview"></a>Vue d’ensemble du modèle d’accès

Accès aux coffres a lieu via deux interfaces ou des plans. Ces plans sont le plan de gestion et le plan de données.

- Le *plan de gestion* où vous gérez Key Vault lui-même et il est l’interface utilisée pour créer et supprimer des coffres. Vous pouvez également lire les propriétés de coffre de clés et gérer les stratégies d’accès.
- Le *plan de données* permet de travailler avec les données stockées dans un coffre de clés. Vous pouvez ajouter, supprimer et modifier des clés, des secrets et des certificats.

Pour accéder à un coffre de clés dans un plan, tous les appelants (utilisateurs ou applications) doivent être authentifiées et autorisées. Les deux plans utilisent Azure Active Directory (Azure AD) pour l’authentification. Pour l’autorisation, le plan de gestion utilise le contrôle d’accès en fonction du rôle (RBAC), tandis que le plan de données s’appuie sur une stratégie d’accès Key Vault.

Le modèle d’un mécanisme d’authentification unique auprès des deux plans présente plusieurs avantages :

- Les organisations peuvent contrôler de manière centralisée l’accès à tous leurs coffres de clés.
- Si un utilisateur part, il perd instantanément l’accès à tous les coffres de clés de l’organisation.
- Les organisations peuvent personnaliser l’authentification en utilisant les options dans Azure AD, par exemple, pour activer l’authentification multifacteur pour renforcer la sécurité

### <a name="managing-administrative-access-to-key-vault"></a>Gérer l’accès administratif à Key Vault

Lorsque vous créez un coffre de clés dans un groupe de ressources, vous gérer l’accès à l’aide d’Azure AD. Vous autorisez des utilisateurs ou des groupes à gérer les coffres de clés dans un groupe de ressources. Vous pouvez accorder l’accès à un niveau de portée spécifique en assignant des rôles RBAC appropriés. Pour permettre à un utilisateur de gérer des coffres de clés, vous attribuez un rôle `key vault Contributor` prédéfini à l’utilisateur dans une étendue spécifique. Les niveaux d’étendue suivants peuvent être attribués à un rôle RBAC :

- **Abonnement**: un rôle RBAC attribué au niveau d’un abonnement s’applique à tous les groupes de ressources et à toutes les ressources au sein de cet abonnement.
- **Groupe de ressources** : un rôle RBAC attribué au niveau d’un groupe de ressources s’applique à toutes les ressources de ce groupe de ressources.
- **Ressource spécifique** : un rôle RBAC attribué pour une ressource spécifique s’applique à cette ressource. Dans ce cas, la ressource est un coffre de clés spécifique.

Il existe plusieurs rôles prédéfinis. Si un rôle prédéfini ne répond pas à vos besoins, vous pouvez définir votre propre rôle. Pour plus d’informations, consultez [RBAC : pour les ressources Azure](../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Si un utilisateur dispose d’autorisations `Contributor` sur un plan de gestion de coffre de clés, il peut s’accorder lui-même l’accès au plan de données en définissant une stratégie d’accès Key Vault. Vous devez contrôler étroitement qui dispose d’un accès accordé par le rôle `Contributor` à vos coffres de clés. Vérifiez que seules les personnes autorisées peuvent accéder à et gérer vos coffres de clés, vos clés, vos secrets et vos certificats.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Contrôler l’accès aux données Key Vault

Stratégies d’accès Key Vault accordent des autorisations séparément pour les clés, secrets ou certificat. Vous pouvez uniquement accorder un accès utilisateur à des clés (pas à des secrets). Autorisations d’accès pour les clés, secrets et certificats sont gérées au niveau du coffre.

> [!IMPORTANT]
> Les stratégies d’accès Key Vault ne prennent pas en charge les autorisations granulaires au niveau des objets, comme une clé, un secret ou un certificat spécifique. Quand un utilisateur est autorisé à créer et à supprimer des clés, il peut effectuer ces opérations sur toutes les clés de ce coffre de clés.

Pour définir des stratégies d’accès pour un coffre de clés, utilisez le [portail Azure](https://portal.azure.com/), l’interface [Azure CLI](../cli-install-nodejs.md), [Azure PowerShell](/powershell/azureps-cmdlets-docs) ou les [API REST de gestion Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx).

Vous pouvez limiter l’accès au plan de données en utilisant des [points de terminaison de service de réseau virtuel pour Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). Vous pouvez configurer [des pare-feu et des règles de réseau virtuel](key-vault-network-security.md) pour mettre en place une couche de sécurité supplémentaire.

## <a name="network-access"></a>Accès réseau

Vous pouvez réduire l’exposition de vos coffres en spécifiant les adresses IP puissent y accéder. Les points de terminaison de service de réseau virtuel pour Azure Key Vault permettent de restreindre l’accès à un réseau virtuel spécifié. Les points de terminaison vous permettent également de restreindre l’accès à une liste de plages d’adresses IPv4 (Internet Protocol version 4). L’accès est refusé à tout utilisateur se connectant à votre coffre de clés en dehors de ces sources.

Après avoir pare-feu les règles sont en effet, les utilisateurs peuvent uniquement lire les données à partir de Key Vault lorsque leurs demandes proviennent de réseaux virtuels autorisés ou des plages d’adresses IPv4. Ceci s’applique également à l’accès au coffre de clés à partir du portail Azure. Si des utilisateurs peuvent accéder à un coffre de clés à partir du portail Azure, il ne peuvent pas lister les clés/secrets/certificats si leur ordinateur client ne figure pas dans la liste autorisée. Ceci affecte également l’accès au sélecteur de compte Key Vault par d’autres services Azure. Des utilisateurs qui peuvent voir la liste des coffres de clés ne peuvent pas lister les clés si des règles de pare-feu bloquent leur ordinateur client.

Pour plus d’informations sur Azure Key Vault de révision de l’adresse réseau [points de terminaison service de réseau virtuel pour Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)

## <a name="monitoring"></a>Surveillance

Journalisation de Key Vault enregistre les informations sur les activités effectuées sur votre coffre. Journaux de Key Vault :

- Toutes les authentifiées requêtes d’API REST, y compris les demandes ayant échoués
  - Opérations sur la clé de coffre lui-même. Ces opérations comprennent la création, suppression, stratégies d’accès de paramètre et la mise à jour des attributs de coffre de clés tels que les balises.
  - Opérations sur les clés et secrets dans le coffre de clés, y compris :
    - Création, modification ou la suppression de ces clés ou ces secrets.
    - Signature, vérification, chiffrement, déchiffrement, encapsulant et désencapsuler les clés, l’obtention des secrets et liste de clés et secrets (et leurs versions).
- les requêtes non authentifiées qui génèrent une réponse 401. Exemples sont des requêtes qui n’ont pas un jeton du porteur, qui sont mal formé ou a expiré ou qui ont un jeton non valide.

Informations de journalisation sont accessibles dans les 10 minutes après l’opération de coffre de clés. C’est à vous de gérer vos journaux dans votre compte de stockage. 

- Utilisez les méthodes de contrôle d’accès Azure standard pour assurer la sécurité de vos journaux d’activité en limitant l’accès à ces derniers.
- Supprimez les journaux d’activité que vous ne souhaitez plus conserver dans votre compte de stockage.

Pour la recommandation sur la gestion du stockage en toute sécurité comptes passez en revue le [guide de sécurité Azure Storage](../storage/common/storage-security-guide.md)

## <a name="next-steps"></a>Étapes suivantes

- [Les points de terminaison de service réseau virtuel pour Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
- [Contrôle d’accès en fonction du rôle (RBAC) : Rôles intégrés](../role-based-access-control/built-in-roles.md)
- [Les points de terminaison de service réseau virtuel pour Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
