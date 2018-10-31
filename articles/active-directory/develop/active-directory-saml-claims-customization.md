---
title: Personnaliser des revendications émises dans le jeton SAML pour les applications d’entreprise dans Azure AD | Microsoft Docs
description: Découvrez comment personnaliser les revendications émises dans le jeton SAML pour les applications d’entreprise dans Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2018
ms.author: celested
ms.reviewer: luleon, jeedes
ms.custom: aaddev
ms.openlocfilehash: 4e80f5cb85a53281da9ec50a02d089f46e97dfde
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466714"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Procédure : personnaliser des revendications émises dans le jeton SAML pour les applications d’entreprise

Actuellement, Azure Active Directory (Azure AD) prend en charge l’authentification unique avec la plupart des applications d’entreprise, y compris les applications pré-intégrées dans la galerie d’applications Azure AD et les applications personnalisées. Quand un utilisateur s’authentifie auprès d’une application par l’intermédiaire d’Azure AD en utilisant le protocole SAML 2.0, Azure AD envoie un jeton à l’application (via HTTP POST). Après quoi, l’application valide et utilise ce jeton pour connecter l’utilisateur au lieu de lui demander un nom d’utilisateur et un mot de passe. Ces jetons SAML contiennent des informations sur l’utilisateur appelées « revendications ».

Une *revendication* concerne ce qu’un fournisseur d’identité déclare sur un utilisateur dans le jeton qu’il émet sur cet utilisateur. Dans un [jeton SAML](http://en.wikipedia.org/wiki/SAML_2.0), ces données sont généralement contenues dans l’instruction d’attribut SAML. L’ID unique de l’utilisateur est généralement présent dans le SAML Subject, également appelé « identificateur de nom ».

Par défaut, Azure AD émet un jeton SAML à destination de votre application qui contient une revendication NameIdentifier, dont la valeur est le nom de l’utilisateur (ou nom d’utilisateur principal) dans Azure AD. Cette valeur peut identifier l’utilisateur de manière unique. Le jeton SAML contient également des revendications supplémentaires contenant l’adresse de messagerie de l’utilisateur, son prénom et son nom.

Pour afficher ou modifier les revendications émises dans le jeton SAML à destination de l’application, ouvrez l’application dans le portail Azure. Dans la section **Attributs utilisateur** de l’application, cochez la case **Afficher et modifier tous les autres attributs utilisateur**.

![Section Attributs utilisateur][1]

Vous pouvez être amené à modifier les revendications émises dans le jeton SAML pour les deux raisons suivantes :
* L’application a été écrite pour exiger un ensemble différent d’URI de revendication ou de valeurs de revendication.
* L’application a été déployée d’une manière qui nécessite que la revendication NameIdentifier soit différente du nom d’utilisateur (nom d’utilisateur principal) stocké dans Azure AD.

Vous pouvez modifier n’importe quelle valeur de revendication par défaut. Sélectionnez la ligne de la revendication dans le tableau d’attributs de jetons SAML. Cette opération ouvre la section **Modifier l’attribut**, où vous pouvez modifier le nom de la revendication, ainsi que la valeur et l’espace de noms associés.

![Modifier un attribut utilisateur][2]

Vous pouvez également supprimer les revendications (autres que NameIdentifier) à l’aide du menu contextuel qui s’ouvre lorsque vous cliquez sur l’icône **...**. Vous pouvez également ajouter de nouvelles revendications à l’aide du bouton **Ajouter un attribut**.

![Modifier un attribut utilisateur][3]

## <a name="editing-the-nameidentifier-claim"></a>Modification de la revendication NameIdentifier

Pour résoudre le problème dans lequel l’application a été déployée à l’aide d’un nom d’utilisateur différent, sélectionnez la liste déroulante **Identificateur d’utilisateur** dans la section **Attributs utilisateur**. Cette action donne accès à une boîte de dialogue qui contient différentes options :

![Modifier un attribut utilisateur][4]

### <a name="attributes"></a>Attributs

Sélectionnez la source souhaitée pour la revendication `NameIdentifier` (ou NameID). Vous pouvez sélectionner les options suivantes.

| NOM | Description |
|------|-------------|
| Email | Adresse e-mail de l’utilisateur |
| userprincipalName | Nom d’utilisateur principal (UPN) de l’utilisateur |
| onpremisessamaccount | Nom de compte SAM qui a été synchronisé à partir d’Azure AD en local |
| objectID | ObjectId de l’utilisateur dans Azure AD |
| EmployeeID | EmployeeID de l’utilisateur |
| Extensions d’annuaire | Extensions d’annuaire [ synchronisées à partir d’Active Directory local à l’aide d’Azure AD Connect Sync](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Attributs d’extension 1-15 | Attributs d’extension local utilisés pour étendre le schéma Azure AD |

### <a name="transformations"></a>Transformations

Vous pouvez également utiliser les fonctions spéciales de transformation de revendication.

| Fonction | Description |
|----------|-------------|
| **ExtractMailPrefix()** | Supprime le suffixe de domaine de l’adresse e-mail, du nom du compte SAM ou du nom d’utilisateur principal. Ainsi, seule la première partie du nom d’utilisateur transmis est extraite (par exemple, « joe_smith » au lieu de joe_smith@contoso.com). |
| **join()** | Joint un attribut avec un domaine vérifié. Si la valeur d’identificateur utilisateur sélectionné possède un domaine, extrait le nom d’utilisateur pour ajouter le domaine vérifié sélectionné. Par exemple, si vous sélectionnez l’adresse e-mail (joe_smith@contoso.com) comme valeur d’identificateur utilisateur et que vous sélectionnez contoso.onmicrosoft.com comme domaine vérifié, le résultat est joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Convertit les caractères de l’attribut sélectionné en minuscules. |
| **ToUpper()** | Convertit les caractères de l’attribut sélectionné en majuscules. |

## <a name="adding-claims"></a>Ajout de revendications

Au moment d’ajouter une revendication, vous pouvez spécifier le nom d’attribut (qui ne doit pas nécessairement correspondre de manière stricte à un modèle d’URI, conformément à la spécification SAML). Faites correspondre la valeur à n’importe quel attribut utilisateur stocké dans l’annuaire.

![Ajouter un attribut utilisateur][7]

Par exemple, vous devez envoyer le service auquel appartient l’utilisateur au sein de son entreprise en tant que revendication (par exemple, Ventes). Entrez le nom de la revendication qui est attendu par l’application, puis sélectionnez **user.department** comme valeur.

> [!NOTE]
> Si pour un utilisateur donné, aucune valeur n’est stockée pour un attribut sélectionné, la revendication n’est pas émise dans le jeton.

> [!TIP]
> **user.onpremisesecurityidentifier** et **user.onpremisesamaccountname** sont uniquement pris en charge lors de la synchronisation des données utilisateur issues de l’annuaire Active Directory local en utilisant [l’outil Azure AD Connect](../hybrid/whatis-hybrid-identity.md).

## <a name="restricted-claims"></a>Revendications restreintes

Il existe certaines revendications restreintes dans SAML. Si vous ajoutez ces revendications, Azure AD ne les enverra pas. Voici ces revendications SAML restreintes :

    | Type de revendication (URI) |
    | ------------------- |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expired |
    | http://schemas.microsoft.com/identity/claims/accesstoken |
    | http://schemas.microsoft.com/identity/claims/openid2_id |
    | http://schemas.microsoft.com/identity/claims/identityprovider |
    | http://schemas.microsoft.com/identity/claims/objectidentifier |
    | http://schemas.microsoft.com/identity/claims/puid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier[MR1] |
    | http://schemas.microsoft.com/identity/claims/tenantid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod |
    | http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groups |
    | http://schemas.microsoft.com/claims/groups.link |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/role |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/wids |
    | http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant |
    | http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown |
    | http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged |
    | http://schemas.microsoft.com/2014/03/psso |
    | http://schemas.microsoft.com/claims/authnmethodsreferences |
    | http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier |
    | http://schemas.microsoft.com/identity/claims/scope |

## <a name="next-steps"></a>Étapes suivantes

* [Gestion des applications dans Azure AD](../manage-apps/what-is-application-management.md)
* [Configurer l’authentification unique pour les applications ne faisant pas partie de la galerie d’applications Azure AD](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Résoudre les erreurs d’authentification unique basée sur SAML](howto-v1-debug-saml-sso-issues.md)

<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/user-attribute-section.png
[2]: ./media/active-directory-saml-claims-customization/edit-claim-name-value.png
[3]: ./media/active-directory-saml-claims-customization/delete-claim.png
[4]: ./media/active-directory-saml-claims-customization/user-identifier.png
[5]: ./media/active-directory-saml-claims-customization/extractemailprefix-function.png
[6]: ./media/active-directory-saml-claims-customization/join-function.png
[7]: ./media/active-directory-saml-claims-customization/add-attribute.png
