---
title: Créer un compte Batch dans le portail Azure | Microsoft Docs
description: Apprenez à créer un compte Azure Batch dans le portail Azure pour exécuter des charges de travail parallèles à grande échelle dans le cloud
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/18/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 89e41dc8e27cf39d9d0e6168dc7352267c321623
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55460520"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Créer un compte Batch avec le portail Azure

Apprenez à créer un compte Azure Batch dans le [portail Azure][azure_portal] et choisissez les propriétés du compte qui correspondent à votre scénario de calcul. Découvrez où se trouvent les propriétés du compte importantes, telles que les clés d’accès et les URL du compte.

Pour plus d’informations sur les comptes et les scénarios Batch, consultez la [présentation des fonctionnalités](batch-api-basics.md).

## <a name="create-a-batch-account"></a>Création d’un compte Batch

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Connectez-vous au [portail Azure][azure_portal].

1. Sélectionnez **Créer une ressource** > **Compute** > **Service Batch**.

    ![Batch dans Marketplace][marketplace_portal]

1. Entrez les paramètres **Nouveau compte Batch**. Consultez les informations suivantes :

    ![Création d’un compte Batch][account_portal]

    a. **Nom du compte** : Le nom que vous choisissez doit être unique dans la région Azure où le compte est créé (voir **Emplacement** ci-dessous). Le nom de compte peut contenir uniquement des caractères minuscules ou des chiffres et doit comporter entre 3 et 24 caractères.

    b. **Abonnement**: Abonnement où créer le compte Batch. Si vous n’avez qu’un seul abonnement, il est sélectionné par défaut.

    c. **Groupe de ressources** : Sélectionnez un groupe de ressources existant pour votre nouveau compte Batch ; vous pouvez aussi en créer un.

    d. **Emplacement** : Région Azure où créer le compte Batch. Seules les régions prises en charge par votre abonnement et votre groupe de ressources sont affichées.

    e. **Compte de stockage** (facultatif) : Compte de stockage Azure à associer à votre compte Batch. Cela est recommandé pour la plupart des comptes Batch. Pour connaître les options de compte de stockage de Batch, consultez [Aperçu des fonctionnalités d’Azure Batch](batch-api-basics.md#azure-storage-account). Dans le portail, sélectionnez un compte de stockage existant ou créez-en un nouveau.

      ![Créez un compte de stockage.][storage_account]

    f. **Mode d’allocation de pool** : Pour la plupart des scénarios, acceptez le **service Batch** par défaut.

1. Sélectionnez **Créer** pour créer le compte.



## <a name="view-batch-account-properties"></a>Afficher les propriétés du compte Batch
Une fois le compte créé, cliquez dessus pour accéder à ses propriétés et paramètres. Vous avez accès à tous les paramètres et propriétés du compte dans le menu de gauche.

![Page du compte Batch dans le portail Azure][account_blade]

* **Nom, URL et clés du compte Batch** : Quand vous développez une application avec des [API Batch](batch-apis-tools.md#azure-accounts-for-batch-development), vous avez besoin d’une URL et d’une clé de compte pour accéder aux ressources Batch. Batch prend également en charge l’authentification Azure Active Directory.

    Pour afficher les informations d’accès du compte Batch, sélectionnez **Clés**.

    ![Clés de compte Batch dans le portail Azure][account_keys]

* Pour afficher le nom et les clés du compte de stockage associé à votre compte Batch, sélectionnez **Compte de stockage**.

* Pour afficher les quotas de ressources qui s’appliquent au compte Batch, sélectionnez **Quotas**. Pour plus de détails, consultez [Quotas et limites du service Batch](batch-quota-limit.md).


## <a name="additional-configuration-for-user-subscription-mode"></a>Configuration supplémentaire pour le mode d’abonnement utilisateur

Si vous choisissez de créer un compte Batch en mode d’abonnement utilisateur, suivez les étapes supplémentaires ci-dessous avant de créer le compte.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Autoriser Azure Batch à accéder à l’abonnement (opération ponctuelle)
Quand vous créez votre premier compte Batch en mode Abonnement utilisateur, vous devez inscrire votre abonnement auprès de Batch. (Si vous l’avez déjà fait, passez à la section suivante.)

1. Connectez-vous au [portail Azure][azure_portal].

1. Sélectionnez **Tous les services** > **Abonnements**, puis cliquez sur l’abonnement que vous souhaitez utiliser pour le compte Batch.

1. Dans la page **Abonnement**, sélectionnez **Fournisseurs de ressources**et recherchez **Microsoft.Batch**. Vérifiez que le fournisseur de ressources **Microsoft.Batch** est enregistré dans l’abonnement. S’il n’est pas enregistré, cliquez sur le lien **Inscrire**.

    ![Inscrire le fournisseur Microsoft.Batch][register_provider]

1. Dans la page **Abonnement**, sélectionnez **Contrôle d’accès (IAM)** > **Attributions de rôles** > **Ajouter une attribution de rôle**.

    ![Contrôle d’accès à l’abonnement][subscription_access]

1. Dans la page **Ajouter une attribution de rôle**, sélectionnez le rôle **Contributeur** et recherchez l’API Batch. Recherchez chacune de ces chaînes, jusqu’à ce que vous trouviez l’API :
    1. **MicrosoftAzureBatch**.
    1. **Microsoft Azure Batch**. Les locataires Azure AD les plus récents peuvent utiliser ce nom.
    1. La chaîne **ddbf3205-c6bd-46ae-8127-60eb93363864** correspond à l’ID de l’API Batch. 

1. Une fois que vous avez trouvé l’API Batch, sélectionnez-la, puis cliquez sur **Enregistrer**.

    ![Ajouter des autorisations Batch][add_permission]

### <a name="create-a-key-vault"></a>Création d’un coffre de clés
En mode Abonnement utilisateur, un coffre de clés Azure appartenant au même groupe de ressources que le compte Batch est requis. Assurez-vous que le groupe de ressources se trouve dans une région prise en charge par votre abonnement et dans laquelle Batch est [disponible](https://azure.microsoft.com/regions/services/).

1. Dans le [portail Azure][azure_portal], cliquez sur **Nouveau** > **Sécurité** > **Key Vault**.

1. Dans la page **Créer un coffre de clés**, entrez un nom pour le coffre de clés et créez un groupe de ressources dans la région souhaitée pour votre compte Batch. Conservez les autres paramètres par défaut, puis cliquez sur **Créer**.

Lors de la création du compte Batch en mode Abonnement utilisateur, utilisez le groupe de ressources pour le coffre de clés, spécifiez **Abonnement utilisateur** en tant que mode d’allocation de pool, puis sélectionnez le coffre de clés.

## <a name="other-batch-account-management-options"></a>Autres options de gestion de comptes Batch
Vous pouvez créer et gérer des comptes Batch à l’aide du portail Azure, mais également avec les outils suivants :

* [Applets de commande PowerShell pour Batch](batch-powershell-cmdlets-get-started.md)
* [Interface de ligne de commande Azure](batch-cli-get-started.md)
* [Gestion de lots .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur les concepts et fonctionnalités du service Batch, consultez [Présentation des fonctionnalités du service Batch pour les développeurs](batch-api-basics.md). L’article traite des principales ressources Batch, notamment des pools, des nœuds de calcul, des travaux et des tâches et fournit une vue d’ensemble des fonctionnalités du service pour l’exécution de charges de travail de calcul à grande échelle.
* Découvrez les bases du développement d’une application Batch à l’aide de la [bibliothèque Azure Batch pour .NET](quick-run-dotnet.md) ou [Python](quick-run-python.md). Ces démarrages rapides décrivent la création d’un exemple d’application qui utilise le service Batch pour exécuter une charge de travail sur plusieurs nœuds de calcul, et inclut l’utilisation d’Azure Storage pour la mise en attente et la récupération de fichiers de la charge de travail.

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[marketplace_portal]: ./media/batch-account-create-portal/marketplace-batch.png
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch-account-portal.png
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[register_provider]: ./media/batch-account-create-portal/register_provider.png

