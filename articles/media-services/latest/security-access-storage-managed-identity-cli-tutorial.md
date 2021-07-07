---
title: Accéder à un stockage avec une identité managée Media Services
description: Si vous souhaitez accéder à un compte de stockage configuré pour bloquer les demandes en provenance d’adresses IP inconnues, le compte Media Services doit avoir accès au compte de stockage. Suivez les étapes ci-dessous pour créer une identité managée pour le compte Media Services et accorder à celle-ci l’accès au stockage en utilisant l’interface de ligne de commande Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: tutorial
ms.date: 05/17/2021
ms.author: inhenkel
ms.openlocfilehash: 77eb737a48213701e424bbc8b1a49ead0c95e88c
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110464287"
---
# <a name="tutorial-access-storage-with-a-media-services-managed-identity"></a>Tutoriel : Accéder à un stockage avec une identité managée Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Si vous souhaitez accéder à un compte de stockage configuré pour bloquer les demandes en provenance d’adresses IP inconnues, le compte Media Services doit avoir accès au compte de stockage. Suivez les étapes ci-dessous pour créer une identité managée pour le compte Media Services et accorder à celle-ci l’accès au stockage en utilisant l’interface de ligne de commande Media Services.

:::image type="content" source="media/diagrams/managed-identities-scenario-storage-permissions-media-services-account.svg" alt-text="Le compte Media Services utilise une identité managée pour accéder au stockage":::

Ce tutoriel utilise l’API Media Services 2020-05-01.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Pour utiliser les commandes évoquées dans cet article, vous devez être connecté à l’abonnement que vous souhaitez utiliser.

 [!INCLUDE [Sign in to Azure with the CLI](./includes/task-sign-in-azure-cli.md)]

### <a name="set-subscription"></a>Définir l’abonnement

Utilisez cette commande pour définir l’abonnement que vous souhaitez utiliser.

[!INCLUDE [Sign in to Azure with the CLI](./includes/task-set-azure-subscription-cli.md)]

## <a name="resource-names"></a>Noms de ressource

Avant de commencer, choisissez les noms des ressources que vous allez créer.  Ils doivent être aisément identifiables en tant qu’ensemble, surtout si vous ne prévoyez pas de les utiliser une fois les tests terminés. Les règles de nommage variant selon les types de ressources, l’idéal est de s’en tenir aux minuscules. Par exemple, « mediatest1rg » pour le nom de votre groupe de ressources et « mediatest1stor » pour le nom de votre compte de stockage. Utilisez les mêmes noms à chacune des étapes décrites dans cet article.

Vous verrez ces noms référencés dans les commandes ci-dessous.  Les noms des ressources dont vous avez besoin sont les suivants :

- your-resource-group-name
- your-storage-account-name
- your-media-services-account-name
- your-region

> [!NOTE]
> Les tirets ci-dessus sont utilisés uniquement pour séparer les mots indicatifs. Compte tenu de l’incohérence de nommage des ressources dans les services Azure, n’utilisez pas de tirets lorsque vous nommez vos ressources.
> Par ailleurs, vous ne créez pas le nom de région.  Celui-ci est déterminé par Azure.

### <a name="list-azure-regions"></a>Liste des régions Azure

Si ignorez le nom de région réel, utilisez cette commande pour accéder à la liste des régions :

[!INCLUDE [List Azure regions with the CLI](./includes/task-list-azure-regions-cli.md)]

## <a name="sequence"></a>Séquence

L’exécution de chacune des étapes ci-dessous suit un ordre particulier car une ou plusieurs valeurs des réponses JSON obtenues à chaque étape sont utilisées à l’étape suivante dans la séquence.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Les ressources que vous allez créer doivent appartenir à un groupe de ressources. Commencez par créer le groupe de ressources. Vous allez utiliser `your-resource-group-name` pour l’étape de création du compte Media Services et les étapes suivantes.

[!INCLUDE [Create a resource group with the CLI](./includes/task-create-resource-group-cli.md)]

## <a name="create-a-storage-account"></a>Créer un compte de stockage

Un compte de stockage doit être associé au compte Media Services que vous allez créer. Commencez par créer le compte de stockage à associer au compte Media Services. Vous allez utiliser `your-storage-account-name` pour les étapes suivantes.

[!INCLUDE [Create a Storage account with the CLI](./includes/task-create-storage-account-cli.md)]

## <a name="create-a-media-services-account-with-a-service-principal-managed-identity"></a>Créer un compte Media Services avec un principal de service (identité managée)

Créez maintenant le compte Media Services avec un principal de service, également appelé identité managée.

> [!IMPORTANT]
> N’oubliez surtout pas d’utiliser l’indicateur --mi dans la commande.  Sinon, vous ne trouverez pas la propriété `principalId` nécessaire ultérieurement.

[!INCLUDE [Create a Media Services account with the CLI](./includes/task-create-media-services-account-managed-identity-cli.md)]

## <a name="grant-the-media-services-managed-identity-access-to-the-storage-account"></a>Accorder à l’identité managée Media Services l’accès au compte de stockage

Accordez à l’identité managée Media Services l’accès au compte de stockage. Il y a trois commandes :

### <a name="get-show-the-managed-identity-of-the-media-services-account"></a>Obtenir (afficher) l’identité managée du compte Media Services

La première commande ci-dessous affiche l’identité managée du compte Media Services correspondant à la propriété `principalId` répertoriée dans le fichier JSON que la commande a retourné.

[!INCLUDE [Show the Managed Identity of a Media Services account with the CLI](./includes/task-show-account-managed-identity-cli.md)]

### <a name="create-the-storage-blob-contributor-role-assignment"></a>Créer l’attribution de rôle Contributeur de blob de stockage

[!INCLUDE [Create the Storage Blob Contributor role assignment](./includes/task-create-storage-blob-contributor-role-cli.md)]

### <a name="create-the-reader-role-assignment"></a>Créer l’attribution de rôle Lecteur

[!INCLUDE [Create the Reader role assignment](./includes/task-create-reader-role-cli.md)]

## <a name="use-the-managed-identity-to-access-the-storage-account"></a>Utiliser l’identité managée pour accéder au compte de stockage

[!INCLUDE [Use the Managed Identity to access the Storage account](./includes/task-set-storage-managed-identity-cli.md)]

## <a name="validation"></a>Validation

Pour vérifier que le compte est chiffré à l’aide d’une clé gérée par le client, consultez les propriétés de chiffrement du compte :

[!INCLUDE [Set Media Services to use the key from Key Vault](./includes/task-show-account-managed-identity-cli.md)]

La propriété `storageAuthentication` doit afficher « ManagedIdentity ».

Pour une validation supplémentaire, vous pouvez consulter les journaux du service Stockage Azure afin de voir quelle méthode d’authentification est utilisée pour chaque demande.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez pas l’intention d’utiliser les ressources que vous avez créées, supprimez le groupe de ressources.

[!INCLUDE [Create a Media Services account with the CLI](./includes/clean-up-resources-cli.md)]
