---
title: Personnaliser les revendications de jeton SAML pour les applications d’entreprise dans Azure AD | Microsoft Docs
description: Découvrez comment personnaliser les revendications émises dans le jeton SAML pour les applications d’entreprise dans Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c0625a233b3b4a949feff2e289361a26fc8dc5a
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835344"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Activation Personnaliser des revendications émises dans le jeton SAML pour les applications d’entreprise

Actuellement, Azure Active Directory (Azure AD) prend en charge l’authentification unique avec la plupart des applications d’entreprise, y compris les applications pré-intégrées dans la galerie d’applications Azure AD et les applications personnalisées. Quand un utilisateur s’authentifie auprès d’une application par l’intermédiaire d’Azure AD en utilisant le protocole SAML 2.0, Azure AD envoie un jeton à l’application (via HTTP POST). Après quoi, l’application valide et utilise ce jeton pour connecter l’utilisateur au lieu de lui demander un nom d’utilisateur et un mot de passe. Ces jetons SAML contiennent des informations sur l’utilisateur appelées *revendications*.

Une *revendication* concerne ce qu’un fournisseur d’identité déclare sur un utilisateur dans le jeton qu’il émet sur cet utilisateur. Dans un [jeton SAML](https://en.wikipedia.org/wiki/SAML_2.0), ces données sont généralement contenues dans l’instruction d’attribut SAML. L’ID unique de l’utilisateur est généralement présent dans le SAML Subject, également appelé « identificateur de nom ».

Par défaut, Azure AD émet un jeton SAML à destination de votre application qui contient une revendication `NameIdentifier`, dont la valeur est le nom de l’utilisateur (ou nom d’utilisateur principal) dans Azure AD, qui peut identifier l’utilisateur de façon unique. Le jeton SAML contient également des revendications supplémentaires contenant l’adresse de messagerie de l’utilisateur, son prénom et son nom.

Pour afficher ou modifier les revendications émises dans le jeton SAML à destination de l’application, ouvrez l’application dans le portail Azure. Ensuite, ouvrez la section **Attributs et revendications de l’utilisateur**.

![Ouvrez la section Attributs et revendications de l’utilisateur dans le Portail Microsoft Azure](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Vous pouvez être amené à modifier les revendications émises dans le jeton SAML pour les deux raisons suivantes :

* La revendication `NameIdentifier` ou NameID doit être différente du nom d’utilisateur (ou du nom d’utilisateur principal) stocké dans Azure AD pour l’application.
* L’application a été écrite pour exiger un ensemble différent d’URI de revendication ou de valeurs de revendication.

## <a name="editing-nameid"></a>Modification de NameID

Pour modifier le NameID (valeur d’identificateur du nom) :

1. Ouvrez la page **Valeur de nom d’identificateur**.
1. Sélectionnez l’attribut ou la transformation à appliquer à l’attribut. Si vous le souhaitez, vous pouvez spécifier le format de la revendication NameID.

   ![Modifier la valeur NameID (identificateur du nom)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>Format NameID

Si la requête SAML contient l’élément NameIDPolicy dans un format spécifique, Azure AD respecte le format de la requête.

Si la requête SAML ne contient aucun élément pour NameIDPolicy, alors Azure AD émet la revendication NameID au format que vous spécifiez. Si aucun format n’est spécifié, Azure AD utilise le format source par défaut associé à la source de la revendication sélectionnée.

Dans le menu déroulant **Choisir le format du nom de l’identificateur**, vous pouvez sélectionner l’une des options suivantes.

| Format NameID | Description |
|---------------|-------------|
| **Par défaut** | Par défaut, Azure AD utilise le format de la source. |
| **Persistent** | Azure AD utilisera Persistent comme format pour NameID. |
| **EmailAddress** | Azure AD utilisera EmailAddress comme format pour NameID. |
| **Unspecified** | Azure AD utilisera Unspecified comme format pour NameID. |
| **Transient** | Azure AD utilisera Transient comme format pour NameID. |

Pour en savoir plus sur l’attribut NameIDPolicy, consultez [Protocole SAML d’authentification unique](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Attributs

Sélectionnez la source souhaitée pour la revendication `NameIdentifier` (ou NameID). Vous pouvez sélectionner les options suivantes.

| Nom | Description |
|------|-------------|
| Email | Adresse e-mail de l’utilisateur |
| userprincipalName | Nom d’utilisateur principal (UPN) de l’utilisateur |
| onpremisessamaccount | Nom de compte SAM qui a été synchronisé à partir d’Azure AD en local |
| objectid | ID d’objet de l’utilisateur dans Azure AD |
| employeeid | ID d’employé de l’utilisateur |
| Extensions d’annuaire | Extensions d’annuaire [ synchronisées à partir d’Active Directory local à l’aide d’Azure AD Connect Sync](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Attributs d’extension 1-15 | Attributs d’extension local utilisés pour étendre le schéma Azure AD |

Pour plus d’informations, consultez [Tableau 3 : Validez les valeurs d’ID par source](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

### <a name="special-claims---transformations"></a>Transformations de revendications générales

Vous pouvez également utiliser les fonctions de transformation de revendication.

| Fonction | Description |
|----------|-------------|
| **ExtractMailPrefix()** | Supprime le suffixe de domaine de l’adresse e-mail ou du nom d’utilisateur principal. Ainsi, seule la première partie du nom d’utilisateur transmis est extraite (par exemple, « joe_smith » au lieu de joe_smith@contoso.com). |
| **Join()** | Joint un attribut avec un domaine vérifié. Si la valeur d’identificateur utilisateur sélectionné possède un domaine, extrait le nom d’utilisateur pour ajouter le domaine vérifié sélectionné. Par exemple, si vous sélectionnez l’adresse e-mail (joe_smith@contoso.com) comme valeur d’identificateur utilisateur et que vous sélectionnez contoso.onmicrosoft.com comme domaine vérifié, le résultat est joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Convertit les caractères de l’attribut sélectionné en minuscules. |
| **ToUpper()** | Convertit les caractères de l’attribut sélectionné en majuscules. |

## <a name="adding-application-specific-claims"></a>Ajout de revendications spécifiques à l’application

Pour ajouter des revendications spécifiques à l’application :

1. Dans **Attributs utilisateur et revendications**, sélectionnez **Ajouter une revendication** pour ouvrir la page **Gérer les revendications des utilisateurs**.
1. Entrez le **nom** des revendications. La valeur n’a pas besoin de suivre strictement un modèle URI, selon la spécification SAML. Si vous avez besoin d’un modèle d’URI, vous pouvez l’indiquer dans le champ **Namespace**.
1. Sélectionnez la **Source** où la revendication va récupérer sa valeur. Vous pouvez sélectionner un attribut utilisateur dans le menu déroulant d’attribut de la source ou appliquer une transformation à l’attribut utilisateur avant de l’émettre en tant que réclamation.

### <a name="application-specific-claims---transformations"></a>Transformations de revendications spécifiques à l’application

Vous pouvez également utiliser les fonctions de transformation de revendication.

| Fonction | Description |
|----------|-------------|
| **ExtractMailPrefix()** | Supprime le suffixe de domaine de l’adresse e-mail ou du nom d’utilisateur principal. Ainsi, seule la première partie du nom d’utilisateur transmis est extraite (par exemple, « joe_smith » au lieu de joe_smith@contoso.com). |
| **Join()** | Crée une nouvelle valeur en joignant deux attributs. Si vous le souhaitez, vous pouvez utiliser un séparateur entre les deux attributs. |
| **ToLower()** | Convertit les caractères de l’attribut sélectionné en minuscules. |
| **ToUpper()** | Convertit les caractères de l’attribut sélectionné en majuscules. |
| **Contains()** | Génère un attribut ou une constante si l’entrée correspond à la valeur spécifiée. Sinon, vous pouvez spécifier une autre sortie s’il n’existe aucune correspondance.<br/>Par exemple, vous pouvez émettre une revendication où la valeur est l’adresse e-mail utilisateur si elle contient le domaine «@contoso.com». Dans ce cas, nous vous conseillons de générer le nom d’utilisateur principal. Pour ce faire, configurez les valeurs suivantes :<br/>*Paramètre 1 (entrée)*  : user.email<br/>*Valeur* : "@contoso.com"<br/>Paramètre 2 (sortie) : user.email<br/>Paramètre 3 (sortie s’il n’existe aucune correspondance) : user.userprincipalname |
| **EndWith()** | Génère un attribut ou une constante si l’entrée se termine par la valeur spécifiée. Sinon, vous pouvez spécifier une autre sortie s’il n’existe aucune correspondance.<br/>Par exemple, vous pouvez émettre une revendication dont la valeur est l’ID d’employé de l’utilisateur, si la valeur employeeid se termine par « 000 ». Dans ce cas, nous vous recommandons d’obtenir un attribut d’extension en sortie. Pour ce faire, configurez les valeurs suivantes :<br/>*Paramètre 1 (entrée)*  : user.employeeid<br/>*Valeur* : "000"<br/>Paramètre 2 (sortie) : user.employeeid<br/>Paramètre 3 (sortie, s’il n’existe aucune correspondance) : user.extensionattribute1 |
| **StartWith()** | Génère un attribut ou une constante si l’entrée commence par la valeur spécifiée. Sinon, vous pouvez spécifier une autre sortie s’il n’existe aucune correspondance.<br/>Par exemple, vous pouvez émettre une revendication dont la valeur est l’ID d’employé de l’utilisateur, si la valeur de Pays/région commence par « US ». Dans ce cas, nous vous recommandons d’obtenir un attribut d’extension en sortie. Pour ce faire, configurez les valeurs suivantes :<br/>*Paramètre 1 (entrée)*  : user.country<br/>*Valeur* : "US"<br/>Paramètre 2 (sortie) : user.employeeid<br/>Paramètre 3 (sortie, s’il n’existe aucune correspondance) : user.extensionattribute1 |
| **Extract() - After matching** | Retourne la sous-chaîne après qu’elle ait atteint la valeur spécifiée.<br/>Par exemple, si la valeur de l’entrée est « Finance_BSimon », la valeur correspondante est « Finance_ » et la sortie de la revendication est « BSimon ». |
| **Extract() - Before matching** | Retourne la sous-chaîne jusqu’à ce qu’elle corresponde à la valeur spécifiée.<br/>Par exemple, si la valeur de l’entrée est « BSimon_US », la valeur correspondante est « _US » et la sortie de la revendication est « BSimon ». |
| **Extract() - Between matching** | Retourne la sous-chaîne jusqu’à ce qu’elle corresponde à la valeur spécifiée.<br/>Par exemple, si la valeur de l’entrée est « Finance_BSimon_US », la première valeur correspondante est « Finance_ », la seconde « _US » et la sortie de la revendication est « BSimon ». |
| **ExtractAlpha() - Prefix** | Retourne la partie alphabétique du préfixe de la chaîne.<br/>Par exemple, si la valeur de l’entrée est « BSimon_123 », elle retourne « BSimon ». |
| **ExtractAlpha() - Suffix** | Retourne la partie alphabétique du suffixe de la chaîne.<br/>Par exemple, si la valeur de l’entrée est « 123_Simon », elle retourne « Simon ». |
| **ExtractNumeric() - Prefix** | Retourne la partie numérique du préfixe de la chaîne.<br/>Par exemple, si la valeur de l’entrée est « 123_BSimon », elle retourne « 123 ». |
| **ExtractNumeric() - Suffix** | Retourne la partie numérique du suffixe de la chaîne.<br/>Par exemple, si la valeur de l’entrée est « BSimon_123 », elle retourne « 123 ». |
| **IfEmpty()** | Génère un attribut ou une constante en sortie si l’entrée est Null ou vide.<br/>Par exemple, si vous souhaitez générer un attribut stocké dans un extensionattribute si la valeur employeeid pour un utilisateur donné est vide. Pour ce faire, configurez les valeurs suivantes :<br/>Paramètre 1 (entrée) : user.employeeid<br/>Paramètre 2 (sortie) : user.extensionattribute1<br/>Paramètre 3 (sortie s’il n’existe aucune correspondance) : user.employeeid |
| **IfNotEmpty()** | Génère un attribut ou une constante en sortie si l’entrée n’est pas Null ou vide.<br/>Par exemple, si vous souhaitez générer un attribut stocké dans un extensionattribute si la valeur employeeid pour un utilisateur donné n’est pas vide. Pour ce faire, configurez les valeurs suivantes :<br/>Paramètre 1 (entrée) : user.employeeid<br/>Paramètre 2 (sortie) : user.extensionattribute1 |

Si vous avez besoin de transformations supplémentaires, soumettez votre idée dans le [Forum de commentaires Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) sous la catégorie *Application SaaS*.

## <a name="next-steps"></a>Étapes suivantes

* [Gestion des applications dans Azure AD](../manage-apps/what-is-application-management.md)
* [Configurer l’authentification unique pour les applications ne faisant pas partie de la galerie d’applications Azure AD](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Résoudre les erreurs d’authentification unique basée sur SAML](howto-v1-debug-saml-sso-issues.md)
