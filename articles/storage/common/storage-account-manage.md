---
title: Gérer les paramètres de compte de stockage dans le portail Azure
titleSuffix: Azure Storage
description: Découvrez comment gérer les paramètres d’un compte de stockage dans le portail Azure, notamment la configuration des paramètres du contrôle d’accès, la regénération des clés d’accès au compte, le changement du niveau d’accès ou le changement du type de réplication utilisé par le compte. Découvrez également comment supprimer un compte de stockage dans le portail.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: 93d3bf3d17348a0f5ad62118fcb9c9a0c6f2f656
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892377"
---
# <a name="manage-storage-account-settings-in-the-azure-portal"></a>Gérer les paramètres de compte de stockage dans le portail Azure

Divers paramètres de compte de stockage sont disponibles dans le [portail Azure](https://portal.azure.com). Cet article décrit certains de ces paramètres et explique comment les utiliser.

## <a name="access-control"></a>Contrôle d’accès

Le Stockage Azure prend en charge l’autorisation avec Azure Active Directory pour le Stockage Blob et le Stockage File d’attente via le contrôle d’accès en fonction du rôle (RBAC). Pour plus d’informations sur l’autorisation avec Azure AD, consultez [Autoriser l’accès aux objets blob et files d’attente Azure à l’aide d’Azure Active Directory](storage-auth-aad.md).

Avec les paramètres **Contrôle d’accès** disponibles dans le portail Azure, vous pouvez facilement assigner des rôles RBAC aux utilisateurs, aux groupes, aux principaux de service et aux identités managées. Pour plus d’informations sur l’assignation de rôles RBAC, consultez [Gérer les droits d’accès aux données d’objet blob et de file d’attente avec RBAC](storage-auth-aad-rbac.md).

## <a name="tags"></a>Balises

Le stockage Azure prend en charge les étiquettes Azure Resource Manager pour organiser vos ressources Azure avec une taxonomie personnalisée. Vous pouvez ajouter des étiquettes à vos comptes de stockage pour les regrouper de manière logique dans votre abonnement.

Pour les comptes de stockage, le nom d’une étiquette est limité à 128 caractères tandis que la valeur d’une étiquette est limitée à 256 caractères.

Pour plus d’informations, consultez [Organisation des ressources Azure à l’aide de balises](../../azure-resource-manager/resource-group-using-tags.md).

## <a name="access-keys"></a>Clés d’accès

Quand vous créez un compte de stockage, Azure génère deux clés d’accès 512 bits pour ce compte. Utilisez ces clés pour autoriser l’accès à votre compte de stockage avec la clé partagée. Microsoft vous recommande de permuter et regénérer les clés régulièrement, sans interruption dans vos applications.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

[!INCLUDE [storage-recommend-azure-ad-include](../../../includes/storage-recommend-azure-ad-include.md)]

### <a name="view-account-keys-and-connection-string"></a>Afficher les clés et la chaîne de connexion du compte

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

### <a name="regenerate-access-keys"></a>Régénération de clés d'accès

Microsoft vous recommande de regénérer vos clés d’accès régulièrement pour garantir une sécurité optimale de votre compte de stockage. Deux clés d’accès sont assignées pour vous permettre de les permuter. Quand vous permutez vos clés, vous vous assurez que votre application maintient l’accès au stockage Azure tout au long du processus. 

> [!WARNING]
> La regénération des clés d’accès peut impacter des applications ou des services Azure qui ont une dépendance avec la clé du compte de stockage. Tous les clients qui utilisent la clé de compte pour accéder au compte de stockage doivent être mis à jour afin d’utiliser la nouvelle clé, y compris les services multimédias, les applications cloud, de bureau et mobiles, et les applications d’interface utilisateur graphique pour le stockage Azure, comme [l’Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/).

Effectuez les étapes suivantes pour permuter vos clés de compte de stockage :

1. Mettez à jour les chaînes de connexion dans votre code d’application pour utiliser la clé secondaire.
2. Régénérez la clé d'accès principale de votre compte de stockage. Dans le panneau **Clés d’accès**, cliquez sur **Regénérer la clé 1** dans le portail Azure, puis cliquez sur **Oui** pour confirmer la regénération d’une nouvelle clé.
3. Mettez à jour les chaînes de connexion dans votre code pour désigner la nouvelle clé d’accès principale.
4. Régénérez la clé d’accès secondaire de la même manière.

## <a name="account-configuration"></a>Configuration du compte

Après avoir créé un compte de stockage, vous pouvez modifier sa configuration. Par exemple, vous pouvez changer le mode de réplication des données, ou changer le niveau d’accès du compte de chaud à froid. Dans le [portail Azure](https://portal.azure.com), accédez à votre compte de stockage, puis recherchez l’option **Configuration** sous **Paramètres** et cliquez dessus pour afficher et/ou modifier la configuration du compte.

La modification de la configuration du compte de stockage peut entraîner des coûts supplémentaires. Pour plus d’informations, consultez la page des [tarifs du stockage Azure](https://azure.microsoft.com/pricing/details/storage/).

## <a name="delete-a-storage-account"></a>Suppression d'un compte de stockage

Pour supprimer un compte de stockage que vous n’utilisez plus, accédez au compte de stockage dans le [portail Azure](https://portal.azure.com), puis cliquez sur **Supprimer**. La suppression d’un compte de stockage supprime l’intégralité du compte, y compris toutes les données qu’il contient.

> [!WARNING]
> Il n’est pas possible de restaurer un compte de stockage supprimé ou son contenu avant la suppression. Veillez à sauvegarder tout ce que vous souhaitez conserver avant de supprimer le compte. Ceci vaut également pour toutes les ressources du compte : dès que vous supprimez un objet blob, une table, une file d’attente ou un fichier, la suppression est irréversible.
> 

Si vous essayez de supprimer un compte de stockage associé à une machine virtuelle Azure, vous pouvez obtenir une erreur indiquant que le compte de stockage est toujours en cours d’utilisation. Si vous avez besoin d’aide pour résoudre cette erreur, consultez [Troubleshoot errors when you delete storage accounts](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md) (Résoudre les erreurs liées à la suppression de compte de stockage).

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble des comptes de stockage Azure](storage-account-overview.md)
- [Créez un compte de stockage](storage-quickstart-create-account.md)
