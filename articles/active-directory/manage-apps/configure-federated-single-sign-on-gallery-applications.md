---
title: Comment configurer l’authentification unique fédérée pour une application de la galerie Azure AD | Microsoft Docs
description: Comment configurer l’authentification unique fédérée pour une application de la galerie Azure AD existante et utiliser les tutoriels pour se lancer rapidement
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: 8e50a495e1b0406e0c935ac31111dc6b5d0c0821
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207118"
---
# <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Comment configurer l’authentification unique fédérée pour une application de la galerie Azure AD

Un tutoriel pas à pas est disponible pour toutes les applications de la galerie Azure AD (Azure Active Directory) qui ont la fonctionnalité Enterprise Single Sign-On. Pour une aide pas à pas détaillée, vous pouvez accéder à la [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/).

## <a name="overview-of-steps-required"></a>Vue d’ensemble des étapes requises
Pour configurer une application à partir de la galerie Azure AD, vous devez effectuer les opérations suivantes :

-   [Ajouter une application à partir de la galerie Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configurer les valeurs de métadonnées de l’application dans Azure AD (URL de connexion, identificateur, URL de réponse)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Sélectionner l’identificateur de l’utilisateur et ajouter les attributs d’utilisateur à envoyer à l’application](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Récupérer le certificat et les métadonnées Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Configurer les valeurs de métadonnées Azure AD dans l’application (URL de connexion, émetteur, URL de déconnexion et certificat)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Affecter des utilisateurs à l’application](#assign-users-to-the-application)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Ajouter une application à partir de la galerie Azure AD

Pour ajouter une application à partir de la galerie Azure AD, procédez comme suit :

1.  Ouvrez le [portail Azure](https://portal.azure.com) et connectez-vous en tant **qu’administrateur général** ou que **coadministrateur**.

2.  Ouvrez l’**extension Azure Active Directory** en sélectionnant **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « Azure Active Directory » dans la zone de recherche, puis sélectionnez **Azure Active Directory**.

4.  Sélectionnez **Applications d’entreprise** dans le menu de navigation Azure AD de gauche.

5.  Sélectionnez **Ajouter** dans le coin supérieur droit du volet **Applications d’entreprise**.

6.  Dans la zone **Entrer un nom** de la section **Ajouter à partir de la galerie**, tapez le nom de l’application.

7.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

8.  Avant d’ajouter l’application, vous pouvez modifier son nom dans la zone **Nom**.

9.  Sélectionnez **Ajouter** pour ajouter l’application.

Après un court délai, vous devez être en mesure de voir le volet de configuration de l’application.

## <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Configurer l’authentification unique pour une application à partir de la galerie Azure AD

Pour configurer l’authentification unique pour une application, procédez comme suit :

1. Ouvrez le [portail Azure](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général** ou que **coadministrateur**.

2. Ouvrez l’**extension Azure Active Directory** en sélectionnant **Tous les services** en haut du menu de navigation principal de gauche.

3. Tapez « Azure Active Directory » dans la zone de recherche, puis sélectionnez **Azure Active Directory**.

4. Sélectionnez **Applications d’entreprise** dans le menu de navigation Azure Active Directory de gauche.

5. Sélectionnez **Toutes les applications** pour afficher la liste complète de vos applications.

   * Si l’application que vous recherchez ne figure pas dans la liste, utilisez le contrôle **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.

6. Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7. Une fois l’application chargée, sélectionnez **Authentification unique** dans le menu de navigation de gauche de l’application.

8. Sélectionnez **Authentification basée sur SAML** dans la liste déroulante **Mode**.

9. Entrez les valeurs obligatoires dans **Domaine et URL.** Ces valeurs doivent vous être communiquées par le fournisseur de l’application.

   1. Pour configurer l’application en tant qu’application à authentification unique lancée par le fournisseur de services, l’URL de connexion est obligatoire. Pour certaines applications, l’identificateur est également une valeur obligatoire.

   2. Pour configurer l’application en tant qu’application à authentification unique lancée par le fournisseur d’identité, une URL de réponse est obligatoire. Pour certaines applications, l’identificateur est également une valeur obligatoire.

10. **Facultatif** : Pour afficher les valeurs non obligatoires, sélectionnez **Afficher les paramètres d’URL avancés**.

11. Dans **Attributs d’utilisateur**, sélectionnez l’identificateur unique de vos utilisateurs dans la liste déroulante **Identificateur de l’utilisateur**.

12. **Facultatif** : Pour modifier les attributs à envoyer à l’application dans le jeton SAML lorsque l’utilisateur se connecte, sélectionnez **Afficher et modifier tous les autres attributs utilisateur**.

    Pour ajouter un attribut :
   
    1. Sélectionnez **Ajouter un attribut**. Entrez le **Nom**, puis sélectionnez la **Valeur** dans la liste déroulante.

    1. Sélectionnez **Enregistrer**. Le nouvel attribut s’affiche dans le tableau.

13. Sélectionnez **Configurer &lt;nom de l’application&gt;** pour accéder à la documentation expliquant comment configurer l’authentification unique dans l’application. En outre, vous disposez des URL de métadonnées et du certificat nécessaires à la configuration de l’authentification unique pour l’application.

14. Sélectionnez **Enregistrer** pour enregistrer la configuration.

15. Affectez des utilisateurs à l’application.

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Sélectionner l’identificateur de l’utilisateur et ajouter les attributs d’utilisateur à envoyer à l’application

Pour sélectionner l’identificateur de l’utilisateur ou ajouter des attributs utilisateur, effectuez les étapes suivantes :

1. Ouvrez le [portail Azure](https://portal.azure.com/) et connectez-vous comme **administrateur général** ou **coadministrateur**.

2. Ouvrez l’**extension Azure Active Directory** en sélectionnant **Tous les services** en haut du menu de navigation principal de gauche.

3. Tapez « Azure Active Directory » dans la zone de recherche, puis sélectionnez **Azure Active Directory**.

4. Sélectionnez **Applications d’entreprise** dans le menu de navigation Azure Active Directory de gauche.

5. Sélectionnez **Toutes les applications** pour afficher la liste complète de vos applications.

   * Si l’application que vous recherchez ne figure pas dans la liste, utilisez le contrôle **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.

6. Sélectionnez l’application que vous avez configurée avec l’authentification unique.

7. Une fois l’application chargée, sélectionnez **Authentification unique** dans le menu de navigation de gauche de l’application.

8. Dans la section **Attributs d’utilisateur**, sélectionnez l’identificateur unique de vos utilisateurs dans la liste déroulante **Identificateur de l’utilisateur**. L’option sélectionnée doit correspondre à la valeur attendue dans l’application pour authentifier l’utilisateur.

   >[!NOTE] 
   >Azure AD sélectionne le format de l’attribut NameID (identificateur d’utilisateur) en fonction de la valeur sélectionnée ou du format demandé par l’application dans la demande d’authentification SAML. Pour plus d’informations, consultez [Protocole SAML d’authentification unique](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) dans la section NameIDPolicy.
   >
   >

9. Pour ajouter des attributs utilisateur, sélectionnez **Afficher et modifier tous les autres attributs utilisateur** afin de modifier les attributs à envoyer à l’application dans le jeton SAML lorsque l’utilisateur se connecte.

   Pour ajouter un attribut :
  
   1. Sélectionnez **Ajouter un attribut**. Entrez le **Nom**, puis sélectionnez la **Valeur** dans la liste déroulante.

   2. Sélectionnez **Enregistrer**. Le nouvel attribut s’affiche dans le tableau.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Télécharger les métadonnées ou le certificat Azure AD

Pour télécharger les métadonnées ou le certificat de l’application à partir d’Azure AD, procédez comme suit :

1. Ouvrez le [portail Azure](https://portal.azure.com/) et connectez-vous comme **administrateur général** ou **coadministrateur**.

2. Ouvrez l’**extension Azure Active Directory** en sélectionnant **Tous les services** en haut du menu de navigation principal de gauche.

3. Tapez « Azure Active Directory » dans la zone de recherche, puis sélectionnez **Azure Active Directory**.

4. Sélectionnez **Applications d’entreprise** dans le menu de navigation Azure Active Directory de gauche.

5. Sélectionnez **Toutes les applications** pour afficher la liste complète de vos applications.

   *  Si l’application que vous recherchez ne figure pas dans la liste, utilisez le contrôle **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.

6. Sélectionnez l’application que vous avez configurée avec l’authentification unique.

7. Une fois l’application chargée, sélectionnez **Authentification unique** dans le menu de navigation de gauche de l’application.

8. Accédez à la section **Certificat de signature SAML**, puis sélectionnez la valeur de colonne **Télécharger**. En fonction de ce que l’application nécessite pour configurer l’authentification unique, vous voyez soit l’option de téléchargement des métadonnées XML, soit le certificat.

Azure AD fournit également une URL permettant d’accéder aux métadonnées. Utilisez le modèle suivant pour obtenir l’URL des métadonnées spécifique à l’application : `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`

## <a name="assign-users-to-the-application"></a>Affecter des utilisateurs à l’application

Pour affecter un ou plusieurs utilisateurs directement à une application, procédez comme suit :

1. Ouvrez le [portail Azure](https://portal.azure.com/) et connectez-vous comme **administrateur général**.

2. Ouvrez l’**extension Azure Active Directory** en sélectionnant **Tous les services** en haut du menu de navigation principal de gauche.

3. Tapez « Azure Active Directory » dans la zone de recherche, puis sélectionnez **Azure Active Directory**.

4. Sélectionnez **Applications d’entreprise** dans le menu de navigation Azure Active Directory de gauche.

5. Sélectionnez **Toutes les applications** pour afficher la liste complète de vos applications.

   * Si l’application que vous recherchez ne figure pas dans la liste, utilisez le contrôle **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.

6. Dans la liste qui s’affiche, sélectionnez l’application à laquelle vous souhaitez affecter un utilisateur.

7. Une fois l’application chargée, sélectionnez **Utilisateurs et groupes** dans le menu de navigation de gauche de l’application.

8. Sélectionnez le bouton **Ajouter** en haut de la liste **Utilisateurs et groupes** pour ouvrir le volet **Ajouter une attribution**.

9. Sélectionnez le sélecteur **Utilisateurs et groupes** à partir du volet **Ajouter une attribution**.

10. Tapez le **nom complet** ou l’**adresse e-mail** de l’utilisateur que vous souhaitez affecter dans la zone de recherche **Rechercher par nom ou adresse de messagerie**.

11. Pointez sur l’**utilisateur** dans la liste pour afficher une **case à cocher**. Cochez la case en regard de la photo de profil ou du logo de l’utilisateur pour ajouter ce dernier à la liste **Sélectionné**.

12. **Facultatif** : Si vous souhaitez **ajouter plusieurs utilisateurs**, tapez un autre **nom complet** ou une autre **adresse e-mail** dans la zone de recherche **Rechercher par nom ou adresse de messagerie**, puis cochez la case pour ajouter cet utilisateur à la liste **Sélectionné**.

13. Après avoir sélectionné les utilisateurs, sélectionnez le bouton **Sélectionner** pour les ajouter à la liste des utilisateurs et des groupes à affecter à l’application.

14. **Facultatif** : Sélectionnez le sélecteur **Sélectionner un rôle** dans le volet **Ajouter une attribution** pour sélectionner un rôle à affecter aux utilisateurs que vous avez sélectionnés.

15. Sélectionnez le bouton **Attribuer** pour affecter l’application aux utilisateurs sélectionnés.

Après quelques instants, les utilisateurs que vous avez sélectionnés seront en mesure de démarrer ces applications à l’aide des méthodes décrites dans la section de description des solutions.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Personnaliser des revendications SAML envoyées à une application

Pour savoir comment personnaliser les revendications d’attribut SAML envoyées à votre application, consultez [Mappage de revendications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping).

## <a name="next-steps"></a>Étapes suivantes
[Fournir une authentification unique à vos applications avec le proxy d’application](application-proxy-configure-single-sign-on-with-kcd.md)



