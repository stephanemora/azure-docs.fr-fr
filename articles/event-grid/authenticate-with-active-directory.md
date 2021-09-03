---
title: Authentifier les clients de publication Event Grid à l’aide d’Azure Active Directory (préversion)
description: Cet article explique comment authentifier les clients de publication Azure Event Grid à l’aide d’Azure Active Directory.
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: c34ce75d02d4e3044819e5e310bdb9e74c1b0004
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562925"
---
# <a name="authentication-and-authorization-with-azure-active-directory-preview"></a>Authentification et autorisation avec Azure Active Directory (préversion)
Cet article explique comment authentifier les clients de publication Azure Event Grid à l’aide d’Azure Active Directory (Azure AD).

## <a name="overview"></a>Vue d'ensemble
La plateforme [Microsoft Identity](../active-directory/develop/v2-overview.md) fournit une gestion intégrée de l’authentification et du contrôle d’accès pour les ressources et les applications qui utilisent Azure Active Directory (Azure AD) comme fournisseur d’identité. Utilisez la plateforme Microsoft Identity pour fournir une prise en charge de l’authentification et des autorisations dans vos applications. Elle est basée sur des normes ouvertes telles que OAuth 2.0 et OpenID Connect et offre des outils et des bibliothèques open source qui prennent en charge de nombreux scénarios d’authentification. Elle fournit des fonctionnalités avancées, telles que l’[accès conditionnel](../active-directory/conditional-access/overview.md), qui vous permet de définir des stratégies qui nécessitent l’authentification multifacteur ou autorisent l’accès à partir d’emplacements spécifiques, par exemple.

L’un des avantages pour votre posture de sécurité lorsque vous utilisez Azure AD, c’est que vous n’avez pas besoin de stocker des informations d’identification, telles que des clés d’authentification, dans du code ou des référentiels. Au lieu de cela, vous pouvez compter sur l’acquisition de jetons d’accès OAuth 2.0 à partir de la plateforme Microsoft Identity présentée par votre application lors de l’authentification auprès d’une ressource protégée. Vous pouvez inscrire votre application de publication d’événements avec Azure AD et obtenir un principal de service associé à votre application que vous gérez et utilisez. Au lieu de cela, vous pouvez utiliser des [identités managées](../active-directory/managed-identities-azure-resources/overview.md), à savoir le système affecté ou l’utilisateur affecté, pour un modèle de gestion des identités encore plus simple, car certains aspects du cycle de vie des identités sont managés pour vous. 

Le [contrôle d’accès en fonction du rôle](../active-directory/develop/custom-rbac-for-developers.md) (RBAC) vous permet de configurer l’autorisation de manière à ce que certains principaux de sécurité (identités pour les utilisateurs, les groupes ou les applications) disposent d’autorisations spécifiques pour exécuter des opérations sur les ressources Azure. De cette façon, le principal de sécurité utilisé par une application cliente qui envoie des événements à Event Grid doit être associé au rôle RBAC **EventGrid Data Sender**. 

### <a name="security-principals"></a>Principaux de sécurité
Deux grandes catégories de principaux de sécurité s’appliquent lorsque vous discutez de l’authentification d’un client de publication Event Grid : 

- **Les identités managées**. Une identité managée peut être attribuée par le système, que vous activez sur une ressource Azure et qui est associé uniquement à cette ressource, ou à l’utilisateur affecté, que vous créez et nommez explicitement. Les identités managées affectées par l’utilisateur peuvent être associées à plusieurs ressources.
- **Le principal de sécurité de l’application**. Il s’agit d’un type de principal de sécurité qui représente une application, qui accède aux ressources protégées par Azure AD. 

Indépendamment du principal de sécurité utilisé, d’une identité managée ou d’un principal de sécurité d’application, votre client utilise cette identité pour s’authentifier avant Azure AD et obtenir un [jeton d’accès OAuth 2.0](../active-directory/develop/access-tokens.md) qui est envoyé avec les demandes lors de l’envoi d’événements à Event Grid. Ce jeton est signé par chiffrement, et lorsqu’Event Grid le reçoit, le jeton est validé. Par exemple, l’audience (destinataire prévu du jeton) est confirmée en tant qu’Event Grid (`https://eventgrid.azure.net`), entre autres choses. Le jeton contient des informations sur l’identité du client. Event Grid prend cette identité et vérifie que le client a le rôle **EventGrid Data Sender** qui lui est assigné. Plus précisément, Event Grid valide que l’identité a l’autorisation ``Microsoft.EventGrid/events/send/action`` dans un rôle RBAC associé à l’identité avant d’autoriser la fin de la requête de publication d’événement. 
 
Si vous utilisez le Kit de développement logiciel (SDK) Event Grid, vous n’avez pas à vous soucier des détails sur la façon d’implémenter l’acquisition de jetons d’accès et sur la façon de l’inclure à chaque requête à Event Grid, car le [Kit de développement logiciel (SDK) de plan de données Event Grid](#publish-events-using-event-grids-client-sdks) le fait pour vous. 

### <a name="high-level-steps"></a>procédure générale
Procédez comme suit pour préparer votre client à l’utilisation de l’authentification Azure AD lors de l’envoi d’événements à une rubrique, un domaine ou un espace de noms de partenaire.

1. Créez un principal de sécurité ou utilisez-en un déjà existant pour vous authentifier. Vous pouvez utiliser une [identité managée](#authenticate-using-a-managed-identity) ou un [principal de service d’application](#authenticate-using-a-security-principal-of-a-client-application).
2. [Accordez l’autorisation à un principal de sécurité pour publier des événements](#assign-permission-to-a-security-principal-to-publish-events) en affectant le rôle **EventGrid Data Sender** au principal de sécurité.
3. Utilisez le Kit de développement logiciel (SDK) Event Grid pour publier des événements sur une instance Event Grid.

## <a name="authenticate-using-a-managed-identity"></a>S’authentifier à l’aide d’une identité managée

Les identités managées sont des identités associées aux ressources Azure. Les identités managées fournissent une identité que les applications utilisent lorsqu’elles utilisent des ressources Azure qui prennent en charge l’authentification Azure AD. Des applications peuvent utiliser l’identité managée de la ressource d’hébergement comme un ordinateur virtuel ou un service Azure App pour obtenir des jetons Azure AD qui sont présentés avec la requête lors de la publication d’événements sur Event Grid. Lorsque l’application se connecte, Event Grid lie le contexte de l’entité managée au client. Une fois associé à une identité managée, votre client de publication Event Grid peut effectuer toutes les opérations autorisées. L’autorisation est accordée en associant une entité managée à un rôle RBAC Event Grid.

L’identité managée fournit aux services Azure une identité managée automatiquement dans Azure AD. Contrairement aux autres méthodes d’authentification, vous n’avez pas besoin de stocker et de protéger les clés d’accès ou les signatures d’accès partagé (SAP) dans le code ou la configuration de votre application, que ce soit pour l’identité elle-même ou pour les ressources auxquelles vous devez accéder.

Pour authentifier votre client de publication d’événements à l’aide d’identités managées, commencez par choisir le service d’hébergement Azure pour votre application cliente, puis activez les identités managées par le système ou attribuées à l’utilisateur sur cette instance de service Azure. Par exemple, vous pouvez activer les identités managées sur une [machine virtuelle](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md), ou une instance [Azure App Service ou Azure Functions](../app-service/overview-managed-identity.md?tabs=dotnet). 

Lorsque vous disposez d’une identité managée configurée dans un service d’hébergement, [attribuez l’autorisation de publier des événements à cette identité](#assign-permission-to-a-security-principal-to-publish-events).

## <a name="authenticate-using-a-security-principal-of-a-client-application"></a>S’authentifier en utilisant un principal de sécurité d’une application cliente

Outre les identités managées, une autre option d’identité consiste à créer un principal de sécurité pour votre application cliente. Pour cela, vous devez inscrire votre application auprès de Azure AD. L’inscription de votre application est un geste par le biais duquel vous déléguez le contrôle de la gestion des identités et des accès à Azure AD. Suivez les étapes décrites dans la section [Inscrire une application](../active-directory/develop/quickstart-register-app.md#register-an-application) et dans la section [Ajouter un secret client](../active-directory/develop/quickstart-register-app.md#add-a-client-secret). Veuillez passer en revue les [prérequis](../active-directory/develop/quickstart-register-app.md#prerequisites) avant de commencer.

Une fois que vous avez un principal de sécurité d’application et que vous avez suivi les étapes ci-dessus, [attribuez l’autorisation de publier des événements à cette identité](#assign-permission-to-a-security-principal-to-publish-events).

> [!NOTE]
> Si vous inscrivez une application dans le portail, un [objet application](../active-directory/develop/app-objects-and-service-principals.md#application-object) et un [principal de service](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) sont automatiquement créés dans votre tenant (locataire) de base. Vous pouvez également utiliser Microsoft Graph pour inscrire votre application. Si vous inscrivez/créez une application à l’aide des API Microsoft Graph, la création de l’objet principal de service devient une étape distincte. 

## <a name="assign-permission-to-a-security-principal-to-publish-events"></a>Attribuer une autorisation à un principal de sécurité pour publier des événements

L’identité utilisée pour publier des événements dans Event Grid doit disposer de l’autorisation ``Microsoft.EventGrid/events/send/action`` qui lui permet d’envoyer des événements à Event Grid. Cette autorisation est incluse dans le rôle RBAC intégré [EventGrid Data Sender](../role-based-access-control/built-in-roles.md#eventgrid-data-sender). Ce rôle peut être attribué à un [principal de la sécurité](../role-based-access-control/overview.md#security-principal), pour une [étendue](../role-based-access-control/overview.md#scope) donnée, qui peut être un groupe d’administration, un abonnement Azure, un groupe de ressources ou un espace de noms de la grille d’événements, un domaine ou un espace de noms de partenaire spécifique. Suivez les étapes décrites dans [Attribuer des rôles Azure à l’aide du portail Azure](../role-based-access-control/role-assignments-portal.md?tabs=current) pour affecter un principal de sécurité au rôle d’**EventGrid Data Sender** et, de cette façon, accorder à une application utilisant ce principal de sécurité l’accès pour envoyer des événements. Vous pouvez également définir un [rôle personnalisé](../role-based-access-control/custom-roles.md) qui comprend l’autorisation ``Microsoft.EventGrid/events/send/action`` et affectez ce rôle personnalisé à votre principal de sécurité.

Une fois les privilèges RBAC pris en charge, vous pouvez désormais [développer votre application cliente pour envoyer des événements](#publish-events-using-event-grids-client-sdks) à Event Grid.

> [!NOTE]
> Event Grid prend en charge d’autres rôles RBAC, pour d’autres objectifs que l’envoi d’événements. Si vous souhaitez en savoir plus, consultez [Rôles intégrés à Event Grid](security-authorization.md#built-in-roles).


## <a name="publish-events-using-event-grids-client-sdks"></a>Publier des événements à l’aide du Kit de développement logiciel (SDK) client Event Grid

Utilisez le [Kit de développement logiciel (SDK) Event Grid](https://devblogs.microsoft.com/azure-sdk/event-grid-ga/) pour publier des événements sur une instance Event Grid. Le Kit de développement logiciel (SDK) Event Grid prend en charge toutes les méthodes d’authentification, y compris l’authentification Azure AD. 

### <a name="prerequisites"></a>Configuration requise

Vous trouverez ci-dessous les conditions préalables à l’authentification auprès d’Event Grid.

- Installez le Kit de développement logiciel (SDK) dans votre application.
   - [Java](/java/api/overview/azure/messaging-eventgrid-readme#include-the-package)
   - [.NET](/dotnet/api/overview/azure/messaging.eventgrid-readme-pre#install-the-package)
   - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/eventgrid/eventgrid.md#install-the-azureeventgrid-package)
   - [Python](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/eventgrid/azure-eventgrid#install-the-package)
- Installez la bibliothèque de client Azure Identity. Le Kit de développement logiciel (SDK) Event Grid dépend de la bibliothèque cliente Azure Identity pour l’authentification. 
   - [Bibliothèque cliente Azure Identity pour Java](/java/api/overview/azure/identity-readme)
   - [Bibliothèque cliente Azure Identity pour .NET](/dotnet/api/overview/azure/identity-readme)
   - [Bibliothèque cliente Azure Identity pour JavaScript](/javascript/api/overview/azure/identity-readme)
   - [Bibliothèque cliente Azure Identity pour Python](/python/api/overview/azure/identity-readme)
- Une rubrique, un domaine ou un espace de noms de partenaire créé dans lequel votre application enverra des événements.

### <a name="publish-events-using-azure-ad-authentication"></a>Publier des événements en utilisant Azure AD Authentication

Pour envoyer des événements à une rubrique, un domaine ou un espace de noms de partenaire, vous pouvez générer le client comme suit. La version d’API ayant fourni la première prise en charge de l’authentification Azure AD est ``2021-06-01-preview``. Utilisez cette version de l’API ou une version plus récente dans votre application.

```java 
        DefaultAzureCredential credential = new DefaultAzureCredentialBuilder().build();
        EventGridPublisherClient cloudEventClient = new EventGridPublisherClientBuilder()
                .endpoint("<your-event-grid-topic-domain-or-partner-namespace-endpoint>?api-version=2021-06-01-preview")
                .credential(credential)
                .buildCloudEventPublisherClient();
```
Si vous utilisez un principal de sécurité associé à une application de publication client, vous devez configurer les variables d’environnement comme indiqué dans l’article [Lisez-moi du kit de développement logiciel (SDK) Java](/java/api/overview/azure/identity-readme#environment-variables). Le `DefaultCredentialBuilder` lit ces variables d’environnement pour utiliser l’identité appropriée. Si vous souhaitez en savoir plus, consultez cette [présentation de l’API Java](/java/api/overview/azure/identity-readme#defaultazurecredential).


Pour plus d’informations, consultez les articles suivants :

- [Bibliothèque de client Azure Event Grid pour Java](/java/api/overview/azure/messaging-eventgrid-readme)
- [Bibliothèque de client Azure Event Grid pour .NET](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/eventgrid/Azure.Messaging.EventGrid#authenticate-using-azure-active-directory)
- [Bibliothèque de client Azure Event Grid pour JavaScript](/javascript/api/overview/azure/eventgrid-readme)
- [Bibliothèque de client Azure Event Grid pour Python](/python/api/overview/azure/eventgrid-readme)

## <a name="disable-key-and-shared-access-signature-authentication"></a>Désactiver l’authentification par clé d’accès et signature d’accès partagé

L’authentification Azure AD fournit une prise en charge d’authentification supérieure à celle proposée par l’authentification par jeton de signature d’accès partagé ou par clé d’accès. Avec l’authentification Azure AD, l’identité est validée par rapport au fournisseur d’identité Azure AD. En tant que développeur, vous n’aurez pas à gérer les clés dans votre code si vous utilisez l’authentification Azure AD. Vous bénéficierez également de toutes les fonctionnalités de sécurité intégrées à la plateforme Microsoft Identity, telles que l’[accès conditionnel](../active-directory/conditional-access/overview.md), qui peuvent vous aider à renforcer la posture de sécurité de votre application. 

Lorsque vous décidez d’utiliser l’authentification Azure AD, vous pouvez désactiver l’authentification basée sur les clés d’accès ou les jetons SAS. 

> [!NOTE]
> L’authentification par clés d’accès ou jeton SAS constitue une forme d’**authentification locale**. Vous entendrez parfois faire référence à « authentification locale » lorsque vous discutez de cette catégorie de mécanismes d’authentification qui ne reposent pas sur Azure AD. Le paramètre d’API utilisé pour désactiver l’authentification locale est appelé, de manière appropriée, ``disableLocalAuth``.

La commande CLI suivante montre comment créer une rubrique personnalisée lorsque l’authentification locale est désactivée. La fonctionnalité Désactiver l’authentification locale est actuellement disponible en version préliminaire et vous devez utiliser la version de l’API ``2021-06-01-preview``.

```cli
az resource create --subscription <subscriptionId> --resource-group <resourceGroup> --resource-type Microsoft.EventGrid/topics --api-version 2021-06-01-preview --name <topicName> --location <location> --properties "{ \"disableLocalAuth\": true}"
```

Pour référence, Voici les valeurs de type de ressource que vous pouvez utiliser en fonction de la rubrique que vous créez ou mettez à jour.

| Type de rubrique        | Type de ressource                        |
| ------------------| :------------------------------------|
| Domaines           | Microsoft.EventGrid/domains          |
| Espace de noms de partenaire | Microsoft.EventGrid/partnerNamespaces|
| Rubrique personnalisée      | Microsoft.EventGrid/topics           |

Si vous utilisez PowerShell, utilisez les applets de commande suivantes pour créer une rubrique personnalisée pour laquelle l’authentification locale est désactivée. 

```PowerShell

Set-AzContext -SubscriptionId <SubscriptionId>

New-AzResource -ResourceGroupName <ResourceGroupName> -ResourceType Microsoft.EventGrid/topics -ApiVersion 2021-06-01-preview -ResourceName <TopicName> -Location <Location> -Properties @{disableLocalAuth=$true}
```

> [!NOTE]
> - Pour en savoir plus sur l’utilisation de la clé d’accès ou de l’authentification par signature d’accès partagé, consultez [authentifier des clients de publication avec des clés ou des jetons SAS](security-authenticate-publishing-clients.md)
> - Cet article traite de l’authentification lors de la publication d’événements sur Event Grid (entrée d’événements). L’authentification Event Grid lors de la remise d’événements (sortie d’événements) est présentée dans l’article [Authentifier la remise des événements aux gestionnaires d’événements](security-authentication.md). 

## <a name="resources"></a>Ressources
- Kits SDK de plan de données
    - Kit de développement logiciel (SDK) Java : [github](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventgrid/azure-messaging-eventgrid) | [échantillons](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventgrid/azure-messaging-eventgrid/src/samples/java/com/azure/messaging/eventgrid) | [guide de migration relatif aux précédentes versions du Kit de développement logiciel (SDK)](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventgrid/azure-messaging-eventgrid/migration-guide.md)
    - Kit de développement logiciel (SDK) .NET SDK : [github](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventgrid/Azure.Messaging.EventGrid) | [échantillons](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventgrid/Azure.Messaging.EventGrid/samples) | [guide de migration relatif aux précédentes versions du Kit de développement logiciel (SDK)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventgrid/Azure.Messaging.EventGrid/MigrationGuide.md)
    - Kit de développement personnel Python SDK  [github](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventgrid/azure-eventgrid) | [échantillon](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventgrid/azure-eventgrid/samples) | [guide de migration relatif aux précédentes versions du Kit de développement logiciel (SDK)](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventgrid/azure-eventgrid/migration_guide.md)
    - Kit de développement personnel JavaScript SDK  [github](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventgrid/eventgrid/) | [échantillon](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventgrid/eventgrid/samples) | [guide de migration relatif aux précédentes versions du Kit de développement logiciel (SDK)](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventgrid/eventgrid/migration.md)
- [Blog du kit de développement logiciel Event Grid](https://devblogs.microsoft.com/azure-sdk/event-grid-ga/)
- Bibliothèque cliente Azure Identity
   - [Java](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/identity/azure-identity/README.md)
   - [.NET](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/identity/Azure.Identity/README.md)  
   - [Python](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/identity/azure-identity/README.md)
   - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/identity/identity/README.md)
- En savoir plus sur les [identités managées](../active-directory/managed-identities-azure-resources/overview.md)
- Pour approfondir vos connaissances, vous pouvez consulter :[Guide pratique pour utiliser des identités managées avec App Service et Azure Functions](../app-service/overview-managed-identity.md?tabs=dotnet).
- En savoir plus sur les [applications et les principaux de service](../active-directory/develop/app-objects-and-service-principals.md)
- En savoir plus sur l’[inscription d’une application à l’aide de la plateforme Microsoft Identity](../active-directory/develop/quickstart-register-app.md).
- En savoir plus sur le fonctionnement de l’[autorisation](../role-based-access-control/overview.md) (contrôle {1}d'{2} accès RBAC).
- En savoir plus sur les rôles RBAC intégrés à Event Grid, notamment leur rôle [EventGrid Data Sender](../role-based-access-control/built-in-roles.md#eventgrid-data-sender). [Liste des rôles de Event Grid](security-authorization.md#built-in-roles).
- En savoir plus sur l’[attribution de rôles RBAC](../role-based-access-control/role-assignments-portal.md?tabs=current) aux identités.
- En savoir plus sur la définition des [rôles RBAC personnalisés](../role-based-access-control/custom-roles.md).
- En savoir plus sur l’[application et les objects du principal du service dans Azure AD](../active-directory/develop/app-objects-and-service-principals.md)
- En savoir plus sur les [jetons d’accès de la plateforme Microsoft Identity](../active-directory/develop/access-tokens.md).
- En savoir plus sur le [flux de code d'authentification OAuth 2.0 et la plateforme Microsoft Identity](../active-directory/develop/v2-oauth2-auth-code-flow.md)
