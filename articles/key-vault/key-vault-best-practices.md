---
title: Meilleures pratiques pour utiliser Key Vault - Azure Key Vault | Microsoft Docs
description: Ce document décrit certaines des meilleures pratiques pour utiliser Key Vault
services: key-vault
documentationcenter: ''
author: yvprashanth
manager: barbkess
tags: azure-key-vault
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: prashanthyv
ms.openlocfilehash: 85bd1858bc3f2d505e1e4d0a88e8c77a46ae4447
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2019
ms.locfileid: "57887968"
---
# <a name="best-practices-to-use-key-vault"></a>Meilleures pratiques pour utiliser Key Vault

## <a name="control-access-to-your-vault"></a>Contrôler l’accès à votre coffre

Azure Key Vault est un service cloud qui protège les clés et secrets de chiffrement comme les certificats, chaînes de connexion et mots de passe. Comme il s’agit de données sensibles et critiques, vous devez sécuriser l’accès à vos coffres de clés en acceptant seulement les applications et les utilisateurs autorisés. Cela [article](key-vault-secure-your-key-vault.md) fournit une vue d’ensemble du modèle d’accès Key Vault. Il décrit l’authentification et l’autorisation, puis explique comment sécuriser l’accès à vos coffres de clés.

Suggestions tout en contrôlant l’accès à votre coffre sont les suivantes :
1. Verrouiller l’accès à votre abonnement, le groupe de ressources et la clé de coffres (RBAC)
2. Créer des stratégies d’accès pour chaque coffre
3. Utiliser moins principal de l’accès de privilèges pour accorder l’accès
4. Activer le pare-feu et [points de terminaison de Service réseau virtuel](key-vault-overview-vnet-service-endpoints.md)

## <a name="use-separate-key-vault"></a>Utiliser le coffre de clés distinctes

Notre recommandation est d’utiliser un coffre par application et par environnement (développement, préproduction et Production). Cela vous permet de ne partager pas les secrets dans des environnements et permet également de réduire la menace en cas de violation.

## <a name="backup-your-vault"></a>Votre coffre de sauvegarde

Veillez à prendre regular retour ups de votre [coffre](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) sur la mise à jour, supprimer ou créer des objets au sein d’un coffre.

## <a name="turn-on-logging"></a>Activer la journalisation

[Activer la journalisation](key-vault-logging.md) pour votre coffre. Également configurer des alertes.

## <a name="turn-on-recovery-options"></a>Activer les options de récupération

1. Activer [suppression réversible](key-vault-ovw-soft-delete.md).
2. Activer la protection de purge si vous souhaitez protéger contre la suppression de force de la clé secrète / coffre même après que la suppression réversible est activée.
