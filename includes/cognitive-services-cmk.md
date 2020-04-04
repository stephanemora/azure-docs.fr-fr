---
title: Fichier include
description: Fichier include
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: e5e1755e6351d308c041de5cefe943e9874c8ebd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372022"
---
### <a name="enable-customer-managed-keys"></a>Activer des clés gérées par le client

Vous devez utiliser Azure Key Vault pour stocker vos clés managées par le client. Vous pouvez créer vos propres clés et les stocker dans un coffre de clés, ou utiliser les API d’Azure Key Vault pour générer des clés. La ressource Cognitive Services et le coffre de clés doivent se trouver dans la même région et dans le même locataire Azure Active Directory, mais ils peuvent appartenir à des abonnements différents. Pour plus d’informations sur Azure Key Vault, consultez [Qu’est-ce qu’Azure Key Vault ?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Une nouvelle ressource Cognitive Services est toujours chiffrée à l'aide de clés gérées par Microsoft. Il est impossible d'activer des clés gérées par le client au moment de la création de la ressource. Les clés gérées par le client sont stockées dans Azure Key Vault, et le coffre de clés doit être configuré avec des stratégies d’accès qui accordent des autorisations de clé à l’identité managée associée à la ressource Cognitive Services. L’identité managée est disponible uniquement une fois la ressource créée à l’aide du niveau tarifaire pour la CMK.

Pour en savoir plus sur l’utilisation des clés gérées par le client avec Azure Key Vault pour le chiffrement Cognitive Services, consultez :

- [Configurer les clés gérées par le client avec Key Vault pour le chiffrement Cognitive Services depuis le portail Azure](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)

L'activation des clés gérées par le client active également une identité managée affectée par le système, une fonctionnalité d'Azure AD. Une fois activée, l'identité managée affectée par le système est inscrite auprès d'Azure Active Directory. Une fois inscrite, elle a accès au coffre de clés sélectionné lors de la configuration des clés gérées par le client. Découvrez-en plus sur les [identités managées](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Si vous désactivez les identités managées affectées par le système, l'accès au coffre de clés est supprimé et toutes les données chiffrées avec les clés client deviennent inaccessibles. Toutes les fonctionnalités dépendant de ces données cessent de fonctionner.

> [!IMPORTANT]
> Les identités managées ne prennent actuellement pas en charge les scénarios entre annuaires. Lorsque vous configurez des clés managées par le client sur le portail Azure, une identité managée est automatiquement affectée. Si, par la suite, vous déplacez l'abonnement, le groupe de ressources ou la ressource d'un répertoire Azure AD vers un autre, l'identité managée associée à la ressource n'est pas transférée vers le nouveau locataire. Par conséquent, les clés gérées par le client peuvent ne plus fonctionner. Pour plus d’informations, consultez **Transfert d’un abonnement entre des répertoires Azure AD** dans [FAQ et problèmes connus en lien avec les identités managées pour ressources Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Stocker les clés gérées par le client dans Azure Key Vault

Pour activer les clés gérées par le client, vous devez utiliser une instance d'Azure Key Vault afin de stocker vos clés. Vous devez activer les propriétés **Suppression réversible** et **Ne pas vider** sur le coffre de clés.

Seules les clés RSA de taille 2048 sont prises en charge par le chiffrement Cognitive Services. Pour plus d’informations sur les clés, consultez **Clés Key Vault** dans [À propos des clés, des secrets et des certificats Azure Key Vault](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

### <a name="rotate-customer-managed-keys"></a>Permuter des clés gérées par le client

Vous pouvez permuter une clé gérée par le client dans Azure Key Vault en fonction de vos stratégies de conformité. Une fois la rotation de la clé effectuée, vous devez mettre à jour la ressources Cognitive Services pour utiliser le nouvel URI de clé. Pour savoir comment mettre à jour la ressource afin d’utiliser une nouvelle version de la clé dans le portail Azure, consultez la section intitulée **Mettre à jour la version de la clé** dans [Configurer les clés gérées par le client pour Cognitive Services à l’aide du portail Azure](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md).

La rotation de la clé ne déclenche pas de rechiffrement de données dans la ressource. Aucune autre action n’est requise de la part de l’utilisateur.

### <a name="revoke-access-to-customer-managed-keys"></a>Révoquer l’accès aux clés gérées par le client

Pour révoquer l’accès aux clés gérées par le client, utilisez PowerShell ou Azure CLI. Pour plus d’informations, consultez [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) ou [Interface de ligne de commande Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault). La révocation de l’accès bloque l’accès à toutes les données de la ressource Cognitive Services, car Cognitive Services n’a pas accès à la clé de chiffrement.


