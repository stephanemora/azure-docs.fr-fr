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
ms.openlocfilehash: f6c2540d8f0bb49491a428b085d20067a36d970a
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44723797"
---
# <a name="how-to-select-permissions-for-a-given-api"></a>Guide pratique pour sélectionner les autorisations pour une API donnée

Vous pouvez trouver les points de terminaison d’authentification pour votre application dans le [portail Azure](https://portal.azure.com).

-   Accédez au [portail Azure](https://portal.azure.com).

-   Dans le volet de navigation gauche, cliquez sur **Azure Active Directory**.

-   Cliquez sur **Inscriptions des applications** puis choisissez **Points de terminaison**.

-   Ceci ouvre la page **Points de terminaison** qui répertorie tous les points de terminaison d’authentification pour votre locataire.

-   Utilisez le point de terminaison spécifique au protocole d’authentification que vous utilisez, en combinaison avec l’ID d’application pour créer la demande d’authentification spécifique à votre application.

## <a name="next-steps"></a>Étapes suivantes
[Guide du développeur Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide#authentication-and-authorization-protocols)
