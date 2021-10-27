---
title: API Microsoft Graph
description: L’API Microsoft Graph est une API web RESTful qui vous permet d’accéder aux ressources des services cloud Microsoft.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/08/2021
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: a0159f1c633806c26bda39ffe0c8a295bec46982
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129993699"
---
# <a name="microsoft-graph-api"></a>API Microsoft Graph

L’API Microsoft Graph est une API web RESTful qui vous permet d’accéder aux ressources des services cloud Microsoft. Une fois que vous avez inscrit votre application et obtenu les jetons d’authentification d’un utilisateur ou d’un service, vous pouvez adresser des demandes à l’API Microsoft Graph. Pour plus d’informations, consultez [Présentation de Microsoft Graph](/graph/overview).

Microsoft Graph expose les API REST et les bibliothèques clientes pour accéder aux données des services cloud Microsoft suivants :

- Services Microsoft 365 : Delve, Excel, Microsoft Bookings, Microsoft Teams, OneDrive, OneNote, Outlook/Exchange, Planner et SharePoint
- Services Enterprise Mobility + Security : Advanced Threat Analytics, Advanced Threat Protection, Azure Active Directory, Identity Manager et Intune
- Services Windows 10 : activités, appareils, notifications
- Dynamics 365 Business Central

## <a name="versions"></a>Versions

Les versions suivantes de l’API Microsoft Graph sont actuellement disponibles :

- **Version bêta** : la version bêta comprend des API actuellement en préversion et accessibles dans le point de terminaison `https://graph.microsoft.com/beta`. Pour commencer à utiliser les API bêta, consultez [Référence de point de terminaison bêta Microsoft Graph](/graph/api/overview?view=graph-rest-beta&preserve-view=true)
- **Version v1.0** : la version v1.0 comprend des API qui sont généralement disponibles et prêtes pour une utilisation en production. La version v1.0 est accessible dans le point de terminaison `https://graph.microsoft.com/v1.0`. Pour commencer à utiliser les API v1.0, consultez [Référence version 1.0 de l’API REST Microsoft Graph](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)

Pour plus d’informations sur les versions de l’API Microsoft Graph, consultez [Gestion de versions, support et stratégies de changements cassants pour Microsoft Graph](/graph/versioning-and-support).


## <a name="get-started"></a>Bien démarrer

Pour lire ou écrire dans une ressource telle qu’un utilisateur ou un e-mail, vous devez créer une requête qui ressemble au modèle suivant :

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

Pour plus d’informations sur les éléments de la requête à créer, consultez [Utiliser l’API Microsoft Graph](/graph/use-the-api)

Des exemples de démarrage rapide sont disponibles pour vous montrer comment accéder à la puissance de l’API Microsoft Graph. Les exemples disponibles accèdent à deux services via une seule authentification : compte Microsoft et Outlook. Chaque démarrage rapide accède aux informations des profils des utilisateurs du compte Microsoft et affiche les événements de leur calendrier.
Les démarrages rapides impliquent quatre étapes :

- Sélectionner votre plateforme
- Obtenir votre ID d’application (ID de client)
- Générer l’exemple
- Se connecter et afficher les événements sur votre calendrier

Une fois le démarrage rapide terminé, vous disposez d’une application prête à être exécutée. Pour plus d’informations, consultez le [FAQ du démarrage rapide de Microsoft Graph](/graph/quick-start-faq). Pour commencer à utiliser les exemples, consultez [Démarrage rapide de Microsoft Graph](https://developer.microsoft.com/graph/quick-start).

## <a name="tools"></a>Outils

L’**afficheur Microsoft Graph** est un outil web que vous pouvez utiliser pour générer et tester des demandes vers l’API Microsoft Graph. Accédez à l’afficheur Microsoft Graph à l’adresse https://developer.microsoft.com/graph/graph-explorer.

**Postman** est un autre outil que vous pouvez utiliser pour adresser des demandes à l’API Microsoft Graph. Vous pouvez télécharger Postman à l’adresse https://www.getpostman.com. Pour interagir avec Microsoft Graph dans Postman, utilisez la [collection Postman Microsoft Graph](/graph/use-postman).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Microsoft Graph, y compris les informations d’utilisation et les didacticiels, consultez :

- [Utiliser l’API Microsoft Graph](/graph/use-the-api)
- [Tutoriels Microsoft Graph](/graph/tutorials)
