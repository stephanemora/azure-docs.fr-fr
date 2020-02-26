---
title: Résolution des problèmes de chiffrement des données pour Azure Database pour PostgreSQL - Serveur unique
description: Découvrez comment résoudre les problèmes du chiffrement des données pour Azure Database pour PostgreSQL - Serveur unique
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 6d2e027c47bef3186f95c2183b316b5c15511070
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371471"
---
# <a name="troubleshooting-data-encryption-with-customer-managed-keys-in-azure-database-for-postgresql---single-server"></a>Résolution des problèmes de chiffrement des données avec des clés gérées par le client dans Azure Database pour PostgreSQL - Serveur unique
Cet article explique comment identifier et résoudre les erreurs/problèmes courants qui se produisent sur une instance Azure Database pour PostgreSQL - Serveur unique configurée avec le chiffrement des données à l’aide d’une clé gérée par le client.

## <a name="introduction"></a>Introduction
Lorsque le chiffrement des données est configuré pour utiliser une clé gérée par le client dans Azure Key Vault, l’accès continu à cette clé est requis pour que le serveur reste disponible. Si le serveur perd l’accès à la clé gérée par le client dans Azure Key Vault, il se met à rejeter toutes les connexions en affichant le message d’erreur associé, et son état passe à ***Inaccessible*** dans le portail Azure.

Si une instance Azure Database pour PostgreSQL - Serveur unique inaccessible n’est plus nécessaire, elle peut être supprimée immédiatement pour arrêter les coûts. Aucune autre action sur le serveur n’est autorisée tant que l’accès au coffre de clés Azure n’a pas été restauré et que le serveur n’est pas de nouveau disponible. Le remplacement de l’option de chiffrement des données « Oui » (gestion par le client) par « Non » (gestion par le service) sur un serveur inaccessible n’est pas non plus possible quand un serveur est chiffré avec une clé gérée par le client. Vous devez revalider la clé manuellement pour que le serveur soit à nouveau disponible. C’est nécessaire pour protéger les données contre tout accès non autorisé alors que les autorisations sur la clé gérée par le client ont été révoquées.

## <a name="common-errors-causing-server-to-become-inaccessible"></a>Erreurs courantes provoquant l’inaccessibilité du serveur

La plupart des problèmes qui se produisent lorsque vous utilisez le chiffrement des données avec Azure Key Vault sont provoqués par l’une des erreurs de configuration suivantes :

Le coffre de clés n’est pas disponible ou n’existe pas

* Le coffre de clés a été supprimé accidentellement.
* Une erreur réseau intermittente entraîne l’indisponibilité du coffre de clés.

Aucune autorisation d’accéder au coffre de clés ou la clé n’existe pas

* La clé a été accidentellement supprimée, désactivée ou a expiré.
* L’identité managée par l’instance Azure Database pour PostgreSQL - Serveur unique a été supprimée par erreur.
* Les autorisations accordées à l’identité managée Azure Database pour PostgreSQL pour les clés ne sont pas suffisantes (Obtenir, Inclure la clé et Ne pas inclure la clé ne sont pas comprises).
* Les autorisations pour l’identité managée par l’instance de serveur unique Azure Database pour PostgreSQL ont été révoquées ou supprimées.

## <a name="identify-and-resolve-common-errors"></a>Identifier et résoudre les erreurs courantes
### <a name="errors-on-the-key-vault"></a>Erreurs sur le coffre de clés

#### <a name="disabled-key-vault"></a>Coffre de clés désactivé
* AzureKeyVaultKeyDisabledMessage
* **Explication** : Impossible d’effectuer l’opération sur le serveur, car la clé Azure Key Vault est désactivée.

#### <a name="missing-key-vault-permissions"></a>Autorisations d’accès au coffre de clés manquantes
* AzureKeyVaultMissingPermissionsMessage
* Le serveur ne dispose pas des autorisations Obtenir, Inclure la clé et Ne pas inclure la clé requises pour les autorisations Azure Key Vault. Accordez les autorisations manquantes au principal du service avec l’ID.

### <a name="mitigation"></a>Limitation des risques
* Vérifiez que la clé gérée par le client est présente dans Key Vault :
* Identifiez le coffre de clés et accédez à celui-ci dans le portail Azure.
* Vérifiez que la clé identifiée par l’URI de la clé est présente.


## <a name="next-steps"></a>Étapes suivantes
[Configurer le chiffrement des données avec une clé gérée par le client pour votre instance Azure Database pour PostgreSQL à l’aide du portail Azure](howto-data-encryption-portal.md).