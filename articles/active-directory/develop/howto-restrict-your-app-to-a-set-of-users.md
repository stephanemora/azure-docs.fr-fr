---
title: Limiter l’application Azure AD à un ensemble d’utilisateurs | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment limiter l’accès à vos applications inscrites dans Azure AD à un ensemble d’utilisateurs sélectionné.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5ced85c19eb8999c839861eb421ebb9d73ea63a6
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2021
ms.locfileid: "112369354"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>Procédure : Limiter votre application Azure AD à un ensemble d’utilisateurs dans un locataire Azure AD

Les applications inscrites dans un locataire Azure Active Directory (Azure AD) sont, par défaut, disponibles pour tous les utilisateurs du locataire qui parviennent à s’authentifier.

De même, avec une application [mutualisée](howto-convert-app-to-be-multi-tenant.md), tous les utilisateurs dans le locataire Azure AD où cette application est approvisionnée sont en mesure d’accéder à cette application après avoir réussi à s’authentifier auprès de leur locataire respectif.

Les développeurs et les administrateurs de locataires ont souvent des exigences impliquant de limiter une application à un certain ensemble d’utilisateurs. Pour ce faire, les développeurs peuvent utiliser des modèles d’autorisation courants comme le contrôle d’accès en fonction du rôle Azure (Azure RBAC), mais cette approche nécessite une quantité de travail importante.

Les développeurs et administrateurs de locataires peuvent également limiter une application à un ensemble spécifique d’utilisateurs ou de groupes de sécurité dans le locataire grâce à la fonctionnalité intégrée d’Azure AD.

## <a name="supported-app-configurations"></a>Configurations d’application prises en charge

La possibilité de limiter une application à un ensemble spécifique d’utilisateurs ou de groupes de sécurité dans un locataire est compatible avec les types d’applications suivants :

- applications configurées pour l’authentification unique fédérée avec l’authentification basée sur SAML ;
- applications de proxy d’application qui utilisent la pré-authentification Azure AD ;
- applications créées directement sur la plateforme d’application Azure AD qui utilisent l’authentification OAuth 2.0/OpenID Connect après qu’un utilisateur ou administrateur a donné son consentement à cette application.

     > [!NOTE]
     > Cette fonctionnalité est disponible pour les applications professionnelles et API web/applications web uniquement. Les applications qui sont inscrites en tant qu’applications [natives](./quickstart-register-app.md) ne peuvent pas être limitées à un ensemble d’utilisateurs ou de groupes de sécurité dans le locataire.

## <a name="update-the-app-to-require-user-assignment"></a>Mettre à jour l'application pour exiger une affectation d'utilisateurs

Pour mettre à jour une application afin d'exiger une affectation d'utilisateurs, vous devez être le propriétaire de cette application sous Applications d'entreprise, ou disposer d'un des rôles d'annuaire suivants : **Administrateur général**, **Administrateur d'application** ou **Administrateur d'application cloud**.

1. Connectez-vous au <a href="https://portal.azure.com/" target="_blank">portail Azure</a>.
1. Si vous avez accès à plusieurs locataires, utilisez le filtre **Répertoire + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: dans le menu du haut pour sélectionner le locataire dans lequel vous voulez inscrire une application.
1. Recherchez et sélectionnez **Azure Active Directory**.
1. Sous **Gérer**, sélectionnez **Applications d’entreprise** > **Toutes les applications**.
1. Sélectionnez l'application que vous souhaitez configurer pour exiger une affectation. Utilisez les filtres en haut de la fenêtre pour rechercher une application spécifique.
1. Dans la page **Vue d’ensemble** de l’application, sous **Gérer**, sélectionnez **Propriétés**.
1. Recherchez le paramètre **Affectation utilisateur requise?** et définissez-le sur **Oui**. Lorsque cette option est définie sur **Oui**, les utilisateurs et les services qui tentent d'accéder à l'application ou aux services doivent d'abord y être affectés pour pouvoir se connecter ou obtenir un jeton d'accès.
1. Sélectionnez **Enregistrer**.

> [!NOTE]
> Lorsqu'une application exige une affectation, le consentement de l'utilisateur n'est pas autorisé pour cette application. Cela est vrai même si ce consentement aurait autrement été autorisé pour l'application en question. Veillez à [accorder le consentement administrateur à l'échelle du locataire](../manage-apps/grant-admin-consent.md) aux applications qui exigent une affectation. 

## <a name="assign-the-app-to-users-and-groups"></a>Affecter l'application à des utilisateurs et à des groupes

Une fois que vous avez configuré votre application de manière à activer une affectation d'utilisateurs, vous pouvez poursuivre et affecter l'application à des utilisateurs et à des groupes.

1. Sous **Gérer**, sélectionnez **Utilisateurs et groupes** > **Ajouter un utilisateur/groupe**.
1. Sélectionnez le sélecteur **Utilisateurs**. 

     Une liste des utilisateurs et des groupes de sécurité s’affiche, ainsi qu’une zone de texte pour rechercher et localiser un utilisateur ou un groupe spécifique. Cet écran vous permet de sélectionner plusieurs utilisateurs et groupes d’un coup.

1. Une fois que vous avez fini de sélectionner les utilisateurs et les groupes, cliquez sur **Sélectionner**.
1. (Facultatif) Si vous avez défini des rôles d'application dans votre application, vous pouvez utiliser l'option **Sélectionner un rôle** pour affecter le rôle d'application aux utilisateurs et groupes sélectionnés. 
1. Sélectionnez **Affecter** pour finaliser l'affectation de l'application aux utilisateurs et groupes. 
1. Vérifiez que les utilisateurs et les groupes ajoutés figurent dans la liste **Utilisateurs et groupes** mise à jour.

## <a name="more-information"></a>Informations complémentaires

- [Procédure : Ajouter des rôles d’application dans votre application](./howto-add-app-roles-in-azure-ad-apps.md)
- [Ajouter une autorisation à une application web ASP.NET Core à l'aide de rôles d'application et de revendications de rôles](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Utilisation de groupes de sécurité et de rôles d’Application dans vos applications (vidéo)](https://www.youtube.com/watch?v=LRoc-na27l0)
- [Azure Active Directory, désormais avec les revendications de groupe et les rôles d’application](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Manifeste d’application Azure Active Directory](./reference-app-manifest.md)
