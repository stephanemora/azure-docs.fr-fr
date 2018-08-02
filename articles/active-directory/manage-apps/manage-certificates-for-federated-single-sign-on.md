---
title: Gestion des certificats de fédération dans Azure AD | Microsoft Docs
description: Apprenez à personnaliser la date d’expiration pour vos certificats de fédération, mais aussi à renouveler les certificats arrivant à expiration.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/09/2017
ms.author: barbkess
ms.reviewer: jeedes
ms.openlocfilehash: 1274143c2ae64574202b45969af43290e532fbfb
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366111"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Gérer des certificats pour l’authentification unique fédérée sur Azure Active Directory
Cet article répond aux questions relatives aux certificats créés par Azure Active Directory (Azure AD) pour établir une authentification unique fédérée vers vos applications SaaS. Ajoutez des applications à partir de la galerie d’applications Azure AD ou à l’aide de modèle d’applications ne figurant pas dans la galerie. Configurez l’application à l’aide de l’option d’authentification unique fédérée.

Cet article concerne uniquement les applications configurées pour utiliser l’authentification unique Azure AD grâce à la fédération SAML, comme le montre l’exemple suivant :

![Authentification unique Azure AD](./media/manage-certificates-for-federated-single-sign-on/saml_sso.PNG)

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Certificat généré automatiquement pour les applications de la galerie et celles ne figurant pas dans la galerie
Lorsque vous ajoutez une nouvelle application à partir de la galerie et configurez une authentification SAML, Azure AD génère un certificat d’une validité de trois ans pour l’application. Vous pouvez télécharger ce certificat dans la section **Certificat de signature SAML**. Pour les applications de la galerie, cette section peut afficher une option permettant de télécharger le certificat ou les métadonnées. L’option qui s’affiche dépend de la configuration de l’application.

![Authentification unique Azure AD](./media/manage-certificates-for-federated-single-sign-on/saml_certificate_download.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Personnaliser la date d’expiration de votre certificat de fédération et activer le nouveau certificat
Par défaut, les certificats sont configurés pour expirer au bout de trois ans. Vous pouvez choisir une autre date d’expiration pour votre certificat en réalisant les étapes suivantes.
Les captures d'écran ont été prises pendant une utilisation de Salesforce, mais les étapes peuvent s'appliquer à n'importe quelle application SaaS fédérée.

1. Dans le [portail Azure](https://aad.portal.azure.com), cliquez sur **Application d’entreprise** dans le volet de navigation gauche puis cliquez sur **Nouvelle application** sur la page **Vue d’ensemble** :

   ![Ouvrir l’Assistant Configuration de l’authentification unique](./media/manage-certificates-for-federated-single-sign-on/enterprise_application_new_application.png)

2. Recherchez une application dans la galerie, puis sélectionnez celle que vous voulez ajouter. Si vous ne trouvez pas l’application voulue, ajoutez l’application à l’aide de l’option **Application ne figurant pas dans la galerie**. Cette fonctionnalité est disponible uniquement dans Azure AD Premium (P1 et P2).

    ![Authentification unique Azure AD](./media/manage-certificates-for-federated-single-sign-on/add_gallery_application.png)

3. Cliquez sur le lien **Authentification unique** dans le volet de gauche, puis configurez le **Mode** sur **Authentification basée sur SAML**. Cette étape génère pour votre application un certificat valide trois ans.

4. Pour créer un certificat, cliquez sur le lien **Créer un certificat** dans la section **Certificat de signature SAML**.

    ![Générer un nouveau certificat](./media/manage-certificates-for-federated-single-sign-on/create_new_certficate.png)

5. Le lien **Créer un nouveau certificat** ouvre le contrôle Calendrier. Vous pouvez définir la date et l’heure de votre choix jusqu'à trois ans plus tard à compter de la date actuelle. La date et l’heure sélectionnées correspondent à la nouvelle date et à la nouvelle heure d’expiration de votre nouveau certificat. Cliquez sur **Enregistrer**.

    ![Télécharger, puis charger le certificat](./media/manage-certificates-for-federated-single-sign-on/certifcate_date_selection.PNG)

6. Le nouveau certificat peut désormais être téléchargé. Cliquez sur le lien **Certificat** pour le télécharger. À ce stade, votre certificat n’est pas encore actif. Pour activer le certificat, cochez la case **Définir comme certificat actif**, puis cliquez sur **Enregistrer**. Une fois le certificat activé, Azure AD démarre à l’aide du nouveau certificat pour authentifier la réponse.

7.  Pour savoir comment charger le certificat dans une application SaaS spécifique, cliquez sur le lien **Afficher le didacticiel sur la configuration de l’application**.

## <a name="certificate-expiration-notification-email"></a>E-mail de notification d’expiration du certificat

Azure AD enverra un e-mail de notification 60, 30 et 7 jours avant expiration du certificat SAML. Pour spécifier l’adresse e-mail à laquelle envoyer la notification :

- Dans la page Authentification unique de l’application Azure Active Directory, accédez au champ E-mail de notification.
- Entrez l’adresse e-mail qui doit recevoir l’e-mail de notification d’expiration du certificat. Par défaut, ce champ utilise l’adresse e-mail de l’administrateur qui a ajouté l’application.

Vous recevez l’e-mail de notification de la part de aadnotification@microsoft.com. Pour éviter que l’e-mail ne soit traité comme un courrier indésirable, veillez à ajouter cette adresse e-mail à vos contacts. 

## <a name="renew-a-certificate-that-will-soon-expire"></a>Renouvellement d’un certificat arrivant à expiration
Les étapes de renouvellement suivantes ne devraient pas entraîner de temps d’arrêt important pour vos utilisateurs. Les captures d’écran de cette section ont été prises pendant une utilisation de Salesforce, mais les étapes sont valables pour n’importe quelle application SaaS fédérée.

1. Sur l’application **Azure Active Directory**, dans la page **Authentification unique**, générez un nouveau certificat pour votre application. Pour cela, cliquez sur le lien **Créer un certificat** dans la section **Certificat de signature SAML**.

    ![Générer un nouveau certificat](./media/manage-certificates-for-federated-single-sign-on/create_new_certficate.png)

2. Sélectionnez la date et l’heure d’expiration souhaitées pour votre nouveau certificat, puis cliquez sur **Enregistrer**.

3. Téléchargez le certificat à l’aide de l’option **Certificat de signature SAML**. Chargez le nouveau certificat sur l’écran de configuration de l’authentification unique de l’application SaaS. Pour savoir comment charger le certificat dans une application SaaS spécifique, cliquez sur le lien **Afficher le didacticiel sur la configuration de l’application**.
   
4. Pour activer le nouveau certificat dans Azure AD, cochez la case **Définir comme certificat actif**, puis cliquez sur le bouton **Enregistrer** en haut de la page. Le nouveau certificat est ainsi activé côté Azure AD. L’état du certificat passe de **Nouveau** à **Actif**. Une fois le certificat activé, Azure AD démarre à l’aide du nouveau certificat pour authentifier la réponse. 
   
    ![Générer un nouveau certificat](./media/manage-certificates-for-federated-single-sign-on/new_certificate_download.png)

## <a name="related-articles"></a>Articles connexes
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](../saas-apps/tutorial-list.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](../active-directory-apps-index.md)
* [Accès aux applications et authentification unique avec Azure Active Directory](what-is-single-sign-on.md)
* [Dépannage de l’authentification unique basée sur SAML](../develop/active-directory-saml-debugging.md)
