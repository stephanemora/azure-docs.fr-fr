---
title: Meilleures pratiques pour utiliser Key Vault - Azure Key Vault | Microsoft Docs
description: Découvrez les meilleures pratiques pour Azure Key Vault, notamment le contrôle de l’accès, quand utiliser les différentes options de coffre de clés, de sauvegarde, de journalisation et de récupération.
services: key-vault
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: mbaldwin
ms.openlocfilehash: 5e4ec2749cec8444382e2b6e7c856e74c57ebb59
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777164"
---
# <a name="best-practices-to-use-key-vault"></a>Meilleures pratiques pour utiliser Key Vault

## <a name="use-separate-key-vaults"></a>Utiliser des coffres de clés distincts

Nous recommandons d’utiliser un coffre par application et par environnement (développement, préproduction et production). Cela vous aide à éviter le partage de secrets entre environnements et réduit la menace en cas de violation.

## <a name="control-access-to-your-vault"></a>Contrôler l’accès à votre coffre

Azure Key Vault est un service cloud qui protège les clés et secrets de chiffrement comme les certificats, chaînes de connexion et mots de passe. Comme il s’agit de données sensibles et critiques, vous devez sécuriser l’accès à vos coffres de clés en acceptant seulement les applications et les utilisateurs autorisés. Cet [article](security-overview.md) fournit une vue d’ensemble du modèle d’accès aux coffres de clés. Il décrit l’authentification et l’autorisation, puis explique comment sécuriser l’accès à vos coffres de clés.

Voici quelques suggestions concernant le contrôle de l’accès à votre coffre :
1. Verrouiller l’accès à votre abonnement, au groupe de ressources et aux coffres de clés (Azure RBAC)
2. Créer des stratégies d’accès pour chaque coffre
3. Utiliser le principe des privilèges d’accès minimum pour accorder l’accès
4. Activer le pare-feu et les [points de terminaison de service de réseau virtuel](overview-vnet-service-endpoints.md)

## <a name="backup"></a>Sauvegarde

Veillez à effectuer des sauvegardes régulières de votre coffre lors de la mise à jour, de la suppression ou de la création d’objets au sein d’un coffre.

### <a name="azure-powershell-backup-commands"></a>Utilisation de commandes de sauvegarde Azure PowerShell

* [Certificat de sauvegarde](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate)
* [Clé de sauvegarde](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey)
* [Secret de sauvegarde](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)

### <a name="azure-cli-backup-commands"></a>Interface de ligne de commande Azure de sauvegarde

* [Certificat de sauvegarde](/cli/azure/keyvault/certificate#az_keyvault_certificate_backup)
* [Clé de sauvegarde](/cli/azure/keyvault/key#az_keyvault_key_backup)
* [Secret de sauvegarde](/cli/azure/keyvault/secret#az_keyvault_secret_backup)


## <a name="turn-on-logging"></a>Activer la journalisation

[Activez la journalisation](logging.md) pour votre coffre. Configurez également des alertes.

## <a name="turn-on-recovery-options"></a>Activer les options de récupération

1. Activez la [suppression réversible](soft-delete-overview.md).
2. Activez la protection contre le vidage si vous souhaitez vous prémunir contre la suppression forcée du secret ou du coffre, même après activation de la suppression réversible.