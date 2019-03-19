---
title: Problèmes de connexion à une application non issue de la galerie configurée pour l’authentification unique fédérée | Microsoft Docs
description: Instructions pour résoudre les problèmes rencontrés lors de la connexion à une application configurée pour l’authentification unique SAML fédérée avec Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cb0139227012be610977df3240f74a03f53be7e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58009057"
---
# <a name="problems-signing-in-to-a-non-gallery-application-configured-for-federated-single-sign-on"></a>Problèmes de connexion à une application non issue de la galerie configurée pour l’authentification unique fédérée

Pour résoudre les problèmes de connexion ci-dessous, nous vous recommandons de que suivre ces suggestion pour obtenir le meilleur diagnostic et d’automatiser les étapes de résolution :

- Installer le [mon Extension de navigateur sécuriser les applications](access-panel-extension-problem-installing.md) pour aider à Azure Active Directory (Azure AD) pour fournir le meilleur diagnostic et résolutions lorsque vous utilisez le test rencontrer dans le portail Azure.
- Reproduisez l’erreur à l’aide de l’expérience de test dans la page de configuration d’application dans le portail Azure. En savoir plus sur [basée sur SAML de déboguer des applications d’authentification uniques](../develop/howto-v1-debug-saml-sso-issues.md)

## <a name="application-not-found-in-directory"></a>Application introuvable dans le répertoire

*Erreur AADSTS70001 : Application avec l’identificateur `https://contoso.com` est introuvable dans le répertoire*.

**Cause possible**

L’attribut d’émetteur envoyé de l’application vers Azure AD dans la demande SAML ne correspond pas à la valeur de l’identificateur configurée dans l’application Azure AD.

**Résolution :**

Vérifiez que le `Issuer` attribut dans la demande SAML correspond à la valeur d’identificateur configurée dans Azure AD. Si vous utilisez le [test expérience](../develop/howto-v1-debug-saml-sso-issues.md) dans le portail Azure avec l’Extension mon navigateur de sécuriser les applications, vous n’avez pas besoin manuellement, suivez ces étapes.

1. Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2. Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3. Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4. Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5. Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

   * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6. Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7. Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8. Une fois l’application chargée, ouvrez **configuration SAML base**. Vérifiez que la valeur dans la zone de texte identificateur correspond à la valeur de la valeur d’identificateur mentionnée dans l’erreur.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>L’adresse de réponse ne correspond pas aux adresses de réponse configurées pour l’application. 

*Erreur AADSTS50011 : L’adresse de réponse `https://contoso.com` ne correspond pas à des adresses de réponse configurées pour l’application* 

**Cause possible** 

La valeur AssertionConsumerServiceURL dans la demande SAML ne correspond pas à la valeur de l’URL de réponse ou au modèle configuré dans Azure AD. La valeur AssertionConsumerServiceURL dans la demande SAML est l’URL que vous voyez dans l’erreur. 

**Résolution :** 

Vérifiez que le `Issuer` attribut dans la demande SAML correspond à la valeur d’identificateur configurée dans Azure AD. Si vous utilisez le [test expérience](../develop/howto-v1-debug-saml-sso-issues.md) dans le portail Azure avec l’Extension mon navigateur de sécuriser les applications, vous n’avez pas besoin manuellement, suivez ces étapes.
 
1. Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**. 

2. Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche. 

3. Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**. 

4. Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory. 

5. Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications. 

   * Si l’application que vous recherchez n’apparaît pas ici, utilisez le contrôle **Filtrer** en haut de la liste **Toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.
  
6. Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7. Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8. Une fois l’application chargée, ouvrez **configuration SAML base**. Vérifier ou mettre à jour la valeur dans la zone de texte URL de réponse pour faire correspondre la `AssertionConsumerServiceURL` valeur dans la demande SAML.    
    
Une fois que vous avez mis à jour la valeur de l’URL de réponse dans Azure AD, et il correspond à la valeur envoyée par l’application dans la demande SAML, il se peut que vous devez être en mesure de se connecter à l’application.

## <a name="user-not-assigned-a-role"></a>Utilisateur non affecté à un rôle

*Erreur AADSTS50105 : L’utilisateur connecté `brian\@contoso.com` n’est pas affecté à un rôle d’application*

**Cause possible**

L’utilisateur ne dispose pas des autorisations nécessaires pour accéder à l’application dans Azure AD.

**Résolution :**

Pour affecter un ou plusieurs utilisateurs à une application directement, suivez les étapes ci-dessous. Si vous utilisez le [test expérience](../develop/howto-v1-debug-saml-sso-issues.md) dans le portail Azure avec l’Extension mon navigateur de sécuriser les applications, vous n’avez pas besoin manuellement, suivez ces étapes.

1. Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général**.

2. Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3. Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4. Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5. Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

   * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6. Dans la liste qui s’affiche, sélectionnez l’application à laquelle vous souhaitez affecter un utilisateur.

7. Une fois l’application chargée, cliquez sur **Utilisateurs et groupes** dans le menu de navigation de gauche de l’application.

8. Cliquez sur le bouton **Ajouter** en haut de la liste **Utilisateurs et groupes** pour ouvrir le volet **Ajouter une attribution**.

9. Cliquez sur le sélecteur **Utilisateurs et groupes** à partir du volet **Ajouter une attribution**.

10. Tapez **le nom complet** ou **l’adresse de messagerie** de l’utilisateur souhaité pour l’attribution dans la zone de recherche **Rechercher par nom ou adresse de messagerie**.

11. Pointez sur **l’utilisateur** dans la liste pour afficher une **case à cocher**. Cliquez sur la case à cocher en regard de la photo de profil ou du logo de l’utilisateur pour ajouter ce dernier à la liste **Sélectionné**.

12. **Facultatif** : Si vous souhaitez **ajouter plusieurs utilisateurs**, entrez un autre **nom complet** ou une autre **adresse de messagerie** dans la zone de recherche **Rechercher par nom ou adresse de messagerie**, puis cochez la case pour ajouter cet utilisateur à la liste **Sélectionné**.

13. Après avoir sélectionné les utilisateurs, cliquez sur le bouton **Sélectionner** pour les ajouter à la liste des utilisateurs et des groupes à affecter à l’application.

14. **Facultatif :** cliquez sur le sélecteur **Sélectionner un rôle** dans le volet **Ajouter une attribution** pour sélectionner un rôle à affecter aux utilisateurs que vous avez sélectionnés.

15. Cliquez sur le bouton **Attribuer** pour affecter l’application aux utilisateurs sélectionnés.

Après quelques instants, les utilisateurs que vous avez sélectionnés seront en mesure de démarrer ces applications à l’aide des méthodes décrites dans la section de description des solutions.

## <a name="not-a-valid-saml-request"></a>Demande SAML non valide

*Erreur AADSTS75005 : La requête n’est pas un message de protocole Saml2 valide.*

**Cause possible**

Azure AD ne prend pas en charge les demandes SAML envoyées par l’application pour l’authentification unique. Voici certains problèmes courants :

-   Des champs obligatoires sont manquants dans la demande SAML

-   La méthode de demande SAML encodée

**Résolution :**

1.  Capturez la demande SAML. Pour savoir comment capturer la demande SAML, suivez le didacticiel [Comment déboguer une authentification unique SAML pour des applications dans Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging).

2.  Contactez le fournisseur de l’application et communiquez-lui les informations suivantes :

    -   Demande SAML

    -   [Spécifications du protocole SAML d’authentification unique Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

Le fournisseur de l’application doit valider qu’ils prennent en charge l’implémentation SAML Azure AD pour l’authentification unique.

## <a name="misconfigured-application"></a>Application mal configurée

*Erreur AADSTS650056 : Application mal configurée. La raison peut être l’une des suivantes : Le client n’a pas répertoriées toutes les autorisations pour « AAD Graph » dans les autorisations demandées dans l’inscription d’application du client. Ou bien, l’administrateur n’a pas accepté par le client. Ou bien, vérifiez l’identificateur d’application dans la demande pour vous assurer qu’il correspond à l’identificateur d’application client configuré. Veuillez contacter votre administrateur pour corriger la configuration ou de donner leur consentement pour le compte du client.* .

**Cause possible**

Le `Issuer` attribut envoyé à partir de l’application à Azure AD dans la demande SAML ne correspond pas à la valeur d’identificateur configurée pour l’application dans Azure AD.

**Résolution :**

Vérifiez que le `Issuer` attribut dans la demande SAML correspond à la valeur d’identificateur configurée dans Azure AD. Si vous utilisez le [test expérience](../develop/howto-v1-debug-saml-sso-issues.md) dans le portail Azure avec l’Extension mon navigateur de sécuriser les applications, vous n’avez pas besoin manuellement, suivez ces étapes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

1.  Ouvrez le **Extension Azure Active Directory** en sélectionnant **tous les services** en haut du menu principal de navigation de gauche.

1.  Type **« Azure Active Directory »** dans la zone de recherche de filtre, puis sélectionnez le **Azure Active Directory** élément.

1.  Sélectionnez **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

1.  Sélectionnez **Toutes les applications** pour afficher la liste complète de vos applications.

    Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.

1.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

1.  Une fois l’application chargée, ouvrez **configuration SAML base**. Vérifiez que la valeur dans la zone de texte identificateur correspond à la valeur de la valeur d’identificateur mentionnée dans l’erreur.

## <a name="certificate-or-key-not-configured"></a>Certificat ou clé non configuré(e)

Erreur AADSTS50003 : Aucune clé de signature configurée.

**Cause possible**

L’objet d’application est endommagé et Azure AD ne reconnaît pas le certificat configuré pour l’application.

**Résolution :**

Pour supprimer et créer un nouveau certificat, effectuez les étapes suivantes :

1. Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2. Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3. Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4. Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5. Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

   * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6. Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7. Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8. Dans la section **Certificat de signature SAML**, cliquez sur **Créer un certificat**.

9. Sélectionnez une date d’expiration. Cliquez ensuite sur **Enregistrer**.

10. Cochez la case **Activer le nouveau certificat** pour substituer le certificat actif. Ensuite, cliquez sur **Enregistrer** en haut du volet, puis acceptez d’activer le certificat de substitution.

11. Dans la section **Certificat de signature SAML**, cliquez sur **Supprimer** pour supprimer le certificat **Inutilisé**.

## <a name="saml-request-not-present-in-the-request"></a>Requête SAML absente de la requête

*Erreur AADSTS750054 : SAMLRequest ou SAMLResponse doit être présent en tant que paramètres de la chaîne de requête dans la requête HTTP pour la liaison de redirection SAML.*

**Cause possible**

Azure AD n'a pas pu identifier la requête SAML dans les paramètres URL de la requête HTTP. Cela peut se produire si l’application n’utilise pas de redirection HTTP liaison lors de l’envoi de la demande SAML à Azure AD.

**Résolution :**

L’application doit envoyer la demande SAML encodée dans l’en-tête location à l’aide de HTTP rediriger la liaison. Pour plus d'informations sur la mise en œuvre, lisez la section Liaison de redirection HTTP dans le [document de spécification du protocole SAML](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD envoie le jeton à un point de terminaison incorrect

**Cause possible**

Lors de l’authentification unique, si la demande de connexion ne contient pas une URL de réponse explicite (URL Assertion Consumer Service), puis Azure AD est une des configuré s’appuient les URL pour cette application. Même si l’application possède une URL de réponse explicite configurée, l’utilisateur peut être redirigé https://127.0.0.1:444. 

Lorsque l’application a été ajoutée comme ne figurant pas sur galerie, Azure Active Directory a créé cette URL de réponse en tant que valeur par défaut. Ce comportement a changé et Azure Active Directory n’ajoute plus cette URL par défaut. 

**Résolution :**

Supprimer les URL de réponse inutilisé configurés pour l’application.

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez le **Extension Azure Active Directory** en sélectionnant **tous les services** en haut du menu principal de navigation de gauche.

3.  Type **« Azure Active Directory »** dans la zone de recherche de filtre, puis sélectionnez le **Azure Active Directory** élément.

4.  Sélectionnez **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Sélectionnez **Toutes les applications** pour afficher la liste complète de vos applications.

    Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.

6.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7.  Une fois l’application chargée, ouvrez **configuration SAML base**. Dans le **URL de réponse (URL Assertion Consumer Service)**, delete inutilisée ou URL de réponse par défaut créé par le système. Par exemple : `https://127.0.0.1:444/applications/default.aspx`.



## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problème lors de la personnalisation des revendications SAML envoyées à une application

Pour savoir comment personnaliser les revendications d’attribut SAML envoyées à votre application, consultez l’article [Mappage des revendications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) pour plus d’informations.

## <a name="next-steps"></a>Étapes suivantes
[Spécifications du protocole SAML d’authentification unique Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)
