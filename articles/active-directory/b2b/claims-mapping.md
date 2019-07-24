---
title: Mappage des revendications d’utilisateur B2B Collaboration - Azure Active Directory | Microsoft Docs
description: Personnaliser les revendications d’utilisateur émises dans le jeton SAML pour les utilisateurs d’Azure Active Directory (Azure AD) B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: edb18c31f40de3358ad987be4a9c67ed3a5079e8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65811928"
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Mappage des revendications d’utilisateur B2B Collaboration dans Azure Active Directory

Azure Active Directory (Azure AD) prend en charge la personnalisation des revendications émises dans le jeton SAML pour les utilisateurs B2B Collaboration. Lorsqu’un utilisateur s’authentifie auprès de l’application, Azure AD émet un jeton SAML à l’application qui contient des informations (ou des revendications) sur l’utilisateur qui l’identifient de façon unique. Par défaut, ceci inclut le nom d’utilisateur, l’adresse e-mail, le prénom et nom de l’utilisateur.

Le [Portail Azure](https://portal.azure.com) permet d’afficher et de modifier les revendications envoyées à l’application dans le jeton SAML. Pour accéder aux paramètres, sélectionnez **Azure Active Directory** > **Applications d’entreprise** > l’application configurée pour l’authentification unique > **Authentification unique**. Consultez les paramètres des jetons SAML dans la section **Attributs utilisateurs**.

![Attributs des jetons SAML dans l’interface utilisateur](media/claims-mapping/view-claims-in-saml-token.png)

Il existe deux cas de figure dans lesquels il peut se révéler nécessaire de modifier les revendications émises dans le jeton SAML :

1. L’application a besoin d’un ensemble différent d’URI ou de valeurs de revendication.

2. La revendication NameIdentifier doit être différente du nom d’utilisateur principal stocké dans Azure AD pour l’application.

Pour plus d’informations sur l’ajout et la modification de revendications, consultez la page [Personnaliser les revendications émises dans le jeton SAML pour les applications d’entreprise dans Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

Pour les utilisateurs B2B Collaboration, le mappage entre locataires de NameID et UPN est empêché pour des raisons de sécurité.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les propriétés des utilisateurs B2B Collaboration, consultez la page [Propriétés d’un utilisateur d’Azure Active Directory B2B Collaboration](user-properties.md).
- Pour plus d’informations sur les jetons d’utilisateur pour les utilisateurs de B2B Collaboration, consultez la page [Comprendre les jetons d’utilisateurs dans Azure AD B2B Collaboration](user-token.md).

