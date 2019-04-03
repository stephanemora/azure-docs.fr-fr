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
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: celested
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6fe74852824c10d24729f785e5e33a17b793161
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878568"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Activation Personnaliser des revendications émises dans le jeton SAML pour les applications d’entreprise

Aujourd'hui, Azure Active Directory (Azure AD) prend en charge l’authentification-unique (SSO) avec la plupart des applications d’entreprise, y compris les deux applications pré-intégrées dans la galerie d’applications Azure AD, ainsi que les applications personnalisées. Quand un utilisateur s’authentifie auprès d’une application par l’intermédiaire d’Azure AD en utilisant le protocole SAML 2.0, Azure AD envoie un jeton à l’application (via HTTP POST). Après quoi, l’application valide et utilise ce jeton pour connecter l’utilisateur au lieu de lui demander un nom d’utilisateur et un mot de passe. Ces jetons SAML contiennent des informations sur l’utilisateur appelées *revendications*.

Une *revendication* concerne ce qu’un fournisseur d’identité déclare sur un utilisateur dans le jeton qu’il émet sur cet utilisateur. Dans un [jeton SAML](https://en.wikipedia.org/wiki/SAML_2.0), ces données sont généralement contenues dans l’instruction d’attribut SAML. L’ID unique de l’utilisateur est généralement présent dans le SAML Subject, également appelé « identificateur de nom ».

Par défaut, Azure AD émet un jeton SAML à votre application qui contienne un `NameIdentifier` revendication avec la valeur de l’utilisateur (également connu sous le nom d’utilisateur principal) dans Azure AD, ce qui peut identifier l’utilisateur. Le jeton SAML contient également des revendications supplémentaires contenant l’adresse de messagerie de l’utilisateur, son prénom et son nom.

Pour afficher ou modifier les revendications émises dans le jeton SAML à destination de l’application, ouvrez l’application dans le portail Azure. Ouvrez ensuite le **attributs utilisateur & revendications** section.

![Section attributs utilisateur et revendications](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Vous pouvez être amené à modifier les revendications émises dans le jeton SAML pour les deux raisons suivantes :

* L’application requiert le `NameIdentifier` ou NameID prétendent être quelque chose autre que le nom d’utilisateur (ou le nom d’utilisateur principal) stocké dans Azure AD.
* L’application a été écrite pour exiger un ensemble différent d’URI de revendication ou de valeurs de revendication.

## <a name="editing-nameid"></a>Modification NameID

Pour modifier le NameID (valeur d’identificateur de nom) :

1. Ouvrez le **nom de valeur d’identificateur** page.
1. Sélectionnez l’attribut ou la transformation à appliquer à l’attribut. Si vous le souhaitez, vous pouvez spécifier le format d’il revendication NameID avoir.

   ![Modifier la valeur NameID (identificateur de nom)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>Format NameID

Si la demande SAML contient l’élément NameIDPolicy dans un format spécifique, Azure AD respecte le format dans la demande.

Si la demande SAML ne contient aucun élément pour NameIDPolicy, Azure AD émet la revendication NameID au format que vous spécifiez. Si aucun format n’est spécifié, Azure AD doit utiliser le format de source par défaut associé à la source de revendication sélectionnée.

À partir de la **format d’identificateur de nom de choisir** liste déroulante, vous pouvez sélectionner une des options suivantes.

| Format NameID | Description |
|---------------|-------------|
| **Default** | Azure AD utilise le format de la source par défaut. |
| **Persistante** | Azure AD utilisera persistant en tant que le format NameID. |
| **EmailAddress** | Azure AD utilisera EmailAddress en tant que le format NameID. |
| **Non spécifié** | Azure AD utilisera non spécifié en tant que le format NameID. |
| **Temporaire** | Azure AD utilisera temporaire en tant que le format NameID. |

Pour en savoir plus sur l’attribut NameIDPolicy, consultez [protocole SAML d’authentification unique](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Attributs

Sélectionnez la source souhaitée pour la revendication `NameIdentifier` (ou NameID). Vous pouvez sélectionner les options suivantes.

| Nom | Description |
|------|-------------|
| Email | Adresse e-mail de l’utilisateur |
| userprincipalName | Nom d’utilisateur principal (UPN) de l’utilisateur |
| onpremisessamaccount | Nom de compte SAM qui a été synchronisé à partir d’Azure AD en local |
| objectid | ObjectID de l’utilisateur dans Azure AD |
| employeeid | EmployeeID de l’utilisateur |
| Extensions d’annuaire | Extensions d’annuaire [ synchronisées à partir d’Active Directory local à l’aide d’Azure AD Connect Sync](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Attributs d’extension 1-15 | Attributs d’extension local utilisés pour étendre le schéma Azure AD |

Pour plus d’informations, consultez [Table 3 : Les valeurs ID valides par source](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

### <a name="special-claims---transformations"></a>Revendications spéciales - Transformations

Vous pouvez également utiliser les fonctions de transformations de revendications.

| Fonction | Description |
|----------|-------------|
| **ExtractMailPrefix()** | Supprime le suffixe de domaine de l’adresse de messagerie ou le nom d’utilisateur principal. Ainsi, seule la première partie du nom d’utilisateur transmis est extraite (par exemple, « joe_smith » au lieu de joe_smith@contoso.com). |
| **Join()** | Joint un attribut avec un domaine vérifié. Si la valeur d’identificateur utilisateur sélectionné possède un domaine, extrait le nom d’utilisateur pour ajouter le domaine vérifié sélectionné. Par exemple, si vous sélectionnez l’adresse e-mail (joe_smith@contoso.com) comme valeur d’identificateur utilisateur et que vous sélectionnez contoso.onmicrosoft.com comme domaine vérifié, le résultat est joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Convertit les caractères de l’attribut sélectionné en minuscules. |
| **ToUpper()** | Convertit les caractères de l’attribut sélectionné en majuscules. |

## <a name="adding-application-specific-claims"></a>Ajout de revendications spécifiques à l’application

Pour ajouter des revendications spécifiques à l’application :

1. Dans **attributs utilisateur & revendications**, sélectionnez **ajouter une nouvelle revendication** pour ouvrir le **les revendications d’utilisateur de gestion** page.
1. Entrez le **nom** des revendications. La valeur strictement n’a pas besoin de suivre un modèle d’URI, par la spécification SAML. Si vous avez besoin d’un modèle d’URI, vous pouvez mettre dans le **Namespace** champ.
1. Sélectionnez le **Source** où la revendication va récupérer sa valeur. Vous pouvez sélectionner un attribut utilisateur dans la liste déroulante attribut de source ou appliquer une transformation à l’attribut de l’utilisateur avant l’émission de l’en tant que revendication.

### <a name="application-specific-claims---transformations"></a>Revendications spécifiques à l’application - Transformations

Vous pouvez également utiliser les fonctions de transformations de revendications.

| Fonction | Description |
|----------|-------------|
| **ExtractMailPrefix()** | Supprime le suffixe de domaine de l’adresse de messagerie ou le nom d’utilisateur principal. Ainsi, seule la première partie du nom d’utilisateur transmis est extraite (par exemple, « joe_smith » au lieu de joe_smith@contoso.com). |
| **Join()** | Crée une nouvelle valeur en joignant les deux attributs. Si vous le souhaitez, vous pouvez utiliser un séparateur entre les deux attributs. |
| **ToLower()** | Convertit les caractères de l’attribut sélectionné en minuscules. |
| **ToUpper()** | Convertit les caractères de l’attribut sélectionné en majuscules. |
| **Contains()** | Génère un attribut ou une constante si l’entrée correspond à la valeur spécifiée. Sinon, vous pouvez spécifier une autre sortie s’il n’existe aucune correspondance.<br/>Par exemple, si vous souhaitez émettre une revendication où la valeur est l’adresse e-mail utilisateur si elle contient le domaine «@contoso.com», sinon vous souhaitez générer le nom d’utilisateur principal. Pour ce faire, vous configurez les valeurs suivantes :<br/>*Paramètre 1(input)*: user.email<br/>*Valeur*: «@contoso.com»<br/>Le paramètre 2 (sortie) : user.email<br/>Paramètre 3 (sortie s’il n’existe aucune correspondance) : user.userprincipalname |
| **EndWith()** | Génère un attribut ou une constante si l’entrée se termine par la valeur spécifiée. Sinon, vous pouvez spécifier une autre sortie s’il n’existe aucune correspondance.<br/>Par exemple, si vous souhaitez émettre une revendication dont la valeur est employeeid de l’utilisateur si la valeur employeeid se termine par « 000 », sinon vous souhaitez un attribut d’extension de sortie. Pour ce faire, vous configurez les valeurs suivantes :<br/>*Paramètre 1(input)*: user.employeeid<br/>*Valeur* : "000"<br/>Le paramètre 2 (sortie) : user.employeeid<br/>Paramètre 3 (sortie s’il n’existe aucune correspondance) : user.extensionattribute1 |
| **StartWith()** | Génère un attribut ou une constante si l’entrée commence par la valeur spécifiée. Sinon, vous pouvez spécifier une autre sortie s’il n’existe aucune correspondance.<br/>Par exemple, si vous souhaitez émettre une revendication dont la valeur est employeeid de l’utilisateur si le pays commence par « US », sinon vous souhaitez un attribut d’extension de sortie. Pour ce faire, vous configurez les valeurs suivantes :<br/>*Paramètre 1(input)*: user.country<br/>*Valeur* : « US »<br/>Le paramètre 2 (sortie) : user.employeeid<br/>Paramètre 3 (sortie s’il n’existe aucune correspondance) : user.extensionattribute1 |
| **Extract() - après la mise en correspondance** | Retourne la sous-chaîne après elle correspond à la valeur spécifiée.<br/>Par exemple, si la valeur de l’entrée est « Finance_BSimon », la valeur correspondante est « Finance_ », puis les sortie de la revendication sont « BSimon ». |
| **Extract() - avant la mise en correspondance** | Retourne la sous-chaîne jusqu'à ce qu’il correspond à la valeur spécifiée.<br/>Par exemple, si la valeur de l’entrée est « BSimon_US », la valeur correspondante est « _US », puis les sortie de la revendication sont « BSimon ». |
| **Extract() - entre le correspondantes** | Retourne la sous-chaîne jusqu'à ce qu’il correspond à la valeur spécifiée.<br/>Par exemple, si la valeur de l’entrée est « Finance_BSimon_US », la première valeur correspondante est « Finance_ », la deuxième valeur de correspondance est « _US », puis les sortie de la revendication sont « BSimon ». |
| **ExtractAlpha() - préfixe** | Retourne la partie alphabétique de préfixe de la chaîne.<br/>Par exemple, si la valeur de l’entrée est « BSimon_123 », elle retourne « BSimon ». |
| **ExtractAlpha() - suffixe** | Retourne la partie alphabétique suffixe de la chaîne.<br/>Par exemple, si la valeur de l’entrée est « 123_Simon », elle retourne « BSimon ». |
| **ExtractNumeric() - Prefix** | Retourne la partie numérique de préfixe de la chaîne.<br/>Par exemple, si la valeur de l’entrée est « 123_BSimon », elle retourne « 123 ». |
| **ExtractNumeric() - suffixe** | Retourne la partie numérique de suffixe de la chaîne.<br/>Par exemple, si la valeur de l’entrée est « BSimon_123 », elle retourne « 123 ». |
| **IfEmpty()** | Si l’entrée est null ou vide, génère un attribut ou une constante.<br/>Par exemple, si vous souhaitez générer un attribut stocké dans un extensionattribute si la valeur employeeid pour un utilisateur donné est vide. Pour ce faire, vous configurez les valeurs suivantes :<br/>Paramètre 1(input) : user.employeeid<br/>Le paramètre 2 (sortie) : user.extensionattribute1<br/>Paramètre 3 (sortie s’il n’existe aucune correspondance) : user.employeeid |
| **IfNotEmpty()** | Si l’entrée n’est pas null ou vide, génère un attribut ou une constante.<br/>Par exemple, si vous souhaitez générer un attribut stocké dans un extensionattribute si la valeur employeeid pour un utilisateur donné n’est pas vide. Pour ce faire, vous configurez les valeurs suivantes :<br/>Paramètre 1(input) : user.employeeid<br/>Le paramètre 2 (sortie) : user.extensionattribute1 |

Si vous avez besoin des transformations supplémentaires, soumettez votre idée dans le [forum de commentaires dans Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) sous le *application SaaS* catégorie.

## <a name="next-steps"></a>Étapes suivantes

* [Gestion des applications dans Azure AD](../manage-apps/what-is-application-management.md)
* [Configurer l’authentification unique sur des applications qui ne sont pas dans la galerie d’applications Azure AD](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Résoudre les problèmes basée sur SAML SSO](howto-v1-debug-saml-sso-issues.md)
