---
title: Utilisation de b2clogin.com | Microsoft Docs
description: Découvrez comment utiliser b2clogin.com au lieu de login.microsoftonline.com.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1d42d9a97244eeff501b9d02b0f143d6ef0c91b2
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440627"
---
# <a name="using-b2clogincom"></a>Utilisation de b2clogin.com

>[!IMPORTANT]
>Cette fonctionnalité est en préversion publique. 
>

Vous avez maintenant la possibilité d’utiliser le service Azure AD B2C avec `<YourTenantName>.b2clogin.com` au lieu de `login.microsoftonline.com`,  ce qui présente de nombreux avantages :
* Vous ne partagez plus la même limite de taille d’en-tête de cookie avec les autres produits Microsoft.
* Vous pouvez supprimer toutes les références à Microsoft dans votre URL (vous pourrez remplacer `<YourTenantName>.onmicrosoft.com` par votre ID de locataire). Par exemple : `https://<tenantname>.b2clogin.com/tfp/<tenantname>/<policyname>/v2.0/.well-known/openid-configuration`.

 Pour pouvoir tirer parti de b2clogin.com, définissez les valeurs suivantes :

1. Pour **Exécuter maintenant le point de terminaison**, vérifiez que vous utilisez `<YourTenantName>.b2clogin.com` plutôt que `login.microsoftonline.com`.
2. Si vous utilisez MSAL, définissez `validateauthority=false`.  En effet, l’émetteur du jeton devient `<YourTenantName>.b2clogin.com`.