---
title: Fichier include
description: Fichier include
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: c0ba8e76e069bf9dc1c96aecdc670699c32b3c96
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "90944587"
---
## <a name="object-model"></a>Modèle objet

Les jetons d’accès utilisateur vous permettent d’autoriser les utilisateurs de votre application à se connecter directement à votre ressource Azure Communication Services. Vous devez générer ces jetons sur votre serveur, les repasser à votre application cliente, puis les utiliser pour initialiser les bibliothèques de client Communication Services. Pour ce faire, vous créez un point de terminaison de service approuvé qui peut authentifier vos utilisateurs et vous émettez les jetons d’accès utilisateur. Ce service doit tenir à jour un mappage persistant entre les identités des utilisateurs de votre application et les utilisateurs Azure Communication Services.

La classe CommunicationIdentityClient fournit toutes les fonctionnalités liées aux jetons d’accès et aux utilisateurs. Vous l’instanciez avec une chaîne de connexion, puis vous l’utilisez pour gérer les utilisateurs et émettre des jetons d’accès.

> [!WARNING]
> Les jetons sont des données sensibles, car elles donnent accès aux ressources d’un utilisateur. Par conséquent, il est essentiel de protéger les jetons. Le point de terminaison de service approuvé que vous créez doit uniquement émettre des jetons d’accès pour les utilisateurs autorisés de votre application. Si vous mettez en cache des jetons d’accès utilisateur dans un magasin de stockage, il est fortement recommandé d’utiliser le chiffrement.

### <a name="access-token-scopes"></a>Étendues des jetons d’accès

Les étendues vous permettent de spécifier avec précision les fonctionnalités Azure Communication Services qu’un jeton d’accès utilisateur pourra autoriser. Les étendues sont appliquées aux jetons d’accès utilisateur de manière individuelle. Azure Communication Services prend en charge les étendues suivantes pour les jetons d’accès utilisateur :

| Nom   | Description                                                                         |
| ------ | ----------------------------------------------------------------------------------- |
| `Chat` | Permet de participer à une conversation                                         |
| `VoIP` | Permet de créer et recevoir des appels VOIP en utilisant la bibliothèque de client appelante* |
| `Pstn` | Permet d’effectuer des appels PSTN en utilisant la bibliothèque de client appelante*           |

\* Cette fonctionnalité n’est pas encore prise en charge, mais elle sera bientôt disponible

Si vous souhaitez retirer l’accès d’un utilisateur à certaines fonctionnalités, vous devez [révoquer les jetons d’accès existants](#revoke-user-access-tokens) susceptibles d’inclure des étendues non souhaitées avant d’émettre un nouveau jeton avec un ensemble d’étendues plus limité.
