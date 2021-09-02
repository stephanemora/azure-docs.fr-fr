---
title: Authentification et autorisation Azure Relay | Microsoft Docs
description: Cet article présente l’authentification par signature d’accès partagé (SAS) auprès du service Azure Relay.
ms.topic: article
ms.date: 07/19/2021
ms.openlocfilehash: dbabee7f49e4f905c34a91dcb6095e7eab3faa0f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562881"
---
# <a name="azure-relay-authentication-and-authorization"></a>Authentification et autorisation Azure Relay
Il existe deux façons d’authentifier et d’autoriser l’accès aux ressources Azure Relay : Azure Active Directory (Azure AD) et les signatures d’accès partagé (SAP). Cet article vous explique l’utilisation de ces deux types de mécanismes de sécurité.

## <a name="azure-active-directory-preview"></a>Azure Active Directory (Préversion)
L’intégration Azure AD pour les ressources Azure Relay fournit un contrôle d’accès en fonction du rôle Azure (Azure RBAC), qui permet un contrôle affiné de l’accès d’un client aux ressources. Vous pouvez utiliser Azure RBAC pour accorder des autorisations à un principal de sécurité, qui peut être un utilisateur, un groupe ou un principal de service d’application. Le principal de sécurité est authentifié par Azure AD pour retourner un jeton OAuth 2.0. Le jeton peut être utilisé pour autoriser une demande d'accès à une ressource Azure Relay.

Pour plus d’informations sur l’authentification avec Azure AD, consultez les articles suivants :
- [Authentifier avec des identités managées](authenticate-managed-identity.md)
- [S’authentifier à partir d’une application Azure Active Directory](authenticate-application.md)

> [!IMPORTANT]
> L’autorisation des utilisateurs ou des applications avec un jeton OAuth 2.0 retourné par Azure AD assure une meilleure sécurité que les signatures d’accès partagé. De plus, elle offre une plus grande simplicité d’utilisation. Azure AD vous évite d’avoir à stocker des jetons dans votre code. Vous êtes ainsi moins exposé au risque de failles de sécurité. Nous vous recommandons d’utiliser Azure AD avec vos applications Azure Relay dans la mesure du possible.

### <a name="built-in-roles"></a>Rôles intégrés
Pour Azure Relay, la gestion des espaces de noms et de toutes les ressources associées via le portail Azure et l’API de gestion des ressources Azure est déjà protégée à l’aide du modèle Azure RBAC. Azure fournit les rôles Azure intégrés ci-dessous pour autoriser l’accès à un espace de noms Relay :

| Rôle | Description | 
| ---- | ----------- | 
| [Propriétaire d’Azure Relay](../role-based-access-control/built-in-roles.md#azure-relay-owner) | Utilisez ce rôle pour octroyer l’accès **complet** aux ressources Azure Relay. |
| [Écouteur Azure Relay](../role-based-access-control/built-in-roles.md#azure-relay-listener) | Utilisez ce rôle pour octroyer l’accès **écoute et lecture d’entité** aux ressources Azure Relay. |
| [Expéditeur Azure Relay](../role-based-access-control/built-in-roles.md#azure-relay-sender) | Utilisez ce rôle pour octroyer l’accès **envoi et lecture d’entité** aux ressources Azure Relay. | 


## <a name="shared-access-signature"></a>Signature d’accès partagé
Les applications peuvent s’authentifier auprès d’Azure Relay à l’aide de l’authentification par signature d’accès partagé. L’authentification par signature d’accès partagé permet aux applications de s’authentifier auprès du service Azure Relay à l’aide d’une clé d’accès configurée dans l’espace de noms Relay. Vous pouvez ensuite utiliser cette clé pour générer un jeton de signature d’accès partagé que les clients peuvent alors utiliser pour s’authentifier auprès du service Azure Relay.

[L’authentification par signature d’accès partagé](../service-bus-messaging/service-bus-sas.md) vous permet d’accorder un accès utilisateur aux ressources Azure Relay avec des droits spécifiques. L’authentification SAP implique la configuration d’une clé de chiffrement avec les droits associés sur une ressource. Les clients peuvent alors accéder à cette ressource en présentant un jeton SAS qui se compose de la ressource URI à laquelle accéder et une échéance signée avec la clé configurée.

Vous pouvez configurer des clés pour SAP dans un espace de noms Relay. Contrairement à la messagerie Service Bus, les [connexions hybrides Relay](relay-hybrid-connections-protocol.md) prennent en charge les expéditeurs non autorisés ou anonymes. Vous pouvez activer l’accès anonyme pour l’entité lors de sa création, comme indiqué dans la capture d’écran suivante du portail :

![Une boîte de dialogue intitulée « Créer une connexion hybride » contient une zone de texte « Nom » et une case à cocher intitulée « Nécessite l’authentification du client », qui est activée.][0]

Pour utiliser SAP, vous pouvez configurer un objet [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) dans un espace de noms Relay, qui est constitué des éléments suivants :

* *Nom de clé* qui identifie la règle.
* *clé primaire* est une clé de chiffrement utilisée pour signer/valider les jetons SAS.
* *clé secondaire* est une clé de chiffrement utilisée pour signer/valider les jetons SAS.
* *droits* représentant la collection des droits écouter, envoyer ou gérer les droits accordés.

Les règles d’autorisation configurées au niveau de l’espace de noms peuvent accorder l’accès à toutes les connexions Relay dans un espace de noms pour les clients disposant de jetons signés à l’aide de la clé correspondante. Un maximum de 12 règles d’autorisation peut être configuré dans un espace de noms Relay. Par défaut, un élément [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) avec tous les droits est configuré pour chaque espace de noms dès sa mise en service initiale.

Pour accéder à une entité, le client requiert un jeton SAP créé à l’aide d’une règle [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)spécifique. Le jeton SAS est généré à l’aide du code HMAC-SHA256 d’une chaîne de ressource qui se compose de l’URI de la ressource à laquelle vous souhaitez accéder et d’une échéance avec une clé de chiffrement associée à la règle d’autorisation.

La prise en charge de l’authentification SAP pour Azure Relay est incluse dans le Kit de développement logiciel (SDK) Azure .NET, versions 2.0 et ultérieures. SAP inclut l’assistance pour [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Toutes les API qui acceptent une chaîne de connexion en tant que paramètre incluent la prise en charge des chaînes de connexion des services SAS.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la signature d’accès partagé (SAP), consultez [Authentification de Service Bus avec les signatures d’accès partagé](../service-bus-messaging/service-bus-sas.md).
- Pour en savoir plus sur la fonctionnalité Connexions hybrides, consultez le [Guide du protocole de connexions hybrides Azure Relay](relay-hybrid-connections-protocol.md).
- Pour obtenir les informations correspondantes sur les autorisations et l’authentification de la messagerie Service Bus, consultez [Authentification et autorisation Service Bus](../service-bus-messaging/service-bus-authentication-and-authorization.md). 

[0]: ./media/relay-authentication-and-authorization/hcanon.png