---
title: Créer un compte dans le portail Azure
description: Apprenez à créer un compte Azure Batch dans le portail Azure pour exécuter des charges de travail parallèles à grande échelle dans le cloud
ms.topic: how-to
ms.date: 02/23/2021
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 36759a0caef41af9307bf621a1b6b634ddf586cc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703662"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Créer un compte Batch avec le portail Azure

Cette rubrique montre comment créer un [compte Azure Batch](accounts.md) dans le [portail Azure](https://portal.azure.com), en choisissant les propriétés du compte qui correspondent à votre scénario de calcul. Vous allez également découvrir où se trouvent les propriétés du compte importantes, telles que les clés d’accès et les URL du compte.

Pour plus d’informations sur les comptes et les scénarios Batch, consultez [Workflow et ressources du service Batch](batch-service-workflow-features.md).

## <a name="create-a-batch-account"></a>Création d’un compte Batch

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans la page d’accueil, sélectionnez **Créer une ressource**.

1. Dans la zone de recherche, entrez **Batch Service**. Sélectionnez **Batch Service** dans les résultats, puis **Créer**.

1. Entrez les informations suivantes.

    :::image type="content" source="media/batch-account-create-portal/batch-account-portal.png" alt-text="Capture d’écran Nouveau compte Batch.":::

    a. **Abonnement**: Abonnement où créer le compte Batch. Si vous n’avez qu’un seul abonnement, il est sélectionné par défaut.

    b. **Groupe de ressources** : Sélectionnez un groupe de ressources existant pour votre nouveau compte Batch ; vous pouvez aussi en créer un.

    c. **Nom du compte** : Le nom que vous choisissez doit être unique dans la région Azure où le compte est créé (voir **Emplacement** ci-dessous). Le nom de compte peut contenir uniquement des caractères minuscules ou des chiffres et doit comporter entre 3 et 24 caractères.

    d. **Emplacement** : Région Azure où créer le compte Batch. Seules les régions prises en charge par votre abonnement et votre groupe de ressources sont affichées.

    e. **Compte de stockage** : [Compte de Stockage Azure](accounts.md#azure-storage-accounts) facultatif à associer à votre compte Batch. Vous pouvez sélectionner un compte de stockage existant ou en créer un. Un compte de stockage v2 universel est recommandé pour obtenir des performances optimales.

    :::image type="content" source="media/batch-account-create-portal/storage_account.png" alt-text="Capture d’écran des options de création d’un compte de stockage.":::

1. Si vous le souhaitez, sélectionnez **Avancé** pour spécifier les options **Type d’identité**, **Accès réseau public** ou **Mode d’allocation de pool**. Pour la plupart des scénarios, les options par défaut suffisent.

1. Sélectionnez **Vérifier + créer**, puis **Créer** pour créer le compte.

## <a name="view-batch-account-properties"></a>Afficher les propriétés du compte Batch

Une fois le compte créé, cliquez dessus pour accéder à ses propriétés et paramètres. Vous avez accès à tous les paramètres et propriétés du compte dans le menu de gauche.

> [!NOTE]
> Le nom du compte Batch correspond à son ID et ne peut pas être modifié. Si vous avez besoin de modifier le nom d’un compte Batch, vous devez supprimer le compte et en créer un nouveau avec le nom souhaité.

:::image type="content" source="media/batch-account-create-portal/batch_blade.png" alt-text="Capture d’écran de la page du compte Batch dans le portail Azure.":::

Quand vous développez une application avec des [API Batch](batch-apis-tools.md#azure-accounts-for-batch-development), vous avez besoin d’une URL et d’une clé de compte pour accéder aux ressources Batch. Batch prend également en charge l’authentification Azure Active Directory. Pour afficher les informations d’accès du compte Batch, sélectionnez **Clés**.

:::image type="content" source="media/batch-account-create-portal/batch-account-keys.png" alt-text="Capture d’écran des clés du compte Batch dans le portail Azure.":::

Pour afficher le nom et les clés du compte de stockage associé à votre compte Batch, sélectionnez **Compte de stockage**.

Pour afficher les [quotas de ressources](batch-quota-limit.md) qui s’appliquent au compte Batch, sélectionnez **Quotas**.

## <a name="additional-configuration-for-user-subscription-mode"></a>Configuration supplémentaire pour le mode d’abonnement utilisateur

Si vous choisissez de créer un compte Batch en mode d’abonnement utilisateur, suivez les étapes supplémentaires ci-dessous avant de créer le compte.

> [!IMPORTANT]
> L'utilisateur qui crée le compte Batch en mode d'abonnement utilisateur doit disposer d'une attribution de rôle Contributeur ou Propriétaire pour l'abonnement dans lequel le compte Batch sera créé.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Autoriser Azure Batch à accéder à l’abonnement (opération ponctuelle)

Quand vous créez votre premier compte Batch en mode Abonnement utilisateur, vous devez inscrire votre abonnement auprès de Batch. (Si vous l’avez déjà fait, passez à la section suivante.)

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Tous les services** > **Abonnements**, puis cliquez sur l’abonnement que vous souhaitez utiliser pour le compte Batch.

1. Dans la page **Abonnement**, sélectionnez **Fournisseurs de ressources** et recherchez **Microsoft.Batch**. Vérifiez que le fournisseur de ressources **Microsoft.Batch** est enregistré dans l’abonnement. Si ce n’est pas le cas, sélectionnez le lien **S’inscrire** en haut de l’écran.

    :::image type="content" source="media/batch-account-create-portal/register_provider.png" alt-text="Capture d’écran montrant le fournisseur de ressources Microsoft. Batch.":::

1. Revenez à la page **Abonnement**, puis sélectionnez **Contrôle d’accès (IAM)**  > **Attributions de rôles** > **Ajouter** > **Ajouter une attribution de rôle**.

    :::image type="content" source="media/batch-account-create-portal/subscription_iam.png" alt-text="Capture d’écran de la page Attributions de rôles pour un abonnement.":::

1. Dans la page **Ajouter une attribution de rôle**, sélectionnez le rôle **Contributeur** ou **Propriétaire**, puis recherchez l’API Batch. Recherchez **Microsoft Azure Batch** ou **MicrosoftAzureBatch** pour trouver l’API. (La chaîne **ddbf3205-c6bd-46ae-8127-60eb93363864** correspond à l’ID d’application de l’API Batch.)

1. Une fois que vous avez trouvé l’API Batch, sélectionnez-la, puis cliquez sur **Enregistrer**.

### <a name="create-a-key-vault"></a>Créer un coffre de clés

En mode abonnement utilisateur, un [coffre de clés Azure](../key-vault/general/overview.md) est nécessaire. Le coffre de clés doit se trouver dans la même région et le même abonnement que le compte Batch à créer.

1. Depuis l’écran d’accueil du [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource**.
1. Dans la zone de recherche, entrez **Key Vault**. Sélectionnez **Key Vault** dans les résultats, puis sélectionnez **Créer**.
1. Dans la page **Créer un coffre de clés**, entrez un nom pour le coffre de clés et créez un groupe de ressources dans la même région souhaitée pour votre compte Batch. Conservez les autres paramètres par défaut, puis cliquez sur **Créer**.

Lors de la création du compte Batch en mode abonnement utilisateur, spécifiez **Abonnement utilisateur** comme mode d’allocation de pool, sélectionnez le coffre de clés que vous avez créé, puis cochez la case pour accorder à Azure Batch l’accès au coffre de clés.

Si vous préférez accorder manuellement l’accès au coffre de clés, accédez à la section **Stratégies d’accès** du coffre de clés, puis sélectionnez **Ajouter une stratégie d'accès**. Cliquez sur le lien en regard de **Sélectionner le principal**, puis recherchez **Microsoft Azure Batch** (Application ID **ddbf3205-c6bd-46ae-8127-60eb93363864**). Sélectionnez ce principal, puis configurez les **Autorisations du secret** à l’aide du menu déroulant. Azure Batch doit avoir au minimum les autorisations **Get**, **List**, **Set** et **Delete**. Pour les [coffres de clés avec suppression réversible](../key-vault/general/soft-delete-overview.md), Azure Batch doit également disposer de l’autorisation **Récupérer**.

:::image type="content" source="media/batch-account-create-portal/secret-permissions.png" alt-text="Capture d’écran des sélections Autorisations du secret pour Azure Batch":::

Sélectionnez **Ajouter**, puis vérifiez que les cases à cocher **Machines virtuelles Azure pour le déploiement** et **Azure Resource Manager pour le déploiement de modèles** sont activées pour la ressource **Key Vault** liée. Sélectionnez **Enregistrer** pour valider vos modifications.

:::image type="content" source="media/batch-account-create-portal/key-vault-access-policy.png" alt-text="Capture d’écran de l’écran Stratégie d’accès.":::

### <a name="configure-subscription-quotas"></a>Configurer les quotas d’abonnement

Pour les comptes Batch d’abonnement utilisateur, les [quotas de base](batch-quota-limit.md) doivent être définis manuellement. Les quotas de cœurs Batch standard ne s’appliquent pas aux comptes en mode abonnement utilisateur, et les [quotas de votre abonnement](../azure-resource-manager/management/azure-subscription-service-limits.md) pour les cœurs de calcul régionaux, les cœurs de calcul par série et d’autres ressources sont utilisés et appliqués.

1. Dans le [Portail Azure](https://portal.azure.com), sélectionnez votre compte Batch en mode d’abonnement utilisateur pour afficher ses paramètres et ses propriétés.
1. Dans le menu de gauche, sélectionnez **Quotas** pour afficher et configurer les quotas de base associés à votre compte Batch.

## <a name="other-batch-account-management-options"></a>Autres options de gestion de comptes Batch

Vous pouvez créer et gérer des comptes Batch à l’aide du portail Azure, mais également avec les outils suivants :

- [Applets de commande PowerShell pour Batch](batch-powershell-cmdlets-get-started.md)
- [Azure CLI](batch-cli-get-started.md)
- [Gestion de lots .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [workflow et les ressources principales du service Batch](batch-service-workflow-features.md) telles que les pools, les nœuds, les travaux et les tâches.
- Découvrez les bases du développement d’une application Batch à l’aide de la [bibliothèque Azure Batch pour .NET](quick-run-dotnet.md) ou [Python](quick-run-python.md). Ces démarrages rapides décrivent la création d’un exemple d’application qui utilise le service Batch pour exécuter une charge de travail sur plusieurs nœuds de calcul, et l’utilisation d’Azure Storage pour la mise en attente et la récupération de fichiers de la charge de travail.