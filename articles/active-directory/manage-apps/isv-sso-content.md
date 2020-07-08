---
title: Activer SSO pour votre application multilocataire
description: Conseils pour les éditeurs de logiciels indépendants sur l’intégration avec Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: kenwith
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26fa2b82b89c7b9ce1e05062718f2c4d0c93197c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763276"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>Activer l’authentification unique pour votre application multilocataire  

Lorsque vous offrez votre application à d’autres entreprises par le biais d’un achat ou d’un abonnement, vous la mettez à disposition des clients au sein de leurs propres locataires Azure. Ceci permet de créer une application multilocataires. Pour un aperçu de ce concept, consultez [Applications multilocataires dans Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) et [Location dans Azure Active Directory](../develop/single-and-multi-tenant-apps.md).

## <a name="what-is-single-sign-on"></a>Qu’est-ce que l’authentification unique

L’authentification unique (SSO) est plus sûre et plus pratique quand les utilisateurs s’authentifient auprès d’applications dans Azure Active Directory et d’autres identités. Lorsqu’une application est compatible avec la SSO, les utilisateurs ne sont pas tenus d’entrer des informations d’identification distinctes pour accéder à cette application. Pour obtenir une explication complète de l’authentification unique. [Consultez S’authentifier avec l’authentification unique auprès des applications dans Azure Active Directory](what-is-single-sign-on.md).

## <a name="why-enable-single-sign-on-in-your-application"></a>Pourquoi activer l’authentification unique dans votre application ?

Il y a de nombreux avantages à activer le SSO dans votre application multilocataire. Lorsque vous activez SSO pour votre application :

* Votre application peut être répertoriée dans la Place de marché Azure, où elle pourra être découverte par des millions d’organisations à l’aide d’Azure Active Directory.
  * Permet aux clients de configurer rapidement l’application avec Azure AD.

* Votre application peut être détectable dans la galerie d’applications Office 365, dans le lanceur d’applications Office 365 et au sein de la Recherche Microsoft sur Office.com

* Votre application peut utiliser l’API REST de Microsoft Graph pour accéder aux données qui favorisent la productivité de l’utilisateur, disponible à partir de Microsoft Graph.

* Vous réduisez les coûts de support en facilitant la tâche à vos clients.
  * La documentation spécifique à l’application coproduite avec l’équipe d’Azure AD pour nos clients communs facilite l’adoption.
  * Si le SSO en un seul clic est activé, les administrateurs informatiques de vos clients n’ont pas besoin d’apprendre à configurer votre application pour l’utiliser dans leur organisation.

* Vous offrez à vos clients la possibilité de gérer complètement l’authentification et l’autorisation des identités de leurs employés et de leurs invités.

  * Toute la responsabilité de la gestion des comptes et de la conformité incombe alors au client propriétaire de ces identités.

  * En offrant la possibilité d’activer ou de désactiver le SSO pour des fournisseurs d’identité, des groupes ou des utilisateurs spécifiques, vous êtes en mesure de répondre à leurs besoins d’affaires.

* Vous augmentez vos possibilités de commercialisation et d’adoption. De nombreuses grandes entreprises exigent (ou souhaitent) que leurs employés bénéficient d’une expérience SSO transparente dans toutes les applications. Il est important de faciliter l’authentification unique.

* Vous réduisez les frustrations des utilisateurs finaux, ce qui peut augmenter l’utilisation par les utilisateurs finaux et vos revenus.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>Comment activer l’authentification unique dans votre application publiée

1. [Choisir le protocole de fédération approprié pour votre application multilocataire](isv-choose-multi-tenant-federation.md).
1. Implémenter l’authentification unique dans votre application
   - ‎Consultez [obtenir des conseils sur les modèles d’authentification](../develop/v2-app-types.md)
   - Consultez [exemples de code Azure Active Directory](../develop/sample-v2-code.md) pour des protocoles OIDC et OAuth
1. [Créez votre locataire Azure](isv-tenant-multi-tenant-app.md) et testez votre application
1. [Créez et publiez une documentation sur l’authentification unique sur votre site Web](isv-create-sso-documentation.md).
1. [Soumettez votre liste d’applications](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) et établissez un partenariat avec Microsoft pour créer de la documentation sur le site de Microsoft.
1. [Rejoignez le Microsoft Partner Network (gratuit) et élaborez votre plan de commercialisation](https://partner.microsoft.com/en-us/explore/commercial#gtm).
