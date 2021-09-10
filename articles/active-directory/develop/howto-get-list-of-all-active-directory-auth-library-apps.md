---
title: 'Procédure : Obtenir une liste complète de toutes les applications qui utilisent la Bibliothèque d’authentification Active Directory (ADAL) dans votre locataire | Azure'
titleSuffix: Microsoft identity platform
description: Ce guide pratique vous propose une liste complète de toutes les applications qui utilisent ADAL dans votre locataire.
services: active-directory
author: SHERMANOUKO
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/22/2021
ms.author: shermanouko
ms.custom: aaddev, has-adal-ref
ms.reviewer: aiwang, marsma
ms.openlocfilehash: 07f6c7f481e815e788b22782f01ad9369bd2c9f6
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039694"
---
# <a name="get-a-complete-list-of-apps-using-adal-in-your-tenant"></a>Obtenir une liste complète des applications qui utilisent ADAL dans votre locataire

La prise en charge de la Bibliothèque d’authentification Active Directory (ADAL) prendra fin le 30 juin 2022. Les applications qui utilisent ADAL sur des versions de système d’exploitation existantes continueront de fonctionner après cette date, mais elles ne bénéficieront plus du support technique ni des mises à jour de sécurité. Sans les mises à jour de sécurité continues, les applications utilisant ADAL deviendront de plus en plus vulnérables aux modèles d’attaque de sécurité les plus récents. Cet article explique comment utiliser les classeurs Azure Monitor pour obtenir une liste de toutes les applications qui utilisent ADAL dans votre locataire.

## <a name="sign-ins-workbook"></a>Classeur des connexions

Les classeurs sont un ensemble de requêtes qui collectent et permettent de visualiser des informations qui sont disponibles dans les journaux Azure Active Directory (Azure AD). [En savoir plus sur le schéma des journaux de connexion ici](../reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md). Le classeur des connexions dans le portail d’administration Azure AD dispose désormais d’un tableau pour vous aider à déterminer les applications qui utilisent ADAL et à quelle fréquence. Nous allons dans un premier temps expliquer comment accéder au classeur avant d’afficher la visualisation de la liste des applications.

## <a name="step-1-integrate-audit-logs-with-azure-monitor"></a>Étape 1 : Intégrer les journaux d’audit à Azure Monitor

Suivez cette procédure dans [Intégrer vos journaux d’audit et de connexion Azure AD à Azure Monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) avant d’accéder au classeur. Seuls les événements d’audit et de connexion créés après l’intégration d’Azure Monitor sont stockés.

## <a name="step-2-access-sign-ins-workbook-in-azure-portal"></a>Étape 2 : Accéder au classeur des connexions dans le Portail Azure

Une fois que vous avez intégré vos journaux d’audit et de connexion Azure AD à Azure Monitor comme spécifié dans l’intégration Azure Monitor, accédez au classeur des connexions :

   1. Connectez-vous au Portail Azure. 
   1. Accédez à  **Azure Active Directory** > **Supervision** > **Classeurs**. 
   1. Dans la section **Utilisation**, ouvrez le classeur  **Connexions**. 

   :::image type="content" source="media/howto-get-list-of-all-active-directory-auth-library-apps/sign-in-workbook.png" alt-text="Capture d’écran de l’interface des classeurs du portail Azure Active Directory mettant en évidence le classeur des connexions.":::

## <a name="step-3-identify-apps-that-use-adal"></a>Étape 3 : Identifier les applications qui utilisent ADAL

Le tableau au bas de la page du classeur des connexions répertorie les applications qui ont récemment utilisé ADAL. Vous pouvez également exporter une liste des applications. Mettez à jour ces applications pour qu’elles utilisent MSAL.
    
:::image type="content" source="media/howto-get-list-of-all-active-directory-auth-library-apps/active-directory-auth-library-apps-present.png" alt-text="Capture d’écran du classeur des connexions qui répertorie les applications qui utilisent la Bibliothèque d’authentification Active Directory.":::
    
Si aucune application n’utilise ADAL, le classeur affiche une vue comme ci-dessous. 
    
:::image type="content" source="media/howto-get-list-of-all-active-directory-auth-library-apps/no-active-directory-auth-library-apps.png" alt-text="Capture d’écran du classeur des connexions si aucune application n’utilise la Bibliothèque d’authentification Active Directory.":::

## <a name="step-4-update-your-code"></a>Étape 4 : Mettre à jour votre code

Après avoir identifié vos applications qui utilisent ADAL, migrez-les vers MSAL en fonction de votre type d’application, comme illustré ci-dessous.

[!INCLUDE [application type](includes/adal-msal-migration.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur MSAL, y compris des informations sur l’utilisation et sur les bibliothèques disponibles pour différents langages de programmation et types d’application, consultez :

- [Acquérir et mettre en cache des jetons à l’aide MSAL](msal-acquire-cache-tokens.md)
- [Options de configuration d’application](msal-client-application-configuration.md)
- [Liste des bibliothèques d’authentification MSAL](reference-v2-libraries.md)
