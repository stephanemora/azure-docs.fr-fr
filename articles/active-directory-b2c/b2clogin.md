---
title: Utiliser b2clogin.com| Microsoft Docs
description: Découvrez comment utiliser b2clogin.com au lieu de login.microsoftonline.com.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/29/2018
ms.author: davidmu
ms.openlocfilehash: d2737e995b91caa2dcc55027baa2b552e64af23e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2018
ms.locfileid: "33660419"
---
#<a name="using-b2clogincom"></a>Utilisation de b2clogin.com

>[!IMPORTANT]
>Cette fonctionnalité est en préversion publique. 
>

Vous avez maintenant la possibilité d’utiliser le service Azure AD B2C avec `<YourTenantName>.b2clogin.com` au lieu de `login.microsoftonline.com`,  ce qui présente de nombreux avantages :
* Vous ne partagerez plus la même limite de taille d’en-tête de cookie avec les autres produits Microsoft.
* Vous pourrez supprimer toutes les références à Microsoft dans votre URL (vous pourrez remplacer `<YourTenantName>.onmicrosoft.com` par votre ID client).

 Pour pouvoir tirer parti de b2clogin.com, définissez les valeurs suivantes :

1. Pour **Exécuter maintenant le point de terminaison**, vérifiez que vous utilisez `<YourTenantName>.b2clogin.com` plutôt que `login.microsoftonline.com`.
2. Si vous utilisez MSAL, définissez `validateauthority=false`.  En effet, l’émetteur du jeton devient `<YourTenantName>.b2clogin.com`.