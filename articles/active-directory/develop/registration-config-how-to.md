---
title: Guide pratique pour sélectionner les autorisations pour une API donnée | Microsoft Docs
description: Guide pratique pour trouver les points de terminaison d’authentification pour une application personnalisée que vous développez ou que vous inscrivez auprès d’Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: d9db2182c20663cc048a5c9501df2586db25d2f3
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364929"
---
# <a name="how-to-configure-endpoints"></a>Guide pratique pour configurer des points de terminaison

Vous pouvez trouver les points de terminaison d’authentification pour votre application dans le [portail Azure](https://portal.azure.com).

-   Accédez au [portail Azure](https://portal.azure.com).

-   Dans le volet de navigation gauche, cliquez sur **Azure Active Directory**.

-   Cliquez sur **Inscriptions des applications** puis choisissez **Points de terminaison**.

-   Ceci ouvre la page **Points de terminaison** qui répertorie tous les points de terminaison d’authentification pour votre locataire.

-   Utilisez le point de terminaison spécifique au protocole d’authentification que vous utilisez, en combinaison avec l’ID d’application pour créer la demande d’authentification spécifique à votre application.

## <a name="next-steps"></a>Étapes suivantes
[Guide du développeur Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide#authentication-and-authorization-protocols)
