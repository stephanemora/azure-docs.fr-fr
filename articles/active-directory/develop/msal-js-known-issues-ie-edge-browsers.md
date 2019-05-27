---
title: Problèmes connus sur les navigateurs (bibliothèque d’authentification Microsoft pour JavaScript) | Azure
description: Découvrez les problèmes connus lors de l’utilisation de la bibliothèque d’authentification Microsoft pour JavaScript (MSAL.js) avec les navigateurs Internet Explorer et Microsoft Edge.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c57ed956ec50c8bac26720a27894c07353928336
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873899"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>Problèmes connus dans les navigateurs Internet Explorer et Microsoft Edge avec MSAL.js

## <a name="issues-due-to-security-zones"></a>Problèmes dus à des zones de sécurité
Nous avions plusieurs rapports de problèmes avec l’authentification dans Internet Explorer et Microsoft Edge (depuis la mise à jour de la *version du navigateur Microsoft Edge pour 40.15063.0.0*). Nous suivez ces et ont informé l’équipe Microsoft Edge. Bien que Microsoft Edge fonctionne sur une résolution, voici une description des problèmes fréquents et les solutions de contournement possibles qui peuvent être implémentées.

### <a name="cause"></a>Cause :
La cause de la plupart de ces problèmes est la suivante. Le stockage de session et le stockage local sont partitionnés par les zones de sécurité dans le navigateur Microsoft Edge. Dans cette version particulière de Microsoft Edge, lorsque l’application est redirigée entre les zones, le stockage de session et le stockage local sont effacées. Plus précisément, le stockage de session est désactivé dans la navigation dans un navigateur standard, et la session et le stockage local sont effacés dans le mode InPrivate du navigateur. MSAL.js enregistre un certain état dans le stockage de session et s’appuie sur la vérification de cet état pendant le flux d’authentification. Lorsque le stockage de session est désactivé, cet état est perdu et, par conséquent, entraîne des expériences rompues.

### <a name="issues"></a>Problèmes

- **Boucles de redirection infinie et page recharge lors de l’authentification**. Lorsque les utilisateurs se connectent à l’application sur Microsoft Edge, ils sont redirigés vers la page de connexion AAD et sont bloqués dans une boucle infinie de redirection résultant de rechargements de page répétés. Cela est généralement accompagnée d’un `invalid_state` erreur dans le stockage de session.

- **Infinite acquérir le jeton boucles et erreur de AADSTS50058**. Lorsqu’une application s’exécutant sur Microsoft Edge essaie d’acquérir un jeton pour une ressource, l’application peut rester bloquée dans une boucle infinie de l’acquisition de jeton appel, ainsi que l’erreur suivante d’AAD dans votre trace réseau :

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **Fenêtre contextuelle ne ferme pas ou est bloqué lors de l’utilisation de la connexion à l’aide contextuelle pour l’authentification**. Lors de l’authentification via la fenêtre contextuelle dans Microsoft Edge ou IE(InPrivate), après saisie des informations d’identification et vous être connecté, si plusieurs domaines entre des zones de sécurité sont impliqués dans le volet de navigation, la fenêtre contextuelle ne ferme pas, car MSAL.js perd le handle à la fenêtre contextuelle.  

    Voici des liens vers ces problèmes dans le suivi des problèmes Microsoft Edge :  
    - [Bogue 13861050](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861050/)
    - [Bogue 13861663](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861663/)

### <a name="update-fix-available-in-msaljs-023"></a>Mettre à jour : Correctif n’est disponible dans MSAL.js 0.2.3
Correctifs pour les problèmes de boucle de redirection d’authentification ont été publiées dans [MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases). Activer l’indicateur `storeAuthStateInCookie` dans le fichier config MSAL.js pour tirer parti de ce correctif. Par défaut, cet indicateur est défini sur false.

Lorsque le `storeAuthStateInCookie` indicateur est activé, MSAL.js utilise les cookies de navigateur pour stocker l’état de requête requis pour la validation des flux d’authentification.

> [!NOTE]
> Ce correctif n’est pas encore disponible pour les wrappers angular-msal et msal-angularjs. Ce correctif ne règle pas le problème avec les fenêtres contextuelles.

Utilisez les solutions de contournement ci-dessous.

#### <a name="other-workarounds"></a>Autres solutions de contournement
Veillez à tester que votre problème se produit uniquement sur la version spécifique de navigateur Microsoft Edge et il travaille sur les autres navigateurs avant d’adopter ces solutions de contournement.  
1. Dans un premier temps de contourner ces problèmes, vérifiez que le domaine d’application, et d’autres sites impliqués dans les redirections du flux d’authentification sont ajoutés en tant que sites de confiance dans les paramètres de sécurité du navigateur, afin qu’ils appartiennent à la même zone de sécurité.
Pour ce faire, procédez comme suit :
    - Ouvrez **Internet Explorer** , puis cliquez sur le **paramètres** (icône d’engrenage) dans l’angle supérieur droit
    - Sélectionnez **Options Internet**
    - Sélectionnez le **sécurité** onglet
    - Sous le **Sites de confiance** option, cliquez sur le **sites** bouton et ajoutez les URL dans la boîte de dialogue qui s’ouvre.

2. Comme mentionné précédemment, depuis seulement la session de stockage est désactivé au cours de la navigation normale, vous pouvez configurer MSAL.js pour utiliser le stockage local à la place. Cela peut être défini comme le `cacheLocation` paramètre de configuration lors de l’initialisation de MSAL.

Notez que cela ne résoudra pas le problème pour la navigation InPrivate, car la session et le stockage local sont effacées.

## <a name="issues-due-to-popup-blockers"></a>Problèmes dus à des bloqueurs de fenêtres

Cas sont les fenêtres contextuelles sont bloquées dans Internet Explorer ou Microsoft Edge, par exemple lorsqu’une fenêtre contextuelle deuxième se produit pendant l’authentification multifacteur. Vous obtiendrez une alerte dans le navigateur pour permettre une seule fois ou toujours la fenêtre contextuelle. Si vous choisissez d’autoriser, le navigateur s’ouvre automatiquement de la fenêtre contextuelle et retourne un `null` gérer pour celui-ci. Par conséquent, la bibliothèque n’a pas un handle de la fenêtre, et il n’existe aucun moyen pour fermer la fenêtre contextuelle. Le même problème ne se produit pas dans Chrome, lorsque vous êtes invité à autoriser les fenêtres contextuelles, car il n’ouvre pas automatiquement une fenêtre contextuelle.

Comme un **solution de contournement**, les développeurs doivent autoriser les fenêtres contextuelles dans Internet Explorer et Microsoft Edge avant de commencer à l’aide de leur application pour éviter ce problème.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [à l’aide de MSAL.js dans Internet Explorer](msal-js-use-ie-browser.md).
