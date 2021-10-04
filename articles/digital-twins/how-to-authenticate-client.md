---
title: Écrire le code d’authentification de l’application
titleSuffix: Azure Digital Twins
description: Voir comment écrire un code d’authentification dans une application cliente
author: baanders
ms.author: baanders
ms.date: 9/1/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a1e397acfe8118c339b45d6c786ae2c0b2cc82e8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124827011"
---
# <a name="write-client-app-authentication-code"></a>Écrire le code d’authentification de l’application cliente

Après avoir [configuré une instance et une authentification Azure Digital Twins](how-to-set-up-instance-portal.md), vous pouvez créer une application cliente que vous allez utiliser pour interagir avec l’instance. Après avoir configuré un projet client de démarrage, vous devez **écrire du code dans cette application cliente pour l’authentifier** auprès de l’instance Azure Digital Twins.

Azure Digital Twins authentifie à l’aide de [jetons de sécurité Azure AD Security basés sur OAUTH 2.0](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims). Pour authentifier votre kit de développement logiciel (SDK), vous devez obtenir un jeton du porteur avec les autorisations appropriées sur Azure Digital Twins et le transmettre avec vos appels d’API. 

Cet article explique comment obtenir des informations d’identification à l’aide de la bibliothèque cliente `Azure.Identity`. Bien que cet article présente des exemples de code en C#, tels que ce que vous écrivez pour le [kit de développement logiciel (SDK) .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true), vous pouvez utiliser une version de `Azure.Identity` quel que soit le kit de développement logiciel (SDK) que vous utilisez (pour plus d’informations sur les SDK disponibles pour Azure Digital Twins, consultez [API et SDK Azure Digital Twins](concepts-apis-sdks.md)).

## <a name="prerequisites"></a>Prérequis

Tout d’abord, terminez les étapes de configuration dans [Configurer une instance et l’authentification](how-to-set-up-instance-portal.md). Cette configuration permet de s’assurer que vous disposez d’une instance Azure Digital Twins et que votre utilisateur dispose d’autorisations d’accès. Une fois cette configuration terminée, vous êtes prêt à écrire le code de l’application cliente.

Pour continuer, vous aurez besoin d’un projet d’application cliente dans lequel écrire votre code. Si vous n’avez pas encore configuré de projet d’application cliente, créez un projet de base à utiliser avec ce didacticiel dans le langage de votre choix.

## <a name="authenticate-using-azureidentity-library"></a>S’authentifier à l’aide de la bibliothèque Azure.Identity

`Azure.Identity` est une bibliothèque cliente qui fournit plusieurs méthodes d’obtention d’informations d’identification que vous pouvez utiliser pour obtenir un jeton du porteur et vous authentifier avec votre kit de développement logiciel (SDK). Bien que cet article donne des exemples en C# , vous pouvez afficher `Azure.Identity` pour plusieurs langages, notamment...

* [.NET (C#)](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)
* [Java](/java/api/overview/azure/identity-readme?view=azure-java-stable&preserve-view=true)
* [JavaScript](/javascript/api/overview/azure/identity-readme?view=azure-node-latest&preserve-view=true)
* [Python](/python/api/overview/azure/identity-readme?view=azure-python&preserve-view=true)

Trois méthodes d’obtention d’informations d’identification courantes dans `Azure.Identity` sont les suivantes :

* [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) fournit un flux d’authentification `TokenCredential` par défaut pour les applications qui seront déployées sur Azure, et est **le choix recommandé pour le développement local**. Il peut également être activé pour essayer les deux autres méthodes recommandées dans cet article. Il encapsule `ManagedIdentityCredential` et peut accéder à `InteractiveBrowserCredential` à l’aide d’une variable de configuration.
* [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) fonctionne parfaitement lorsqu’il vous faut des [identités managées (MSI)](../active-directory/managed-identities-azure-resources/overview.md) et peut être utilisé avec Azure Functions et pour déployer sur des services Azure.
* [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) est destiné aux applications interactives et peut être utilisé pour créer un client de kit de développement logiciel (SDK) authentifié.

Le reste de cet article explique comment utiliser ces méthodes avec le [Kit de développement logiciel (SDK) .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true).

### <a name="add-azureidentity-to-your-net-project"></a>Ajouter Azure.Identity à votre projet .NET

Pour configurer votre projet .NET pour l’authentification avec `Azure.Identity`, procédez comme suit :

1. Incluez le package du kit de développement logiciel (SDK) `Azure.DigitalTwins.Core` et le package `Azure.Identity` dans votre projet. Selon les outils que vous choisissez, vous pouvez inclure les packages à l’aide du gestionnaire de package Visual Studio ou de l’outil en ligne de commande `dotnet`. 

1. Ajoutez les instructions using suivantes à votre code de projet :

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="Azure_Digital_Twins_dependencies":::

Ajoutez ensuite du code pour obtenir des informations d’identification à l’aide de l’une des méthodes dans `Azure.Identity`. Les sections suivantes fournissent plus de détails sur l’utilisation de chacune d’elles.

### <a name="defaultazurecredential-method"></a>Méthode DefaultAzureCredential

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) fournit un flux d’authentification `TokenCredential` par défaut pour les applications qui seront déployées sur Azure, et est **le choix recommandé pour le développement local**.

Pour utiliser les informations d’identification Azure par défaut, vous avez besoin de l’URL de l’instance Azure Digital Twins ([instructions pour les trouver](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

Voici un exemple de code pour ajouter un `DefaultAzureCredential` à votre projet :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_full":::

#### <a name="set-up-local-azure-credentials"></a>Configurer les informations d’identification Azure locales

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](../../includes/digital-twins-local-credentials-inner.md)]

### <a name="managedidentitycredential-method"></a>Méthode ManagedIdentityCredential

La méthode [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) fonctionne parfaitement lorsqu’il vous faut des [identités managées (MSI)](../active-directory/managed-identities-azure-resources/overview.md), en cas d’[authentification avec Azure Functions](#authenticate-azure-functions).

Cela signifie que vous pouvez utiliser `ManagedIdentityCredential` dans le même projet que `DefaultAzureCredential` ou `InteractiveBrowserCredential`, pour authentifier une autre partie du projet.

Pour utiliser les informations d’identification Azure par défaut, vous avez besoin de l’URL de l’instance Azure Digital Twins ([instructions pour les trouver](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)). Il se peut que vous ayez également besoin de l’[inscription d’application](./how-to-create-app-registration-portal.md) et de l’[ID d’application](./how-to-create-app-registration-portal.md#collect-client-id-and-tenant-id).

Dans une fonction Azure, vous pouvez utiliser les informations d’identification de l’identité managée comme suit :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="ManagedIdentityCredential":::

### <a name="interactivebrowsercredential-method"></a>Méthode InteractiveBrowserCredential

La méthode [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) est destinée aux applications interactives et affiche un navigateur web pour l’authentification. Vous pouvez utiliser cette méthode à la place de `DefaultAzureCredential` lorsque vous avez besoin d’une authentification interactive.

Pour utiliser les informations d’identification du navigateur interactif, vous avez besoin d’une **inscription d’application** disposant d’autorisations sur les API Azure Digital Twins. Pour plus d’informations sur la configuration de cette inscription d’application, consultez [Créer une inscription d’application](./how-to-create-app-registration-portal.md). Une fois l’inscription d’application configurée, vous avez besoin de...
* [l’ID d’application (client) de l’inscription d’application](./how-to-create-app-registration-portal.md#collect-client-id-and-tenant-id)
* [l’ID de répertoire (locataire) de l’inscription d’application](./how-to-create-app-registration-portal.md#collect-client-id-and-tenant-id)
* [l’URL de l’instance Azure Digital Twins](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)

Voici un exemple de code permettant de créer un client du kit de développement logiciel (SDK) authentifié à l’aide de `InteractiveBrowserCredential`.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="InteractiveBrowserCredential":::

>[!NOTE]
> Bien que vous puissiez placer l’ID client, l’ID de locataire et l’URL de l’instance directement dans le code comme indiqué ci-dessus, il peut être judicieux de faire en sorte que votre code récupère ces valeurs à partir d’un fichier de configuration ou d’une variable d’environnement.

## <a name="authenticate-azure-functions"></a>Authentifier Azure Functions

Cette section contient quelques-unes des options de configuration importantes dans le contexte de l’authentification avec Azure Functions. Tout d’abord, vous allez découvrir les concepts sur les variables au niveau de la classe et le code d’authentification permettant à la fonction d’accéder à Azure Digital Twins. Ensuite, vous découvrirez certaines étapes de configuration finales à effectuer pour votre fonction une fois que son code est publié sur Azure. 

### <a name="write-application-code"></a>Écrire le code de l’application

Lors de l’écriture de la fonction Azure, envisagez d’ajouter ces variables et du code à votre fonction :

* **Code permettant de lire l’URL du service Azure Digital Twins en tant que variable d’environnement ou de paramètre de configuration.** Il est recommandé de lire l’URL du service à partir d’un(e) [paramètre d’application/variable d’environnement](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal) plutôt que de le (la) coder en dur dans la fonction. Dans une fonction Azure, ce code permettant de lire la variable d’environnement peut se présenter comme suit : 

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

    Plus tard, après avoir publié la fonction, vous allez créer et définir la valeur de la variable d’environnement pour que ce code soit lu. Pour obtenir des instructions sur la procédure à suivre, passez directement à la section [Configurer les paramètres de l’application](#configure-application-settings).

* **Une variable statique destinée à contenir une instance HttpClient.** HttpClient étant relativement coûteux à créer, vous souhaiterez probablement le créer une fois avec le code d’authentification pour éviter d’avoir à le créer pour chaque appel de fonction.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* **Informations d’identification de l’identité managée.** Créez les informations d’identification d’identité managée que votre fonction utilisera pour accéder à Azure Digital Twins.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

    Par la suite, après la publication de la fonction, vous vous assurez que l’identité de la fonction a l’autorisation d’accéder aux API Azure Digital Twins. Pour obtenir des instructions sur la procédure à suivre, passez directement à la rubrique [Attribuer un rôle d’accès](#assign-an-access-role).    

* **Une variable locale _DigitalTwinsClient_.** Ajoutez la variable à l'intérieur de votre fonction pour contenir votre instance de client Azure Digital Twins. Vous *ne devez pas* rendre cette variable statique dans votre classe.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* **Un contrôle de valeur Null pour _adtInstanceUrl_.** Ajoutez le contrôle de valeur Null, puis wrappez la logique de votre fonction dans un bloc try/catch pour intercepter toutes les exceptions.

Une fois ces variables ajoutées à une fonction, votre code de fonction peut ressembler à l’exemple suivant.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

Lorsque vous avez terminé avec votre code de fonction, y compris l’ajout de l’authentification et la logique de la fonction, [publiez l’application dans Azure](../azure-functions/functions-develop-vs.md#publish-to-azure)

### <a name="configure-published-app"></a>Configurer l’application publiée

Enfin, effectuez les étapes de configuration suivantes pour une fonction Azure publiée afin de vous assurer qu’elle peut accéder à votre instance Azure Digital Twins.

[!INCLUDE [digital-twins-configure-function-app-cli.md](../../includes/digital-twins-configure-function-app-cli.md)]

## <a name="authenticate-across-tenants"></a>S’authentifier auprès des locataires

Azure Digital Twins est un service qui ne prend en charge qu’un seul [locataire Azure Active Directory (Azure AD)](../active-directory/develop/quickstart-create-new-tenant.md), à savoir le locataire principal de l’abonnement où se trouve l’instance Azure Digital Twins.

[!INCLUDE [digital-twins-tenant-limitation](../../includes/digital-twins-tenant-limitation.md)]

Si vous avez besoin d’accéder à votre instance Azure Digital Twins à l’aide d’un principal de service ou d’un compte d’utilisateur qui appartient à un autre locataire de l’instance, vous pouvez faire en sorte que chaque identité fédérée issue d’un autre locataire demande un **jeton** au locataire « d’accueil » de l’instance Azure Digital Twins. 

[!INCLUDE [digital-twins-tenant-solution-1](../../includes/digital-twins-tenant-solution-1.md)]

Vous pouvez également spécifier un locataire d’accueil dans les options d’informations d’identification de votre code. 

[!INCLUDE [digital-twins-tenant-solution-2](../../includes/digital-twins-tenant-solution-2.md)]

## <a name="other-credential-methods"></a>Autres méthodes d’informations d’identification

Si les scénarios d’authentification indiqués ci-dessus ne couvrent pas les besoins de votre application, vous pouvez explorer les autres types d’authentification proposés dans la [Plateforme d’identité Microsoft](../active-directory/develop/v2-overview.md#getting-started). La documentation de cette plateforme couvre des scénarios d’authentification supplémentaires, organisés par type d’application.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le fonctionnement de la sécurité dans Azure Digital Twins :
* [Sécurité pour les solutions Azure Digital Twins](concepts-security.md)

Sinon, l’authentification étant configurée, passez à la création et à la gestion de modèles dans votre instance :
* [Gérer des modèles DTDL](how-to-manage-model.md)