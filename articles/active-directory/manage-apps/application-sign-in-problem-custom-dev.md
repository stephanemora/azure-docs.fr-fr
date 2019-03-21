---
title: Problèmes de connexion à une application personnalisée | Microsoft Docs
description: Erreurs courantes pouvant vous empêcher de vous connecter à une application développée avec Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4365c87f0028fe3a9c4ba35f40599359eb1455f
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259123"
---
# <a name="problems-signing-in-to-an-custom-developed-application"></a>Problèmes de connexion à une application personnalisée

Plusieurs erreurs peuvent vous empêcher de vous connecter à une application. La raison principale vient du fait que les applications sont mal configurées.

## <a name="errors-related-to--misconfigured-apps"></a>Erreurs liées à des applications mal configurées

* Vérifiez que les deux configurations dans le portail correspondent à ce que vous avez dans votre application. Plus précisément, comparez l’ID de client/d’application, les URL de réponse, les clés secrètes client/clés et l’URI ID d’application.

* Comparez la ressource à laquelle vous souhaitez accéder dans le code aux autorisations configurées sous l’onglet **Ressources nécessaires** pour vérifier que vous demandez uniquement les ressources que vous avez configurées.

* Pour obtenir des informations sur des erreurs ou des problèmes similaires, consultez [Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory).

## <a name="next-steps"></a>Étapes suivantes

[Guide de développement Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Consentement et intégration d’applications à Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Consentement et octroi d’autorisations pour les applications convergées Azure AD v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory>)
