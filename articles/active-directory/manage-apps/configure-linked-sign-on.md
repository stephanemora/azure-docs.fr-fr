---
title: Authentification liée pour les applications Azure AD – Plateforme d’identité Microsoft
description: Configurer l’authentification unique liée pour les applications d’entreprise Azure AD dans la plateforme d’identité Microsoft (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c4547bddeea8b67bd3377124b1c299662fea3e6
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274166"
---
# <a name="configure-linked-sign-on"></a>Configurer l’authentification liée

Lorsque vous ajoutez une application web provenant ou non de la galerie, l’une des options d’authentification unique disponibles est l’[authentification liée](what-is-single-sign-on.md). Sélectionnez cette option pour ajouter un lien vers l’application au panneau d’accès Azure AD ou au portail Office 365 de votre organisation. Vous pouvez utiliser cette méthode pour ajouter des liens à des applications web personnalisées qui utilisent actuellement des services de fédération Active Directory (ou un autre service de fédération) au lieu d’Azure AD pour l’authentification. Ou bien, vous pouvez ajouter des liens ciblés vers des pages SharePoint spécifiques ou d'autres pages Web dont vous souhaitez qu'elles s'affichent uniquement sur vos panneaux d'accès d'utilisateurs.

## <a name="before-you-begin"></a>Avant de commencer

Si l’application n’a pas été ajoutée à votre locataire Azure AD, consultez [Ajouter une application figurant dans la galerie](add-gallery-app.md) ou [Ajouter une application ne figurant pas dans la galerie](add-non-gallery-app.md).

### <a name="open-the-app-and-select-linked-sign-on"></a>Ouvrir l’application et sélectionner l’authentification liée

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur d’application cloud ou administrateur d’application pour votre locataire Azure AD.

1. Accédez à **Azure Active Directory** > **Applications d’entreprise**. Un échantillon aléatoire des applications dans votre locataire Azure AD s’affiche. 

1. Dans le menu **Type d’Application**, sélectionnez **Toutes les Applications**, puis sélectionnez **Appliquer**.

1. Entrez le nom de l’application dans la zone de recherche, puis sélectionnez l’application dans les résultats.

1. Sous la section **Gérer**, sélectionnez **Authentification unique**. 

1. Sélectionnez **Lié**.

1. Entrez l’URL de connexion de l’application avec laquelle effectuer une liaison. Tapez l’URL, puis sélectionnez **Enregistrer**. 
 
1. Vous pouvez affecter des utilisateurs et des groupes à l’application, qui s’affiche alors dans le [Lanceur d’applications Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou le [Panneau d’accès Azure AD](end-user-experiences.md) pour ces utilisateurs.

1. Sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

- [Affecter des utilisateurs et des groupes à l’application](methods-for-assigning-users-and-groups.md)
- [Configurer le provisionnement automatique d’un compte utilisateur](configure-automatic-user-provisioning-portal.md)
