---
title: Guide pratique pour sélectionner les autorisations pour une API donnée | Microsoft Docs
description: Guide pratique pour trouver les points de terminaison d’authentification pour une application personnalisée que vous développez ou que vous inscrivez auprès d’Azure AD.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: dcb3a8f735b72b2408e28d2f0dc61b2c634baf96
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366631"
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
