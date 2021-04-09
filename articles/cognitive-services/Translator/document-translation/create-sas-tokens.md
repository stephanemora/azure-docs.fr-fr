---
title: Créer un jeton de signature d’accès partagé (SAS) pour les conteneurs et les blobs avec l’Explorateur Stockage Microsoft
description: Comment créer un jeton d’accès partagé (SAS) pour les conteneurs et les blobs avec l’Explorateur Stockage Microsoft et le portail Azure
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 03/05/2021
ms.openlocfilehash: e40fc569ad1c8ec5894f06915422bea37cfc40ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102489631"
---
# <a name="create-sas-tokens-for-document-translation-processing"></a>Créer des jetons SAS pour le traitement des traductions de documentation

Dans cet article, vous allez apprendre à créer des jetons de signature d’accès partagé (SAS) avec l’Explorateur Stockage Azure ou le portail Azure. Un jeton SAS fournit un accès délégué sécurisé aux ressources dans votre compte de stockage Azure.

## <a name="create-your-sas-tokens-with-azure-storage-explorer"></a>Créer vos jetons SAS avec l’Explorateur Stockage Azure

### <a name="prerequisites"></a>Prérequis

* Vous devez disposer d’une application [**Explorateur Stockage Azure**](../../../vs-azure-tools-storage-manage-with-storage-explorer.md) installée dans votre environnement de développement Windows, macOS ou Linux. L’Explorateur Stockage Azure est un outil gratuit qui vous permet de gérer facilement vos ressources de stockage cloud Azure.
* Une fois l’application Explorateur Stockage Azure installée, [connectez-la au compte de stockage](../../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#connect-to-a-storage-account-or-service) que vous utilisez pour la traduction de documentation.

### <a name="create-your-tokens"></a>Créer vos jetons

### <a name="sas-tokens-for-containers"></a>[Jetons SAS pour les conteneurs](#tab/Containers)

1. Ouvrez l’application Explorateur Stockage Azure sur votre ordinateur local, puis accédez à vos **comptes de stockage** connectés.
1. Développez le nœud Comptes de stockage, puis sélectionnez **Conteneurs d’objets blob**.
1. Développez le nœud Conteneurs d’objets blob, puis cliquez avec le bouton droit sur un nœud de **conteneur** de stockage pour afficher le menu d’options.
1. Dans le menu d’options, sélectionnez **Obtenir une signature d’accès partagé...**
1. Dans la fenêtre **Signature d’accès partagé**, effectuez les sélections suivantes :
    * Sélectionnez votre **stratégie d’accès** (la valeur par défaut est Aucune).
    * Spécifiez la date et l’heure de **début** et d’**expiration** de la clé signée. Une durée de vie courte est recommandée car, une fois générée, une signature d’accès partagé ne peut pas être révoquée.
    * Sélectionnez le **fuseau horaire** pour la date et l’heure de début et d’expiration (la valeur par défaut est Local).
    * Définissez les **autorisations** de votre conteneur en cochant et/ou décochant la case appropriée.
    * Passez en revue les informations, puis sélectionnez **Créer**.

1. Une nouvelle fenêtre s’affiche avec le nom de votre **Conteneur**, son **URI** et sa **Chaîne de requête**.  
1. **Copiez et collez les valeurs Conteneur, URI et Chaîne de requête en lieu sûr. Elles ne s’affichent qu’une seule fois et ne peuvent pas être récupérées une fois la fenêtre fermée.**
1. Pour construire une URL de la signature d’accès partagé, ajoutez le jeton SAS (URI) à l’URL d’un service de stockage.

### <a name="sas-tokens-for-blobs"></a>[Jetons SAS pour les blobs](#tab/blobs)

1. Ouvrez l’application Explorateur Stockage Azure sur votre ordinateur local, puis accédez à vos **comptes de stockage** connectés.
1. Développez votre nœud de stockage, puis sélectionnez **Conteneurs d’objets blob**.
1. Développez le nœud Conteneurs d’objets blob, puis sélectionnez un nœud de **conteneur** pour afficher le contenu dans la fenêtre principale.
1. Sélectionnez le blob dans lequel vous souhaitez déléguer l’accès SAS, puis cliquez avec le bouton droit pour afficher le menu d’options.
1. Dans le menu d’options, sélectionnez **Obtenir une signature d’accès partagé...**
1. Dans la fenêtre **Signature d’accès partagé**, effectuez les sélections suivantes :
    * Sélectionnez votre **stratégie d’accès** (la valeur par défaut est Aucune).
    * Spécifiez la date et l’heure de **début** et d’**expiration** de la clé signée. Une durée de vie courte est recommandée car, une fois générée, une signature d’accès partagé ne peut pas être révoquée.
    * Sélectionnez le **fuseau horaire** pour la date et l’heure de début et d’expiration (la valeur par défaut est Local).
    * Définissez les **autorisations** de votre conteneur en cochant et/ou décochant la case appropriée.
    * Passez en revue les informations, puis sélectionnez **Créer**.
1. Une nouvelle fenêtre s’affiche avec le nom de votre **blob**, son **URI** et sa **Chaîne de requête**.  
1. **Copiez et collez les valeurs Objet blob, URI et Chaîne de requête en lieu sûr. Elles ne s’affichent qu’une seule fois et ne peuvent pas être récupérées une fois la fenêtre fermée.**
1. Pour construire une URL de la signature d’accès partagé, ajoutez le jeton SAS (URI) à l’URL d’un service de stockage.

---

## <a name="create-sas-tokens-for-blobs-in-the-azure-portal"></a>Créer des jetons SAS pour les blobs dans le portail Azure

> [!NOTE]
> La création de jetons SAS pour les conteneurs directement dans le portail Azure n’est actuellement pas prise en charge. Toutefois, vous pouvez créer un jeton SAS avec l’[**Explorateur Stockage Azure**](#create-your-sas-tokens-with-azure-storage-explorer) ou effectuer la tâche [programmatiquement](../../../storage/blobs/sas-service-create.md).

<!-- markdownlint-disable MD024 -->
### <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez des éléments suivants :

* Un [**compte Azure**](https://azure.microsoft.com/free/cognitive-services/) actif.  Si vous n’en avez pas, vous pouvez [**créer un compte gratuit**](https://azure.microsoft.com/free/).
* Une ressource du service [**Translator**](https://ms.portal.azure.com/#create/Microsoft) (et **pas** une ressource multiservice Cognitive Services.  *Consultez* [Créer une nouvelle ressource Azure](../../cognitive-services-apis-create-account.md#create-a-new-azure-cognitive-services-resource).  
* Un [**compte de stockage Blob Azure**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Vous allez créer des conteneurs pour stocker et organiser vos données de blob dans votre compte de stockage.

### <a name="create-your-tokens"></a>Créer vos jetons

Accédez au [portail Azure](https://ms.portal.azure.com/#home), puis naviguez comme suit :  

 **Votre compte de stockage** → **conteneurs** → **votre conteneur** → **votre blob**

1. Sélectionnez **Générer une signature d’accès partagé** dans le menu situé en haut de la page.

1. Sélectionnez **Méthode de signature** → **Clé de délégation d’utilisateur**.

1. Définissez les **autorisations** en cochant et/ou décochant la case appropriée.

1. Spécifiez les heures de **début** et d’**expiration** de la clé signée.

1. Le champ **Adresses IP autorisées** est facultatif. Il spécifie une adresse IP ou une plage d’adresses IP à partir desquelles des requêtes doivent être acceptées. Si l’adresse IP de la requête e ne correspond pas à l’adresse IP ou à la plage d’adresses spécifiée sur le jeton SAS, elle ne sera pas autorisée.

1. Le champ **Protocoles autorisés** est facultatif. Il spécifie le protocole autorisé pour une requête effectuée avec la signature d’accès partagé. La valeur par défaut est HTTPS.

1. Passez en revue les informations, puis sélectionnez **Générer une URL et un jeton SAS**.

1. La chaîne de requête du **Jeton SAS d’objet blob** et l’**URL SAP d’objet blob** s’affichent en bas dans la fenêtre.  

1. **Copiez et collez les valeurs Jeton SAS d’objet blob et URL en lieu sûr. Elles ne s’affichent qu’une seule fois et ne peuvent pas être récupérées une fois la fenêtre fermée.**

1. Pour construire une URL de la signature d’accès partagé, ajoutez le jeton SAS (URI) à l’URL d’un service de stockage.

## <a name="learn-more"></a>En savoir plus

* [Créer des jetons SAS programmatiquement pour des blobs ou des conteneurs](../../../storage/blobs/sas-service-create.md)
* [Autorisations pour un répertoire, un conteneur ou un blob](/rest/api/storageservices/create-service-sas#permissions-for-a-directory-container-or-blob)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Bien démarrer avec Document Translation](get-started-with-document-translation.md)
>
>