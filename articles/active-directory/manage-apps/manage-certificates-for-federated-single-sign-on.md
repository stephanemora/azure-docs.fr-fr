---
title: Gestion des certificats de fédération dans Azure AD | Microsoft Docs
description: Apprenez à personnaliser la date d’expiration pour vos certificats de fédération, mais aussi à renouveler les certificats arrivant à expiration.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: celested
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64c2d14a2aa6fc6b53260912b5bead2bd7c01e8d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60440571"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Gérer des certificats pour l’authentification unique fédérée sur Azure Active Directory

Dans cet article, nous abordons les questions courantes et les informations relatives aux certificats qu’Azure Active Directory (Azure AD) crée pour établir fédérée-session unique (SSO) à votre logiciel en tant qu’applications de service (SaaS). Ajoutez des applications à partir de la galerie d’applications Azure AD ou à l’aide de modèle d’applications ne figurant pas dans la galerie. Configurez l’application à l’aide de l’option d’authentification unique fédérée.

Cet article s’applique uniquement aux applications qui sont configurées pour utiliser l’authentification unique Azure AD via [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) fédération (SAML).

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Certificat généré automatiquement pour les applications de la galerie et celles ne figurant pas dans la galerie

Lorsque vous ajoutez une nouvelle application à partir de la galerie et configurez une basée sur SAML authentification (en sélectionnant **Single sign-on** > **SAML** à partir de la page de présentation d’application), Azure AD génère un certificat de l’application est valide pendant trois ans. Pour télécharger le certificat actif comme un certificat de sécurité (**.cer**) fichier, revenez à cette page (**basée sur SAML authentification**) et sélectionnez un lien de téléchargement dans le **certificat de signature SAML** en-tête. Vous pouvez choisir entre le certificat brut (binaire) ou le certificat (base texte codé en base 64) en Base64. Pour les applications de la galerie, cette section peut également indiquer un lien pour télécharger le certificat en tant que XML de métadonnées de fédération (un **.xml** fichier), en fonction de la configuration requise de l’application.

![Active signatures options de téléchargement certificat SAML](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

Vous pouvez également télécharger un certificat actif ou inactif en sélectionnant le **certificat de signature SAML** du titre **modifier** icône (un crayon), qui affiche le **certificat de signature SAML** page. Sélectionnez les points de suspension (**...** ) en regard du certificat que vous souhaitez télécharger, puis choisissez le format de certificat que vous souhaitez. Vous avez l’option supplémentaire pour télécharger le certificat au format de courrier de confidentialité (PEM). Ce format est identique en Base64, mais avec un **.pem** extension qui n’est pas reconnue dans Windows en tant que certificat format de fichier.

![Options de téléchargement de certificat (actives et inactives) de signature SAML](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Personnaliser la date d’expiration de votre certificat de fédération et activer le nouveau certificat

Par défaut, Azure configure un certificat pour expirer après trois ans lorsqu’il est créé automatiquement pendant la configuration de l’authentification unique de le SAML. Étant donné que vous ne pouvez pas modifier la date d’un certificat après que l’avoir enregistrée, vous devez :

1. Créer un nouveau certificat avec la date de votre choix.
2. Enregistrer le nouveau certificat.
3. Téléchargez le nouveau certificat dans le format correct.
4. Chargez le nouveau certificat à l’application.
5. Activer le nouveau certificat dans le portail Azure Active Directory.

Les deux sections suivantes vous aider à effectuer ces étapes.

### <a name="create-a-new-certificate"></a>Créer un certificat

Tout d’abord, créez et enregistrez le nouveau certificat avec une autre date d’expiration :

1. Se connecter à la [portail Azure Active Directory](https://aad.portal.azure.com/). Le **centre d’administration Azure Active Directory** page s’affiche.

2. Dans le volet gauche, sélectionnez **Applications d’entreprise**. Une liste des applications d’entreprise dans votre compte s’affiche.

3. Sélectionnez l’application affectée. Une page de présentation de l’application s’affiche.

4. Dans le volet gauche de la page de vue d’ensemble d’application, sélectionnez **Single sign-on**.

5. Si le **sélectionner une méthode d’authentification unique** page s’affiche, sélectionnez **SAML**.

6. Dans le **définir des Single Sign-On with SAML - aperçu** page, recherchez le **certificat de signature SAML** titre et sélectionnez le **modifier** icône (un crayon). Le **certificat de signature SAML** page apparaît, affichant l’état (**Active** ou **inactif**), date d’expiration et l’empreinte numérique (une chaîne de hachage) de chaque certificat.

7. Sélectionnez **nouveau certificat**. Une nouvelle ligne apparaît sous la liste des certificats, où la date d’expiration par défaut est exactement trois ans après la date actuelle. (Vos modifications n’ont pas encore été enregistrées, vous pouvez toujours modifier la date d’expiration.)

8. Dans la nouvelle ligne de certificat, pointez sur la colonne de date d’expiration et sélectionnez le **sélectionner une Date** icône (un calendrier). Un contrôle calendrier apparaît, affichant les jours d’un mois de date d’expiration de la nouvelle ligne.

9. Utiliser le contrôle de calendrier pour définir une nouvelle date. Vous pouvez définir n’importe quelle date entre la date actuelle et trois ans après la date actuelle.

10. Sélectionnez **Enregistrer**. Le nouveau certificat s’affiche maintenant avec l’état **inactif**, la date d’expiration et date que vous avez choisi, une empreinte numérique.

11. Sélectionnez le **X** pour revenir à la **définir des Single Sign-On with SAML - version préliminaire** page.

### <a name="upload-and-activate-a-certificate"></a>Télécharger et activer un certificat

Ensuite, téléchargez le nouveau certificat dans le format correct, chargez-le à l’application et activez-le dans Azure Active Directory :

1. Afficher les instructions de configuration de l’authentification SAML supplémentaires de l’application à l’aide :
   - en sélectionnant le **guide de configuration** lien pour afficher dans une fenêtre de navigateur distincte ou d’un onglet, ou
   - accédant à la **configurer** titre et en sélectionnant **obtenir des instructions détaillées** à afficher dans une barre latérale.

2. Dans les instructions, notez le format de codage requis pour le téléchargement du certificat.

3. Suivez les instructions de la [certificat généré automatiquement pour les applications non-galerie et](#auto-generated-certificate-for-gallery-and-non-gallery-applications) section précédemment. Cette étape télécharge le certificat dans le format d’encodage requis pour le chargement par l’application.

4. Lorsque vous souhaitez reportées sur le nouveau certificat, revenez à la **certificat de signature SAML** page, puis dans la ligne de certificat qui vient d’être enregistré, sélectionnez les points de suspension (**...** ) et sélectionnez **activer le certificat**. L’état du nouveau certificat devient **Active**, et le certificat précédemment actif devient un statut de **inactif**.

5. Continuer suivant les instructions de configuration de l’authentification de SAML de l’application que vous avez affiché précédemment, afin que vous pouvez télécharger la signature SAML de certificat dans le format d’encodage correct.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>Ajouter des adresses de notification de messagerie pour l’expiration du certificat

Azure AD envoie un jours de notification de 60, 30 et 7 e-mail avant l’expiration du certificat SAML. Vous pouvez ajouter plusieurs adresses de messagerie pour recevoir des notifications. Pour spécifier les adresses e-mail que vous souhaitez que les notifications à envoyer à :

1. Dans le **certificat de signature SAML** page, accédez à la **adresses e-mail de notification** titre. Par défaut, cet en-tête utilise uniquement l’adresse de messagerie de l’administrateur qui a ajouté l’application.

2. Sous l’adresse de messagerie finale, tapez l’adresse de messagerie qui doit recevoir l’avis d’expiration du certificat, puis appuyez sur ENTRÉE.

3. Répétez l’étape précédente pour chaque adresse de messagerie que vous souhaitez ajouter.

4. Pour chaque adresse de messagerie que vous souhaitez supprimer, sélectionnez le **supprimer** icône (une poubelle) en regard de l’adresse de messagerie.

5. Sélectionnez **Enregistrer**.

Vous recevez l’e-mail de notification de la part de aadnotification@microsoft.com. Pour éviter l’e-mail à partir de votre emplacement de courrier indésirable, ajoutez ce courrier électronique à vos contacts.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Renouvellement d’un certificat arrivant à expiration

Si un certificat est sur le point d’expirer, vous pouvez le renouveler à l’aide d’une procédure qui résulte aucun temps d’arrêt important pour vos utilisateurs. Pour renouveler un certificat arrivant à expiration :

1. Suivez les instructions de la [créer un nouveau certificat](#create-a-new-certificate) section plus haut, à l’aide d’une date qui se chevauche avec le certificat existant. Cette date limite la quantité de temps d’arrêt causés par l’expiration du certificat.

2. Si l’application peut remplacer automatiquement un certificat, définissez le nouveau certificat à l’état actif en suivant ces étapes :
   1. Revenez à la **certificat de signature SAML** page.
   2. Dans la ligne de certificat qui vient d’être enregistré, sélectionnez les points de suspension (**...** ), puis sélectionnez **activer le certificat**.
   3. Ignorer les deux étapes suivantes.

3. Si l’application peut uniquement gérer un certificat à la fois, choisissez un intervalle de temps d’arrêt pour effectuer l’étape suivante. (Sinon, si l’application ne récupère automatiquement le nouveau certificat, mais peut gérer plusieurs certificats de signature, vous pouvez effectuer l’étape suivante à tout moment.)

4. Avant l’expiration de l’ancien certificat, suivez les instructions de la [télécharger et activer un certificat](#upload-and-activate-a-certificate) section précédemment.

5. Connectez-vous à l’application pour vous assurer que le certificat fonctionne correctement.

## <a name="related-articles"></a>Articles connexes

* [Tutoriels pour l’intégration d’applications SaaS avec Azure Active Directory](../saas-apps/tutorial-list.md)
* [Gestion des applications avec Azure Active Directory](what-is-application-management.md)
* [S’authentifier avec l’authentification unique auprès des applications dans Azure Active Directory](what-is-single-sign-on.md)
* [Débogage basée sur SAML SSO aux applications dans Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md)
