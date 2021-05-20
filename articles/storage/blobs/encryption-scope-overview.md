---
title: Étendues de chiffrement pour le stockage d'objets blob
description: Les étendues de chiffrement permettent de gérer le chiffrement au niveau d’un objet blob ou d’un conteneur individuel. Vous pouvez utiliser des étendues de chiffrement pour créer des limites sécurisées entre les données qui résident dans le même compte de stockage mais qui appartiennent à des clients différents.
services: storage
author: tamram
ms.service: storage
ms.date: 05/10/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: a583af2811facf1cf8037fa0dd3fb9982fba60c4
ms.sourcegitcommit: b35c7f3e7f0e30d337db382abb7c11a69723997e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109685672"
---
# <a name="encryption-scopes-for-blob-storage"></a>Étendues de chiffrement pour le stockage d'objets blob

Les étendues de chiffrement vous permettent de gérer le chiffrement à l'aide d'une clé étendue à un conteneur ou à un objet blob individuel. Vous pouvez utiliser des étendues de chiffrement pour créer des limites sécurisées entre les données qui résident dans le même compte de stockage mais qui appartiennent à des clients différents.

Pour plus d'informations sur l'utilisation des étendues de chiffrement, consultez [Créer et gérer des étendues de chiffrement](encryption-scope-manage.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-encryption-scopes-work"></a>Fonctionnement des étendues de chiffrement

Par défaut, un compte de stockage est chiffré avec une clé étendue au compte de stockage entier. Lorsque vous définissez une étendue de chiffrement, vous spécifiez une clé qui peut être étendue à un conteneur ou à un objet blob individuel. Lorsque l'étendue de chiffrement est appliquée à un objet blob, celui-ci est chiffré à l'aide de cette clé. Lorsque l'étendue de chiffrement est appliquée à un conteneur, elle sert d'étendue par défaut aux objets blob de ce conteneur ; par conséquent, tous les objets blob chargés dans ce conteneur sont chiffrés avec la même clé. Le conteneur peut être configuré pour appliquer l'étendue de chiffrement par défaut à tous ses objets blob, ou pour autoriser le chargement d'un objet blob individuel dans le conteneur avec une étendue de chiffrement différente de la valeur par défaut.

Les opérations de lecture sur un objet blob qui a été créé avec une étendue de chiffrement se déroulent de façon transparente, tant que l'étendue de chiffrement n'est pas désactivée.

### <a name="key-management"></a>Gestion des clés

Lorsque vous définissez une étendue de chiffrement, vous pouvez spécifier si elle est protégée par une clé gérée par Microsoft ou par une clé gérée par le client stockée dans Azure Key Vault. Différentes étendues de chiffrement sur le même compte de stockage peuvent utiliser des clés gérées par Microsoft ou par le client. En outre, vous pouvez à tout moment basculer le type de clé utilisé pour protéger une étendue de chiffrement d'une clé gérée par le client vers une clé gérée par Microsoft, ou vice versa. Pour plus d'informations sur les clés gérées par le client, consultez [Clés gérées par le client pour le chiffrement du Stockage Azure](../common/customer-managed-keys-overview.md). Pour plus d’informations sur les clés managées par Microsoft, consultez [À propos de la gestion des clés de chiffrement](../common/storage-service-encryption.md#about-encryption-key-management).

Si vous définissez une étendue de chiffrement avec une clé gérée par le client, vous pouvez choisir de mettre à jour la version de la clé automatiquement ou manuellement. Si vous optez pour la mise à jour automatique de la version de la clé, le service Stockage Azure recherche quotidiennement dans le coffre de clés ou dans le HSM managé la présence d'une nouvelle version de la clé gérée par le client et procède automatiquement à la mise jour. Pour plus d'informations sur la mise à jour d'une clé gérée par le client, consultez [Mettre à jour la version de la clé](../common/customer-managed-keys-overview.md#update-the-key-version).

Un compte de stockage peut comporter jusqu'à 10 000 étendues de chiffrement protégées par des clés gérées par le client dont la version est automatiquement mise à jour. Si votre compte de stockage comporte déjà 10 000 étendues de chiffrement protégées par des clés gérées par le client qui sont mises à jour automatiquement, la version des clés doit être mise à jour manuellement pour toutes les étendues de chiffrement supplémentaires protégées par des clés gérées par le client.  

### <a name="infrastructure-encryption"></a>Chiffrement d’infrastructure

Le chiffrement de l’infrastructure dans le stockage Azure permet un chiffrement double des données. Avec le chiffrement d’infrastructure, les données sont chiffrées deux fois &mdash; une fois au niveau du service et une fois au niveau de l’infrastructure &mdash; avec deux algorithmes de chiffrement et deux clés différents.

Le chiffrement d’infrastructure est pris en charge pour une étendue de chiffrement, ainsi qu’au niveau du compte de stockage. Si le chiffrement d’infrastructure est activé pour un compte, toute étendue de chiffrement créée sur ce compte utilise automatiquement le chiffrement d’infrastructure. Si le chiffrement d’infrastructure n’est pas activé au niveau du compte, vous avez la possibilité de l’activer pour une étendue de chiffrement au moment où vous créez l’étendue. Le paramètre de chiffrement d’infrastructure d’une étendue de chiffrement ne peut pas être modifié après la création de l’étendue.

Pour plus d’informations sur le chiffrement d’infrastructure, consultez [Activer le chiffrement d’infrastructure pour le chiffrement double des données](../common/infrastructure-encryption-enable.md).

### <a name="encryption-scopes-for-containers-and-blobs"></a>Étendues de chiffrement pour les conteneurs et les objets blob

Lorsque vous créez un conteneur, vous pouvez spécifier une étendue de chiffrement par défaut pour les objets blob qui y seront chargés. Lorsque vous spécifiez une étendue de chiffrement par défaut pour un conteneur, vous pouvez déterminer le mode d'application de celle-ci :

- Vous pouvez exiger que tous les objets blob chargés dans le conteneur utilisent l'étendue de chiffrement par défaut. Dans ce cas, tous les objets blob du conteneur sont chiffrés avec la même clé.
- Vous pouvez autoriser un client à remplacer l'étendue de chiffrement par défaut du conteneur, afin qu'un objet blob puisse être chargé avec une étendue de chiffrement autre que l'étendue par défaut. Dans ce cas, les objets blob du conteneur peuvent être chiffrés avec des clés différentes.

Le tableau suivant récapitule le comportement d'une opération de chargement d'objets blob en fonction de la façon dont l'étendue de chiffrement par défaut est configurée pour le conteneur :

| L'étendue de chiffrement définie sur le conteneur est... | Chargement d'un objet blob avec l'étendue de chiffrement par défaut... | Chargement d'un objet blob avec une étendue de chiffrement autre que l'étendue par défaut... |
|--|--|--|
| Une étendue de chiffrement par défaut avec remplacements autorisés | Réussite | Réussite |
| Une étendue de chiffrement par défaut avec remplacements interdits | Réussite | Échecs |

L'étendue de chiffrement par défaut doit être spécifiée au moment de la création du conteneur.

Si aucune étendue de chiffrement par défaut n'est spécifiée pour le conteneur, vous pouvez charger un objet blob à l'aide de n'importe quelle étendue de chiffrement définie pour le compte de stockage. L'étendue de chiffrement doit être spécifiée au moment du chargement de l'objet blob.

## <a name="disabling-an-encryption-scope"></a>Désactiver une étendue de chiffrement

Lorsque vous désactivez une étendue de chiffrement, toutes les opérations de lecture ou d’écriture ultérieures effectuées avec l’étendue de chiffrement échouent avec le code d’erreur HTTP 403 (Interdit). Si vous réactivez l’étendue de chiffrement, les opérations de lecture et d’écriture se poursuivent normalement.

Quand une étendue de chiffrement est désactivée, vous n’êtes plus facturé pour celle-ci. Désactivez les étendues de chiffrement qui ne sont pas nécessaires pour éviter les frais inutiles.

Si votre étendue de chiffrement est protégée par une clé gérée par le client et que vous supprimez cette clé dans le coffre de clés, les données deviennent inaccessibles. Veillez également à désactiver l'étendue de chiffrement afin de ne pas être facturé pour celle-ci.

Gardez à l’esprit que les clés gérées par le client sont protégées par la suppression réversible et la protection contre le vidage dans le coffre de clés, et qu’une clé supprimée est soumise au comportement défini par ces propriétés. Pour plus d'informations, consultez les rubriques suivantes dans la documentation d’Azure Key Vault :

- [Guide pratique pour utiliser la suppression réversible avec Power​Shell](../../key-vault/general/key-vault-recovery.md)
- [Guide pratique pour utiliser la suppression réversible avec Azure CLI](../../key-vault/general/key-vault-recovery.md)

> [!IMPORTANT]
> Il n’est pas possible de supprimer une étendue de chiffrement.

## <a name="next-steps"></a>Étapes suivantes

- [Chiffrement du stockage Azure pour les données au repos](../common/storage-service-encryption.md)
- [Créer et gérer des étendues de chiffrement](encryption-scope-manage.md)
- [Clés gérées par le client pour le chiffrement du service Stockage Azure](../common/customer-managed-keys-overview.md)
- [Qu’est-ce qu’Azure Key Vault ?](../../key-vault/general/overview.md)