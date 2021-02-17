---
title: Obtenir les points de terminaison pour une inscription d’application Azure AD
titleSuffix: Microsoft identity platform
description: Guide pratique pour trouver les points de terminaison d’authentification pour une application personnalisée que vous développez ou que vous inscrivez auprès d’Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 778523869715916bf1e4c32af59ea7a0081df91b
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103309"
---
# <a name="how-to-discover-endpoints"></a>Comment découvrir des points de terminaison

Vous pouvez trouver les points de terminaison d’authentification pour votre application dans le [portail Azure](https://portal.azure.com).

1. Connectez-vous au <a href="https://portal.azure.com/" target="_blank">portail Azure</a>.
1. Sélectionnez **Azure Active Directory**.
1. Sous **Gérer**, sélectionnez **Inscriptions d’applications**, puis sélectionnez **Points de terminaison** dans le menu supérieur.

    La page **Points de terminaison** s’affiche et présente tous les points de terminaison d’authentification pour votre locataire.
    
    Utilisez le point de terminaison qui correspond au protocole d’authentification que vous utilisez en combinaison avec l’**ID d’application (client)** pour créer la demande d’authentification spécifique à votre application.

Les **clouds nationaux** (comme Azure AD Chine, Allemagne ou US Government) disposent de leur propre portail d’inscription d’application, ainsi que de leurs propres points de terminaison d’authentification Azure AD. Pour plus d’informations, consultez la [présentation des clouds nationaux](authentication-national-cloud.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les points de terminaison dans les différents environnements Azure, consultez la [Vue d’ensemble des clouds nationaux](authentication-national-cloud.md).
