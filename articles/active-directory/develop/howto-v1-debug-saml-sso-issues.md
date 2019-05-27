---
title: Déboguer l’authentification unique SAML – Azure Active Directory | Microsoft Docs
description: Déboguez l’authentification unique SAML aux applications dans Azure Active Directory.
services: active-directory
author: rwike77
documentationcenter: na
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: luleon, hirsin, smalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a06aeee1a7d9635d625edbeb2ff57a514a33a84
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962720"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Déboguer l’authentification unique SAML aux applications dans Azure Active Directory

Découvrez comment rechercher et corriger les problèmes [d’authentification unique](../manage-apps/what-is-single-sign-on.md) pour les applications qui prennent en charge [SAML (Security Assertion Markup Language) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language) dans Azure Active Directory (Azure AD). 

## <a name="before-you-begin"></a>Avant de commencer

Nous vous recommandons d’installer [l’extension de connexion sécurisée à Mes applications](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension). Cette extension de navigateur rend plus facile rassembler la demande SAML et les informations de réponse SAML dont vous avez besoin de résoudre les problèmes avec l’authentification unique. Au cas où vous ne parviendriez pas à l’installer, cet article explique comment résoudre les problèmes avec et sans l’extension.

Pour télécharger et installer l’extension de connexion sécurisée à Mes applications, suivez l’un des liens ci-dessous.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)


## <a name="test-saml-based-single-sign-on"></a>Tester l’authentification unique SAML

Pour tester basée sur SAML SSO entre Azure AD et une application cible :

1. Connectez-vous au [Portail Azure](https://portal.azure.com) en tant qu’administrateur général (ou autre type d’administrateur autorisé à gérer les applications).
1. Dans le panneau de gauche, sélectionnez **Azure Active Directory**, puis sélectionnez **applications d’entreprise**. 
1. Dans la liste des applications d’entreprise, sélectionnez l’application pour laquelle vous souhaitez tester l’authentification unique, puis, sous les options sur la gauche, sélectionnez **Single sign-on**.
1. Pour ouvrir la basée sur SAML unique test expérience d’authentification, accédez à **tester l’authentification unique sur** (étape 5). Si le **Test** bouton est grisé, vous devez renseigner et enregistrez les attributs requis tout d’abord le **base Configuration SAML** section.
1. Dans le panneau **Tester l’authentification unique**, utilisez vos informations d’identification d’entreprise pour vous connecter à l’application cible. Vous pouvez vous connecter sous l’identité de l’utilisateur actuel ou sous celle d’un autre utilisateur. Dans le deuxième cas, une invite vous demande de vous authentifier.

    ![Page de test SAML](./media/howto-v1-debug-saml-sso-issues/test-single-sign-on.png)


Si la connexion est établie, le test a réussi. Dans ce cas, Azure AD émis un jeton de réponse SAML, que l’application a utilisé pour vous connecter.

Si une erreur s’affiche sur la page de connexion d’entreprise ou sur la page de l’application, utilisez l’une des sections suivantes pour la résoudre.


## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Résoudre une erreur de connexion sur votre page de connexion d’entreprise

Lorsque vous essayez de vous connecter, vous pouvez voir une erreur sur votre entreprise-page de connexion qui est similaire à l’exemple suivant.

![Erreur de connexion](./media/howto-v1-debug-saml-sso-issues/error.png)

Pour déboguer cette erreur, vous aurez besoin du message d’erreur et de la demande SAML. L’extension de connexion sécurisée à Mes applications rassemble automatiquement ces informations et affiche une aide à la résolution sur Azure AD. 

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>Pour résoudre l’erreur de connexion avec l’Extension de connexion sécurisée à mes applications installées

1. Lorsqu’une erreur se produit, l’extension vous redirige vers Azure AD **tester l’authentification unique sur** panneau. 
1. Sur le **tester l’authentification unique sur** panneau, sélectionnez **télécharger la demande SAML**. 
1. Lisez l’aide à la résolution qui s’affiche. Elle dépend de l’erreur et des valeurs de la
1. Vous verrez un **Fix it** bouton Mettre à jour automatiquement la configuration dans Azure AD pour résoudre le problème. Si vous ne voyez pas ce bouton, puis le problème de connexion n’est pas en raison d’une configuration incorrecte sur Azure AD.

Si aucune résolution n’est fournie pour l’erreur de connexion, nous vous suggérons d’utiliser la zone de texte des commentaires de nous informer.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>Pour résoudre l’erreur sans installer l’Extension de connexion sécurisée à mes applications

1. Copiez le message d’erreur dans le coin inférieur droit de la page. Il comporte :
    - Un ID de corrélation et un timestamp. Ces valeurs sont importantes lors de la création d’une demande de support auprès de Microsoft, car elles permettent aux ingénieurs d’identifier le problème et de le résoudre avec précision.
    - Une instruction identifiant la cause du problème.
1. Revenez à Azure AD et localisez le panneau **Tester l’authentification unique**.
1. Dans la zone de texte au-dessus de **Obtenir une aide à la résolution**, collez le message d’erreur.
1. Cliquez sur **Obtenir une aide à la résolution** pour afficher les étapes de résolution du problème. Elles sont susceptibles de demander des informations sur la demande ou la réponse SAML. Si vous n’utilisez pas l’Extension de connexion sécurisée à mes applications, vous devrez peut-être un outil tel que [Fiddler](https://www.telerik.com/fiddler) pour récupérer la demande SAML et la réponse.
1. Vérifiez que la destination dans la demande SAML correspond à la SAML Single Sign-On Service URL obtenues à partir d’Azure AD.
1. Vérifiez que l’émetteur dans la demande SAML est le même identificateur que vous avez configuré pour l’application dans Azure AD. Azure AD utilise l’émetteur pour rechercher une application dans l’annuaire.
1. Vérifiez AssertionConsumerServiceURL où l’application s’attend à recevoir le jeton SAML d’Azure AD. Vous pouvez configurer cette valeur dans Azure AD, mais il n’est pas obligatoire si elle fait partie de la demande SAML.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Résoudre une erreur de connexion sur la page d’application

Il peut arriver que la connexion réussisse, puis qu’une erreur s’affiche sur la page de l’application. Cela se produit lorsque Azure AD a envoyé un jeton à l’application, mais que celle-ci n’accepte pas la réponse.   

Pour résoudre cette erreur, effectuez les étapes suivantes :

1. Si l’application est dans la galerie Azure AD, vérifiez que vous avez suivi toutes les étapes pour l’intégration de l’application avec Azure AD. Pour trouver les instructions d’intégration relatives à votre application, voir la [liste des tutoriels d’intégration d’applications SaaS](../saas-apps/tutorial-list.md).
1. Récupérez la réponse SAML.
    - Si l’extension de connexion sécurisée à Mes applications est installée, cliquez sur **Télécharger la réponse SAML** dans le panneau **Tester l’authentification unique**.
    - Si l’extension n’est pas installée, utilisez un outil comme [Fiddler](https://www.telerik.com/fiddler) pour récupérer la réponse SAML. 
1. Notez ces éléments dans le jeton de la réponse SAML :
   - Identificateur unique de la valeur NameID et du format
   - Revendications émises dans le jeton
   - Certificat utilisé pour signer le jeton. 

     Pour plus d’informations sur la réponse SAML, voir [Protocole SAML d’authentification unique](single-sign-on-saml-protocol.md).

1. Maintenant que vous avez consulté la réponse SAML, consultez [erreur sur la page d’une application après la connexion](../manage-apps/application-sign-in-problem-application-error.md) pour obtenir des conseils sur la façon de résoudre le problème. 
1. Si vous êtes toujours pas en mesure de se connecter avec succès, vous pouvez demander le fournisseur de l’application en ce qui manque à partir de la réponse SAML.


## <a name="next-steps"></a>Étapes suivantes

Maintenant que l’authentification unique fonctionne à votre application, vous pourriez [automatiser l’approvisionnement et annulation d’approvisionnement pour les applications SaaS](../manage-apps/user-provisioning.md) ou [prise en main l’accès conditionnel](../conditional-access/app-based-conditional-access.md).
