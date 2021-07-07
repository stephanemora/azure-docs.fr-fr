---
title: Utiliser une clé de Key Vault pour chiffrer les données dans un compte Media Services
description: Si vous souhaitez que Media Services chiffre les données à l'aide d'une clé de votre coffre de clés Key Vault, le compte Media Services doit avoir *accès* à ce coffre de clés. Suivez les étapes ci-dessous afin de créer une identité managée pour le compte Media Services, et d'accorder à cette identité l'accès à votre coffre de clés à l'aide de l'interface de ligne de commande Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: tutorial
ms.date: 05/14/2021
ms.author: inhenkel
ms.openlocfilehash: 20b0ec0025fcac72005eb8b2343ede0ef9e5d9b4
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110464323"
---
# <a name="tutorial-use-a-key-vault-key-to-encrypt-data-into-a-media-services-account"></a>Tutoriel : Utiliser une clé de Key Vault pour chiffrer les données dans un compte Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Si vous souhaitez que Media Services chiffre les données à l'aide d'une clé de votre coffre de clés Key Vault, le compte Media Services doit avoir *accès* à ce coffre de clés. Suivez les étapes ci-dessous afin de créer une identité managée pour le compte Media Services, et d'accorder à cette identité l'accès à votre coffre de clés à l'aide de l'interface de ligne de commande Media Services.

:::image type="content" source="media/diagrams/managed-identities-scenario-keyvault-media-services-account.svg" alt-text="Le compte Media Services utilise Key Vault avec une identité managée":::



Ce tutoriel utilise l'API Media Services du 01/05/2020.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Pour utiliser l'une des commandes de cet article, vous devez d'abord être connecté à l'abonnement que vous souhaitez utiliser.

 [!INCLUDE [Sign in to Azure with the CLI](./includes/task-sign-in-azure-cli.md)]

### <a name="set-subscription"></a>Définir l’abonnement

Utilisez cette commande pour définir l'abonnement que vous souhaitez utiliser.

[!INCLUDE [Sign in to Azure with the CLI](./includes/task-set-azure-subscription-cli.md)]

## <a name="resource-names"></a>Noms de ressource

Avant de commencer, choisissez les noms des ressources que vous allez créer.  Ils doivent être facilement identifiables en tant qu'ensemble, surtout si vous ne prévoyez pas de les utiliser une fois les tests terminés. Les règles de nommage varient selon les types de ressources. Il est donc préférable de s'en tenir aux minuscules. Par exemple, « mediatest1rg » pour le nom de votre groupe de ressources et « mediatest1stor » pour le nom de votre compte de stockage. Utilisez les mêmes noms à chacune des étapes de cet article.

Vous verrez ces noms référencés dans les commandes ci-dessous.  Les noms de ressources dont vous aurez besoin sont les suivants :

- your-resource-group-name
- your-storage-account-name
- your-media-services-account-name
- your-keyvault-name
- your-key-name
- your-region

> [!NOTE]
> Les tirets ci-dessus sont uniquement utilisés pour séparer les mots. Compte tenu du manque de cohérence en termes de dénomination des ressources dans les services Azure, n'utilisez pas de tirets lorsque vous nommez vos ressources.
> En outre, vous ne créez pas le nom de la région.  Celui-ci est déterminé par Azure.

### <a name="list-azure-regions"></a>Liste des régions Azure

Si besoin, utilisez cette commande pour accéder à la liste des régions :

[!INCLUDE [List Azure regions with the CLI](./includes/task-list-azure-regions-cli.md)]

## <a name="sequence"></a>Séquence

Chacune des étapes ci-dessous suit un ordre particulier car une ou plusieurs valeurs des réponses JSON sont utilisées à l'étape suivante de la séquence.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Les ressources que vous allez créer doivent appartenir à un groupe de ressources. Commencez par créer le groupe de ressources. Vous utiliserez `your-resource-group-name` pour l'étape de création du compte Media Services et les étapes suivantes.

[!INCLUDE [Create a resource group with the CLI](./includes/task-create-resource-group-cli.md)]

## <a name="create-a-storage-account"></a>Créer un compte de stockage

Un compte de stockage doit être associé au compte Media Services que vous allez créer. Commencez par créer le compte de stockage à associer au compte Media Services. Vous utiliserez `your-storage-account-name` pour les étapes suivantes.

[!INCLUDE [Create a Storage account with the CLI](./includes/task-create-storage-account-cli.md)]

## <a name="create-a-media-services-account-with-a-service-principal-managed-identity"></a>Créer un compte Media Services avec un principal de service (identité managée)

Créez maintenant le compte Media Services avec un principal de service, également appelé identité managée.

> [!IMPORTANT]
> Important : n'oubliez pas d'utiliser l'indicateur --mi dans la commande.  Sinon, vous ne trouverez pas la propriété `principalId` nécessaire ultérieurement.

[!INCLUDE [Create a Media Services account with the CLI](./includes/task-create-media-services-account-managed-identity-cli.md)]

## <a name="create-a-key-vault"></a>Créer un coffre de clés

Créez le coffre de clés.  Le coffre de clés est utilisé pour chiffrer les données multimédias. Vous utiliserez `your-keyvault-name` pour créer votre clé et pour les étapes ultérieures.

[!INCLUDE [Create a Media Services account with the CLI](./includes/task-create-key-vault-cli.md)]

## <a name="grant-the-media-services-system-assigned-managed-identity-access-to-the-key-vault"></a>Accorder à l'identité managée attribuée au système Media Services l'accès au coffre de clés

Accordez à l'identité managée Media Services l'accès au coffre de clés. Deux commandes sont disponibles :

### <a name="get-show-the-managed-identity-of-the-media-services-account"></a>Permet d'obtenir (d'afficher) l'identité managée du compte Media Services

La première commande ci-dessous affiche l'identité managée du compte Media Services qui correspond à la propriété `principalId` répertoriée dans la sortie JSON renvoyée par la commande.

[!INCLUDE [Show the Managed Identity of a Media Services account with the CLI](./includes/task-show-account-managed-identity-cli.md)]

### <a name="set-the-key-vault-policy"></a>Définir la stratégie Key Vault

La deuxième commande accorde à l'ID du principal l'accès au coffre de clés. Définissez `object-id` sur la valeur de `principalId` que vous avez obtenue à l'étape précédente.

[!INCLUDE [Set the Key Vault policy with the CLI](./includes/task-set-key-vault-policy-cli.md)]

### <a name="set-media-services-to-use-the-key-from-key-vault"></a>Définir Media Services pour utiliser la clé de Key Vault

Définissez Media Services pour utiliser la clé que vous avez créée. La valeur de la propriété `key-identifier` provient de la sortie générée lors de la création de la clé. Cette commande peut échouer en raison du temps nécessaire à la propagation des modifications du contrôle d'accès. Dans ce cas, réessayez au bout de quelques minutes.

[!INCLUDE [Set Media Services to use the key from Key Vault](./includes/task-set-encryption-cli.md)]

## <a name="validation"></a>Validation

Pour vérifier que le compte est chiffré à l'aide d'une clé gérée par le client, consultez les propriétés de chiffrement du compte :

[!INCLUDE [Set Media Services to use the key from Key Vault](./includes/task-show-account-encryption-cli.md)]

La propriété `type` doit afficher `CustomerKey` et la propriété `currentKeyIdentifier` doit être définie sur le chemin d'une clé du coffre de clés du client.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n'avez pas l'intention d'utiliser les ressources que vous avez créées, supprimez le groupe de ressources.

[!INCLUDE [Create a Media Services account with the CLI](./includes/clean-up-resources-cli.md)]
