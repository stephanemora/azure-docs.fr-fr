---
title: Identités managées de ressources Azure avec la préversion d’Azure Event Hubs | Microsoft Docs
description: Utiliser les identités managées de ressources Azure avec Azure Event Hubs
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: shvija
ms.openlocfilehash: 815a6ff528e024ed1685b09b66f8fabce4d360c1
ms.sourcegitcommit: 4edf9354a00bb63082c3b844b979165b64f46286
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2018
ms.locfileid: "48784551"
---
# <a name="managed-identities-for-azure-resources-with-event-hubs"></a>Identités managées de ressources Azure avec Azure Event Hubs

La fonctionnalité [Identités managées de ressources Azure](../active-directory/managed-identities-azure-resources/overview.md) vous permet de créer une identité sécurisée associée au déploiement sous lequel s’exécute le code de votre application. Vous pouvez ensuite associer cette identité à des rôles de contrôle d’accès qui accordent des autorisations personnalisées pour l’accès aux ressources Azure nécessaires à votre application. 

Avec les identités managées, la plateforme Azure gère cette identité d’exécution. Vous n’avez pas besoin de stocker et de protéger des clés d’accès dans le code ou la configuration de votre application, que ce soit pour l’identité elle-même ou pour les ressources auxquelles vous devez accéder. Une application cliente Event Hubs exécutée dans une application Azure App Service ou dans une machine virtuelle prenant en charge les identités managées des ressources Azure n’a pas besoin de gérer des clés et des règles SAS ou d’autres jetons d’accès. L’application cliente a uniquement besoin de l’adresse de point de terminaison de l’espace de noms Event Hubs. Lorsque l’application se connecte, Event Hubs associe le contexte de l’identité managée au client dans une opération illustrée plus loin dans cet article.

Une fois associé à une identité managée, un client Event Hubs peut effectuer toutes les opérations autorisées. L’autorisation est accordée en associant une identité managée à des rôles Event Hubs. 

## <a name="event-hubs-roles-and-permissions"></a>Rôles et autorisations Event Hubs

Vous ne pouvez ajouter une identité managée qu’aux rôles « Propriétaire » ou « Contributeur » d’un espace de noms Event Hubs, qui accorde à l’identité un contrôle total sur toutes les entités dans l’espace de noms. Toutefois, les opérations de gestion qui modifient la topologie de l’espace de noms ne sont initialement prises en charge que par Azure Resource Manager. Mais pas par l’interface de gestion REST Event Hubs native. Cette prise en charge signifie également que vous ne pouvez pas utiliser l’objet [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) du client .NET Framework dans une identité managée. 
 
## <a name="use-event-hubs-with-managed-identities-for-azure-resources"></a>Utiliser Event Hubs avec les identités managées des ressources Azure

La section suivante décrit les étapes ci-dessous :

1. Créez et déployez un exemple d’application qui s’exécute sous une identité managée.
2. Accordez à cette identité l’accès à un espace de noms Event Hubs.
3. Comment l’application interagit avec Event Hubs à l’aide de cette identité.

Cette présentation décrit une application web hébergée dans [Azure App Service](https://azure.microsoft.com/services/app-service/). Les étapes requises pour une application hébergée sur une machine virtuelle sont similaires.

### <a name="create-an-app-service-web-application"></a>Créer une application web App Service

La première étape consiste à créer une application ASP.NET App Service. Si vous n’êtes pas familiarisé avec la procédure à suivre dans Azure, suivez [ce guide pratique](../app-service/app-service-web-get-started-dotnet-framework.md). Toutefois, au lieu de créer une application MVC, comme indiqué dans le didacticiel, créez une application Web Forms.

### <a name="set-up-the-managed-identity"></a>Configurer l’identité managée

Une fois l’application web créée, accédez-y dans le portail Azure (procédure également indiquée dans le guide pratique), puis accédez à la page **Managed Service Identity** et activez la fonctionnalité : 

![](./media/event-hubs-managed-service-identity/msi1.png)
 
Une fois la fonctionnalité activée, une identité de service est créée dans votre annuaire Azure Active Directory et configurée dans l’hôte App Service.

### <a name="create-a-new-event-hubs-namespace"></a>Créer un espace de noms Event Hubs

Ensuite, [créez un espace de noms Event Hubs](event-hubs-create.md) dans l’une des régions Azure qui prend en charge la préversion des identités managées des ressources Azure : **USA Est**, **USA Est 2** ou **Europe Ouest**. 

Accédez à la page **Contrôle d’accès (IAM)** de l’espace de nom sur le portail, puis cliquez sur **Ajouter** pour ajouter l’identité managée au rôle **Propriétaire**. Pour ce faire, recherchez le nom de l’application web dans le champ **Sélectionner** du panneau **Ajouter des autorisations**, puis cliquez sur l’entrée. Cliquez ensuite sur **Enregistrer**.

![](./media/event-hubs-managed-service-identity/msi2.png)
 
L’identité managée de l’application web a désormais accès à l’espace de noms Event Hubs et au hub d’événements que vous avez créé. 

### <a name="run-the-app"></a>Exécution de l'application

À présent, modifiez la page par défaut de l’application ASP.NET que vous avez créée. Vous pouvez également utiliser le code d’application web à partir de [ce référentiel GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp). 

Une fois que vous démarrez l’application, pointez votre navigateur sur EventHubsMSIDemo.aspx. Vous pouvez également la définir comme page de démarrage. Le code se trouve dans le fichier EventHubsMSIDemo.aspx.cs. Le résultat est une application web minimale avec quelques champs d’entrée et les boutons **send** (envoyer) et **receive** (recevoir) qui permettent de se connecter à Event Hubs pour envoyer ou recevoir des événements. 

Notez la façon dont l’objet [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) est initialisé. Au lieu d’utiliser le fournisseur de jetons SAS (Jeton d’accès partagé), le code crée un fournisseur de jetons pour l’identité managée avec l’appel `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)`. Ainsi, il n’est pas nécessaire de conserver et d’utiliser des secrets. Le flux du contexte d’identité managée vers Event Hubs et la négociation des autorisations sont gérés automatiquement par le fournisseur de jetons, ce qui est plus simple que d’utiliser SAS.

Après avoir apporté ces modifications, publiez et exécutez l’application. Pour obtenir les données de publication correctes, téléchargez un profil de publication, puis importez-le dans Visual Studio :

![](./media/event-hubs-managed-service-identity/msi3.png)
 
Pour envoyer ou recevoir des messages, saisissez le nom de l’espace de noms et le nom de l’entité que vous avez créée, puis cliquez sur **send** (envoyer) ou **receive** (recevoir). 
 
L’identité managée ne fonctionne que dans l’environnement Azure et seulement dans le déploiement App Service où vous l’avez configurée. Pour l’instant, les identités managées ne fonctionnent pas avec les emplacements de déploiement App Service.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les concentrateurs d’événements, accédez aux liens suivants :

* Prise en main avec un [didacticiel des concentrateurs d’événements](event-hubs-dotnet-standard-getstarted-send.md)
* [FAQ sur les hubs d’événements](event-hubs-faq.md)
* [Tarification des concentrateurs d'événements](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Exemples d’application complets qui utilisent des concentrateurs d’événements](https://github.com/Azure/azure-event-hubs/tree/master/samples)
