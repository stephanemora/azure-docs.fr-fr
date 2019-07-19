---
title: Contrôle d’accès en fonction du rôle Azure Service Bus (version préliminaire) | Microsoft Docs
description: Contrôle d’accès en fonction du rôle Azure Service Bus
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: aschhab
ms.openlocfilehash: 6f5390162ce56a0e77ef41740d7e88f3546c5530
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444732"
---
# <a name="active-directory-role-based-access-control-preview"></a>Contrôle d’accès en fonction du rôle Azure Active Directory (version préliminaire)

Microsoft Azure offre la gestion du contrôle d’accès intégré pour les ressources et les applications basées sur Azure Active Directory (Azure AD). Azure AD vous permet de gérer les comptes et les applications utilisateur spécifiquement pour vos applications Azure ou de fédérer votre infrastructure Active Directory existante avec Azure AD pour une authentification unique à l’échelle de l’entreprise qui couvre également les ressources Azure et les applications hébergées par Azure. Vous pouvez ensuite affecter ces identités d’utilisateur et d’application Azure AD à des rôles globaux et spécifiques à un service pour accorder l’accès aux ressources Azure.

Pour Azure Service Bus, la gestion des espaces de noms et de toutes les ressources associées via le portail Azure et l’API de gestion des ressources Azure est déjà protégée à l’aide du modèle de *contrôle d’accès en fonction du rôle* (RBAC). Le contrôle d’accès en fonction du rôle des opérations d’exécution est maintenant une fonctionnalité en version préliminaire publique.

Une application qui utilise le contrôle d’accès en fonction du rôle Azure AD n’a pas besoin de gérer les règles et les clés SAS ou les autres jetons d’accès spécifiques de Service Bus. L’application cliente interagit avec Azure AD pour établir un contexte d’authentification et acquiert un jeton d’accès pour Service Bus. Avec les comptes d’utilisateur de domaine qui requièrent une connexion interactive, l’application ne gère jamais directement les informations d’identification.

## <a name="service-bus-roles-and-permissions"></a>Rôles et autorisations Service Bus

Azure fournit les rôles RBAC intégrés ci-dessous pour autoriser l’accès à un espace de noms Service Bus :

* [Propriétaire de données Azure Service Bus (préversion)](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner-preview) : Permet l’accès aux données sur l’espace de noms Service Bus et ses entités (files d’attente, rubriques, abonnements et filtres)

>[!IMPORTANT]
> Auparavant, nous prenions en charge l’ajout d’une identité managée pour le rôle **« Propriétaire »** ou **« Contributeur »** .
>
> Toutefois, des privilèges d’accès aux données pour le rôle **« Propriétaire »** et **« Contributeur »** ne sont plus respectés. Si vous utilisiez le rôle **« Propriétaire »** ou **« Contributeur »** , ces derniers devront être adaptés de manière à utiliser le rôle **« Propriétaire de données Azure Service Bus (préversion) »** .

## <a name="use-service-bus-with-an-azure-ad-domain-user-account"></a>Utiliser Service Bus avec un compte d’utilisateur de domaine Azure AD

La section suivante décrit les étapes requises pour créer et exécuter un exemple d’application qui invite un utilisateur Azure AD interactif à se connecter, comment accorder à ce compte d’utilisateur l’accès à Service Bus et comment utiliser cette identité pour accéder aux Event Hubs.

Cette présentation décrit une application console simple, [dont le code se trouve sur GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).

### <a name="create-an-active-directory-user-account"></a>Création d’un compte d’utilisateur Active Directory

La première étape est facultative. Chaque abonnement Azure est automatiquement associé à un locataire Azure Active Directory et si vous avez accès à un abonnement Azure, votre compte d’utilisateur est déjà inscrit. Cela signifie que vous pouvez utiliser seulement votre compte.

Si vous souhaitez toujours créer un compte spécifique pour ce scénario, [procédez comme suit](../automation/automation-create-aduser-account.md). Vous devez être autorisé à créer des comptes dans le locataire Azure Active Directory, ce qui peut ne pas être le cas pour les scénarios d’entreprise de plus grande taille.

### <a name="create-a-service-bus-namespace"></a>Création d’un espace de noms Service Bus

Ensuite, [créez un espace de noms Service Bus Messaging](service-bus-create-namespace-portal.md).

Une fois l’espace de noms créé, accédez à la page **Contrôle d’accès (IAM)** correspondante sur le portail, puis cliquez sur **Ajouter une attribution de rôle** pour ajouter le compte d’utilisateur Azure AD au rôle de propriétaire. Si vous utilisez votre propre compte d’utilisateur et que vous avez créé l’espace de noms, vous êtes déjà dans le rôle de propriétaire. Pour ajouter un autre compte au rôle, recherchez le nom de l’application web dans le champ **Sélectionner** du panneau **Ajouter des autorisations**, puis cliquez sur l’entrée. Cliquez ensuite sur **Enregistrer**.

Le compte d’utilisateur a désormais accès à l’espace de noms Service Bus et à la file d’attente que vous avez précédemment créé.

### <a name="register-the-application"></a>Enregistrement de l’application

Avant de pouvoir exécuter l’exemple d’application, enregistrez-la dans Azure AD et approuvez l’invite de consentement qui permet à l’application d’accéder à Azure Service Bus en son nom.

Étant donné que l’exemple d’application est une application console, vous devez enregistrer une application native et ajouter des autorisations d’API pour **Microsoft.ServiceBus** à l’ensemble « autorisations requises ». Les applications natives requièrent également un **URI de redirection** dans Azure AD qui sert d’identificateur ; il n’est pas nécessaire que l’URI soit une destination réseau. Utilisez `https://servicebus.microsoft.com` pour cet exemple, étant donné que l’exemple de code utilise déjà cet URI.

Les étapes d’enregistrement détaillées sont expliquées dans [ce didacticiel](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Suivez les étapes pour enregistrer une application **native**, puis les instructions de mise à jour pour ajouter l’API **Microsoft.ServiceBus** aux autorisations requises. Au fil de ces étapes, notez les **TenantId** et **ApplicationId**, dont vous aurez besoin pour exécuter l’application.

### <a name="run-the-app"></a>Exécution de l'application

Avant de pouvoir exécuter l’exemple, modifiez le fichier App.config et, selon votre scénario, définissez les valeurs suivantes :

- `tenantId`: défini avec la valeur **TenantId**.
- `clientId`: défini avec la valeur **ApplicationId**.
- `clientSecret`: si vous souhaitez vous connecter à l’aide du secret client, créez-le dans Azure AD. En outre, utilisez une application web ou une API au lieu d’une application native. Ajoutez également l’application sous **Contrôle d’accès (IAM)** dans l’espace de noms que vous avez créé précédemment.
- `serviceBusNamespaceFQDN`: défini sur le nom DNS complet de l’espace de noms Service Bus que vous venez de créer, par exemple, `example.servicebus.windows.net`.
- `queueName`: défini sur le nom de la file d’attente que vous avez créé.
- URI de redirection que vous avez spécifié dans votre application lors des étapes précédentes.

Lorsque vous exécutez l’application console, vous êtes invité à sélectionner un scénario. Cliquez sur **Connexion interactive de l’utilisateur** en saisissant son numéro et en appuyant sur ENTRÉE. L’application affiche une fenêtre de connexion, vous demande l’autorisation d’accéder à Service Bus et utilise ensuite le service à exécuter via le scénario d’envoi/réception à l’aide de l’identité de connexion.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la messagerie Service Bus, voir les rubriques suivantes.

* [Files d’attente, rubriques et abonnements Service Bus](service-bus-queues-topics-subscriptions.md)
* [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Utilisation des rubriques et abonnements Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
