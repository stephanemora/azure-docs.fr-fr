---
title: Configuration de l’authentification et de l’autorisation Service Bus Azure | Microsoft Docs
description: Authentifiez des applications dans Service Bus avec l’authentification Signature d’accès partagé (SAS).
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 7234e33c04e742c77630f8d87481c7831fb00bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70013243"
---
# <a name="service-bus-authentication-and-authorization"></a>Authentification et de autorisation Service Bus

Les applications peuvent accéder aux ressources Azure Service Bus à l’aide de l’authentification par jeton de signature d’accès partagé. Avec la signature d’accès partagé (SAS), les applications présentent à Service Bus un jeton qui a été signé avec une clé symétrique connue de l’émetteur du jeton et de Service Bus (donc « partagée »). Cette clé est directement associée à une règle qui accorde des droits d’accès, tels que l’autorisation de recevoir, d’écouter ou d’envoyer des messages. Les règles de signature d’accès partagé sont soit configurées dans l’espace de noms, soit directement dans les entités telles qu’une file d’attente ou un sujet, ce qui permet un contrôle d’accès affiné.

Les jetons SAS peuvent être générés directement par un client Service Bus ou par un point de terminaison intermédiaire émetteur de jetons, avec lequel le client interagit. Par exemple, un système peut nécessiter que le client appelle un point de terminaison de service web Active Directory protégé par autorisation afin de prouver son identité et ses droits d’accès système. Dans ce cas, le service web retourne le jeton Service Bus approprié. Ce jeton SAS peut être généré facilement à l’aide du fournisseur de jetons Service Bus inclus dans le Kit de développement logiciel (SDK) Azure. 

> [!IMPORTANT]
> Si vous utilisez Azure Active Directory Access Control (également appelé Access Control Service ou ACS) avec Service Bus, notez que la prise en charge de cette méthode est désormais limitée et que vous devez migrer votre application en vue d’utiliser la signature d’accès partagé. Pour plus d’informations, consultez [ce billet de blog](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) et [cet article](service-bus-migrate-acs-sas.md).

## <a name="azure-active-directory"></a>Azure Active Directory
L'intégration Azure Active Directory (Azure AD) pour les ressources Service Bus fournit un contrôle d'accès en fonction du rôle (RBAC), qui permet un contrôle affiné de l'accès d'un client aux ressources. Vous pouvez utiliser le contrôle d’accès en fonction du rôle (RBAC) pour accorder des autorisations à un principal de sécurité, qui peut être un utilisateur, un groupe ou un principal de service d’application. Le principal de sécurité est authentifié par Azure AD pour retourner un jeton OAuth 2.0. Le jeton peut être utilisé pour autoriser une requête d’accès à une ressource Service Bus (file d’attente, rubrique, etc.).

Pour plus d’informations sur l’authentification avec Azure AD, consultez les articles suivants :

- [Authentifier avec des identités managées](service-bus-managed-service-identity.md)
- [Authentifier à partir d’une application](authenticate-application.md)

> [!IMPORTANT]
> L’autorisation des utilisateurs ou des applications avec un jeton OAuth 2.0 retourné par Azure AD assure une meilleure sécurité que les signatures d’accès partagé. De plus, elle offre une plus grande simplicité d’utilisation. Azure AD vous évite d’avoir à stocker les jetons dans votre code. Vous êtes ainsi moins exposé au risque de failles de sécurité. Nous vous recommandons d’utiliser Azure AD avec vos applications Azure Service Bus dans la mesure du possible. 


## <a name="shared-access-signature"></a>Signature d’accès partagé
[L’authentification SAP](service-bus-sas.md) vous permet d’accorder un accès utilisateur aux ressources Service Bus avec des droits spécifiques. L’authentification SAP dans Service Bus implique la configuration d’une clé de chiffrement avec les droits associés sur une ressource Service Bus. Les clients peuvent alors accéder à cette ressource en présentant un jeton SAS qui se compose de la ressource URI à laquelle accéder et une échéance signée avec la clé configurée.

Vous pouvez configurer des clés pour SAS dans un espace de noms Service Bus. La clé s’applique à toutes les entités de messagerie dans cet espace de noms. Vous pouvez également configurer des clés sur les rubriques et files d’attente Service Bus. SAP est également pris en charge par [Azure Relay](../service-bus-relay/relay-authentication-and-authorization.md).

Pour utiliser SAS, vous pouvez configurer un objet [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) sur un espace de noms, une file d’attente ou une rubrique. Cette règle se compose des éléments suivants :

* *KeyName* : identifie la règle.
* *PrimaryKey* : clé de chiffrement utilisée pour signer/valider les jetons SAS.
* *SecondaryKey* : clé de chiffrement utilisée pour signer/valider les jetons SAS.
* *Rights* : représente la collection des droits **Listen**, **Send** ou **Manage** accordés.

Les règles d’autorisation configurées au niveau de l’espace de noms peuvent accorder l’accès à toutes les entités dans un espace de noms pour les clients avec des jetons signés à l’aide de la clé correspondante. Vous pouvez configurer jusqu’à 12 règles d’autorisation de ce type sur un espace de noms, une file d’attente ou une rubrique Service Bus. Par défaut, un élément [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) avec tous les droits est configuré pour chaque espace de noms dès sa mise en service initiale.

Pour accéder à une entité, le client requiert un jeton SAP créé à l’aide d’une règle [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)spécifique. Le jeton SAS est généré à l’aide du code HMAC-SHA256 d’une chaîne de ressource qui se compose de l’URI de la ressource à laquelle vous souhaitez accéder et d’une échéance avec une clé de chiffrement associée à la règle d’autorisation.

La prise en charge de l’authentification SAS pour Service Bus est incluse dans le Kit de développement Azure .NET SDK versions 2.0 et ultérieures. SAP inclut l’assistance pour [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Toutes les API qui acceptent une chaîne de connexion en tant que paramètre incluent la prise en charge des chaînes de connexion des services SAS.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la signature d’accès partagé (SAP), consultez [Authentification de Service Bus avec les signatures d’accès partagé](service-bus-sas.md).
- [Migrer du service Access Control Service d’Azure Active Directory vers le service de signature d’accès partagé](service-bus-migrate-acs-sas.md).
- [Modifications des espaces de noms ACS](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).
- Pour obtenir les informations correspondantes sur les autorisations et l’authentification Azure Relay, consultez [Authentification et autorisation Azure Relay](../service-bus-relay/relay-authentication-and-authorization.md). 

