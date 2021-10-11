---
title: À propos des clés de compte de stockage gérées par Azure Key Vault - Azure Key Vault
description: Vue d’ensemble des clés de compte de stockage gérées par Azure Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 10/01/2021
ms.author: mbaldwin
ms.openlocfilehash: e2b72dd6960232dd3b9afd05ff19edccaeface12
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390652"
---
# <a name="about-azure-key-vault-managed-storage-account-keys"></a>À propos des clés de compte de stockage gérées par Azure Key Vault

> [!IMPORTANT]
> Nous vous recommandons d’utiliser l’intégration de Stockage Azure avec Azure Active Directory (Azure AD), le service Microsoft basé sur le cloud qui gère les identités et les accès. L’intégration d’Azure AD est disponible pour les [objets blob et les files d’attente Azure](../../storage/blobs/authorize-access-azure-active-directory.md) et fournit un accès basé sur les jetons OAuth2 au Stockage Azure (comme Azure Key Vault). Azure AD vous permet d’authentifier votre application cliente en utilisant une identité d’application ou d’utilisateur plutôt que les informations d’identification du compte de stockage. Vous pouvez utiliser une [identité Azure AD managée](../../active-directory/managed-identities-azure-resources/index.yml) lors de l’exécution sur Azure. Les identités managées suppriment l’authentification du client ainsi que le stockage des informations d’identification dans ou avec votre application. Utilisez la solution ci-dessous uniquement lorsque l’authentification Azure AD n’est pas possible.

Un compte de stockage Azure utilise des informations d’identification comprenant un nom de compte et une clé. La clé qui est générée automatiquement sert de mot de passe et non de clé de chiffrement. Key Vault gère les clés de compte de stockage en les regénérant régulièrement dans le compte de stockage. De plus, il fournit des jetons de signature d’accès partagé pour permettre un accès délégué aux ressources de votre compte de stockage.

Vous pouvez utiliser la fonctionnalité de clé de compte de stockage gérée de Key Vault pour lister (synchroniser) les clés avec un compte de stockage Azure et regénérer (faire tourner) régulièrement les clés. Vous pouvez gérer les clés des comptes de stockage et des comptes de stockage classiques.

## <a name="azure-storage-account-key-management"></a>Gestion des clés de compte de stockage Azure

Key Vault peut gérer les clés de [compte de stockage Azure](../../storage/common/storage-account-overview.md) :

- En interne, Key Vault peut lister (synchroniser) les clés avec un compte de stockage Azure. 
- Key Vault regénère (fait tourner) les clés régulièrement.
- Les valeurs de clés ne sont jamais retournées en réponse à l’appelant.
- Key Vault gère les clés des comptes de stockage et des comptes de stockage classiques.

Pour plus d'informations, consultez les pages suivantes :
- [Clés d’accès au compte de stockage](../../storage/common/storage-account-keys-manage.md)
- [Gestion des clés de compte de stockage dans Azure Key Vault](../secrets/overview-storage-keys.md)


## <a name="storage-account-access-control"></a>Contrôle d’accès aux comptes de stockage

Vous pouvez utiliser les autorisations suivantes quand vous autorisez un utilisateur ou un principal d’application à effectuer des opérations sur un compte de stockage géré :  

- Autorisations pour les opérations de définition SAS et de compte de stockage géré
  - *get* : obtenir des informations sur un compte de stockage 
  - *list* : lister les comptes de stockage gérés par un coffre de clés
  - *update* : mettre à jour un compte de stockage
  - *delete* : Suppression d'un compte de stockage  
  - *recover* : récupérer un compte de stockage supprimé
  - *backup* : sauvegarder un compte de stockage
  - *restore* : restaurer un compte de stockage sauvegardé sur un coffre de clés
  - *set* : créer ou mettre à jour un compte de stockage
  - *regeneratekey* : regénérer une valeur de clé spécifiée pour un compte de stockage
  - *getsas* : obtenir des informations sur une définition SAS pour un compte de stockage
  - *listsas* : lister les définitions SAS de stockage d’un compte de stockage
  - *deletesas* : supprimer une définition SAS d’un compte de stockage
  - *setsas* : créer ou mettre à jour une définition/des attributs SAS pour un compte de stockage

- Autorisations pour les opérations privilégiées
  - *purge* : effacer (supprimer définitivement) un compte de stockage géré

Pour plus d’informations, consultez [Informations de référence sur les opérations de compte de stockage dans l’API REST Key Vault](/rest/api/keyvault). Pour plus d’informations sur l’établissement d’autorisations, consultez [Coffres : créer ou mettre à jour](/rest/api/keyvault/vaults/createorupdate) et [Coffres : mettre à jour la stratégie d’accès](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Étapes suivantes

- [Gérer les clés de compte de stockage avec Key Vault et l’interface de ligne de commande Azure](overview-storage-keys.md)
- [À propos de Key Vault](../general/overview.md)
- [Présentation des clés, des secrets et des certificats](../general/about-keys-secrets-certificates.md)
- [Meilleures pratiques de gestion des secrets dans Key Vault](secrets-best-practices.md)
- [Guide du développeur Key Vault](../general/developers-guide.md)
