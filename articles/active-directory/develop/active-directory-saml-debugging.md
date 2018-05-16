---
title: Débogage d’une authentification unique basée sur SAML aux applications dans Azure Active Directory | Microsoft Docs
description: 'Découvrez comment déboguer une authentification unique basée sur SAML aux applications dans Azure Active Directory  '
services: active-directory
author: CelesteDG
documentationcenter: na
manager: mtillman
ms.assetid: edbe492b-1050-4fca-a48a-d1fa97d47815
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: dastrock; smalser
ms.openlocfilehash: 1a33b5ab9e26ed497e3be2d430f66ef41402733d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Débogage d’une authentification unique basée sur SAML aux applications dans Azure Active Directory

Lors du débogage d’une intégration d’application basée sur SAML, il est généralement conseillé d’utiliser un outil tel que [Fiddler](http://www.telerik.com/fiddler) pour afficher la requête SAML et la réponse SAML qui contiennent le jeton SAML émis pour une application. 

![Fiddler][1]

Souvent, les problèmes sont liés à une configuration incorrecte sur Azure Active Directory ou côté application. Selon l’endroit où s’affiche l’erreur, vous devez examiner la requête ou la réponse SAML :

- L’erreur s’affiche sur la page de connexion de mon entreprise [Liens vers la section]
- L’erreur s’affiche sur la page de l’application après la connexion [Liens vers la section]

## <a name="i-see-an-error-in-my-company-sign-in-page"></a>L’erreur s’affiche sur la page de connexion de mon entreprise.

Vous pouvez trouver un mappage un à un entre le code d’erreur et les étapes de résolution dans l’article [Problèmes de connexion à une application de la galerie configurée pour l’authentification unique fédérée](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML).

Si une erreur s’affiche sur la page de connexion de votre entreprise, vous avez besoin du message d’erreur et de la requête SAML pour procéder au débogage.

### <a name="how-can-i-get-the-error--message"></a>Comment puis-je obtenir le message d’erreur ?

Pour obtenir le message d’erreur, observez le coin inférieur droit de la page. Vous pouvez y voir une erreur qui comprend :

- Un ID de corrélation
- Un timestamp
- Un message

![Error][2] 

 
L’ID de corrélation et le timestamp forment un identificateur unique que vous pouvez utiliser pour rechercher les journaux de serveur principal associés à l’échec de connexion. Ces valeurs sont importantes lorsque vous envoyez une demande de support à Microsoft, car elles permettent aux ingénieurs de résoudre plus rapidement votre problème.

Le message est la cause racine du problème de connexion. 


### <a name="how-can-i-review-the-saml-request"></a>Comment puis-je examiner la requête SAML ?

En général, la requête SAML envoyée par l’application à Azure Active Directory est la dernière réponse HTTP 200 de [https://login.microsoftonline.com](https://login.microsoftonline.com).
 
Avec Fiddler, vous pouvez afficher la requête SAML en sélectionnant cette ligne, puis l’onglet **Inspecteurs > WebForms** dans le panneau droit. Faites un clic droit sur la valeur **SAMLRequest** et sélectionnez **Send to TextWizard** (Envoyer à TextWizard). Puis sélectionnez **From Base64** à partir du menu **Transform** pour décoder le jeton et afficher son contenu. 

Vous pouvez également copier la valeur de SAMLRequest et utiliser un autre décodeur Base64.

    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    Destination=https://login.microsoftonline.com/<Tenant-ID>/saml2
    AssertionConsumerServiceURL="https://contoso.applicationname.com/acs"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>

Avec la requête SAML décodée, passez en revue les éléments suivants :

1. La valeur **Destination** dans la requête SAML correspond à l’URL du service d’authentification unique SAML obtenue auprès d’Azure Active Directory.
 
2. La valeur **Émetteur** dans la requête SAML est identique à la valeur **Identificateur** configurée pour l’application dans Azure Active Directory. Azure AD utilise l’émetteur pour rechercher une application dans votre répertoire.

3. **AssertionConsumerServiceURL** correspond à l’emplacement où l’application doit recevoir le jeton SAML d’Azure Active Directory. Vous pouvez configurer cette valeur dans Azure Active Directory, mais elle n’est pas obligatoire si elle fait partie de la requête SAML.


## <a name="i-see-an-error-on-the-applications-page-after-signing-in"></a>L’erreur s’affiche sur la page de l’application après la connexion

Dans ce scénario, l’application n’accepte pas la réponse émise par Azure AD. Il est primordial que vous vérifiiez la réponse d’Azure AD qui contient le jeton SAML avec le fournisseur de l’application pour savoir quels sont les éléments manquants ou incorrects. 

### <a name="how-can-i-get-and-review-the-saml-response"></a>Comment puis-je obtenir et examiner la réponse SAML ?

La réponse d’Azure AD qui contient le jeton SAML est généralement celle qui est générée après une redirection HTTP 302 depuis https://login.microsoftonline.com et qui est envoyée à **l’URL de réponse** configurée de l’application. 

Vous pouvez afficher le jeton SAML en sélectionnant cette ligne, puis l’onglet **Inspecteurs > WebForms** dans le volet droit. À partir de là, cliquez sur la valeur **SAMLResponse** et sélectionnez **Send to TextWizard**. Puis, sélectionnez **From Base64** (À partir de Base64) à partir du menu **Transformer** pour décoder le jeton et afficher son contenu. 

Vous pouvez également copier la valeur de **SAMLRequest** et utiliser un autre décodeur Base64.

Consultez l’article [Problèmes de connexion à une application de la galerie configurée pour l’authentification unique fédérée](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML) pour plus d’informations sur les éléments manquants ou incorrects de la réponse SAML.

Pour plus d’informations sur comment examiner la réponse SAML, consultez l’article [Protocole SAML d’authentification unique](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML#response).


## <a name="related-articles"></a>Articles connexes
* [Index d’articles pour la gestion des applications dans Azure Active Directory](../active-directory-apps-index.md)
* [Configuration de l'authentification unique pour les applications ne faisant pas partie de la galerie d'applications Azure Active Directory.](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [Comment personnaliser les revendications émises dans le jeton SAML pour les applications pré-intégrées](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ../media/active-directory-saml-debugging/fiddler.png
[2]: ../media/active-directory-saml-debugging/error.png
