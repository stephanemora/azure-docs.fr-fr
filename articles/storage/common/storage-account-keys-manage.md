---
title: Gérer les touches d’accès rapide au compte
titleSuffix: Azure Storage
description: Découvrez comment consulter, gérer et permuter les clés d’accès de vos comptes de stockage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.openlocfilehash: 13adf6de420b54299d04a226dab81e75cbb9fef2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75975781"
---
# <a name="manage-storage-account-access-keys"></a>Gérer les clés d’accès au compte de stockage

Quand vous créez un compte de stockage, Azure génère deux clés d’accès 512 bits pour ce compte. Ces clés peuvent être utilisées pour autoriser l’accès aux données de votre compte de stockage par le biais de l’autorisation de clé partagée.

Microsoft vous recommande d’utiliser Azure Key Vault pour gérer vos clés d’accès, ainsi que de permuter et de regénérer régulièrement vos clés. L’utilisation d’Azure Key Vault facilite la permutation de vos clés sans interruption de vos applications. Vous pouvez également permuter manuellement vos clés.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-access-keys-and-connection-string"></a>Consulter les clés d’accès et la chaîne de connexion

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Utiliser Azure Key Vault pour gérer vos clés d’accès

Microsoft recommande d’utiliser Azure Key Vault pour gérer et permuter vos clés d’accès. Votre application peut accéder de manière sécurisée à vos clés dans Key Vault, afin que vous puissiez éviter de les stocker avec votre code d’application. Pour plus d’informations sur l’utilisation de Key Vault pour la gestion des clés, consultez les articles suivants :

- [Gérer les clés de compte de stockage avec Azure Key Vault et PowerShell](../../key-vault/key-vault-overview-storage-keys-powershell.md)
- [Gérer les clés de compte de stockage avec Azure Key Vault et Azure CLI](../../key-vault/key-vault-ovw-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Permuter des clés d’accès manuellement

Microsoft vous recommande de permuter vos clés d’accès régulièrement pour garantir une sécurité optimale de votre compte de stockage. Si possible, utilisez Azure Key Vault pour gérer vos clés d’accès. Si vous n’utilisez pas Key Vault, vous devrez permuter vos clés manuellement.

Deux clés d’accès sont assignées pour vous permettre de les permuter. L’existence de deux clés permet de garantir que votre application maintient l’accès au Stockage Azure tout au long du processus.

> [!WARNING]
> La regénération des clés d’accès peut impacter des applications ou des services Azure qui ont une dépendance avec la clé du compte de stockage. Tous les clients qui utilisent la clé de compte pour accéder au compte de stockage doivent être mis à jour afin d’utiliser la nouvelle clé, y compris les services multimédias, les applications cloud, de bureau et mobiles, et les applications d’interface utilisateur graphique pour le stockage Azure, comme [l’Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/).

Effectuez les étapes suivantes pour permuter vos clés de compte de stockage :

1. Mettez à jour les chaînes de connexion dans votre code d’application pour utiliser la clé secondaire.
2. Régénérez la clé d'accès principale de votre compte de stockage. Dans le panneau **Clés d’accès**, cliquez sur **Regénérer la clé 1** dans le portail Azure, puis cliquez sur **Oui** pour confirmer la regénération d’une nouvelle clé.
3. Mettez à jour les chaînes de connexion dans votre code pour désigner la nouvelle clé d’accès principale.
4. Régénérez la clé d’accès secondaire de la même manière.

> [!NOTE]
> Microsoft recommande d’utiliser uniquement l’une des clés dans toutes vos applications en même temps. Si vous utilisez parfois la clé 1 et parfois la clé 2, vous ne pouvez effectuer aucune rotation de vos clés sans qu’une application ne perde l’accès.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble des comptes de stockage Azure](storage-account-overview.md)
- [Créez un compte de stockage](storage-account-create.md)
