---
title: Problèmes de connexion à une application d’authentification unique fédérée n’appartenant pas à la galerie
description: Instructions pour résoudre les problèmes rencontrés lors de la connexion à une application configurée pour l’authentification unique SAML fédérée avec Azure AD
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
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cd951f0b4d2f4887630e29cbd3b0ae429b9f6f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367861"
---
# <a name="problems-signing-in-to-a-non-gallery-application-configured-for-federated-single-sign-on"></a>Problèmes de connexion à une application non issue de la galerie configurée pour l’authentification unique fédérée

Pour résoudre les problèmes de connexion ci-dessous, nous vous recommandons de suivre ces suggestions afin de bénéficier du meilleur diagnostic et d’automatiser les étapes de résolution :

- Installez l’[extension de navigateur sécurisée Mes applications](access-panel-extension-problem-installing.md) pour aider Azure Active Directory (Azure AD) à fournir un meilleur diagnostic et de meilleures résolutions lorsque vous utilisez l’expérience de test dans le portail Azure.
- Reproduisez l’erreur à l’aide de l’expérience de test sur la page de configuration d’application du portail Azure. En savoir plus sur le [débogage d’applications avec authentification unique SAML](../azuread-dev/howto-v1-debug-saml-sso-issues.md)

## <a name="application-not-found-in-directory"></a>Application introuvable dans le répertoire

*Erreur AADSTS70001 : L’application associée à l’identificateur `https://contoso.com` est introuvable dans le répertoire*.

**Cause possible**

L’attribut d’émetteur envoyé de l’application vers Azure AD dans la demande SAML ne correspond pas à la valeur de l’identificateur configurée dans l’application Azure AD.

**Résolution :**

Vérifiez que l’attribut `Issuer` de la requête SAML correspond à la valeur de l’identificateur configurée dans Azure AD. Si vous utilisez l’[expérience de test](../azuread-dev/howto-v1-debug-saml-sso-issues.md) dans le portail Azure avec l’extension de navigateur sécurisée Mes applications, vous n’avez pas besoin de suivre ces étapes manuellement.

1. Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2. Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3. Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4. Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5. Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

   * Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6. Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7. Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8. Une fois l’application chargée, ouvrez **Configuration SAML de base**. Vérifiez que la valeur située dans la zone de texte de l’identificateur correspond à celle de l’identificateur mentionnée dans l’erreur.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>L’adresse de réponse ne correspond pas aux adresses de réponse configurées pour l’application. 

*Erreur AADSTS50011 : L’adresse de réponse `https://contoso.com` ne correspond pas aux adresses de réponse configurées pour l’application* 

**Cause possible** 

La valeur AssertionConsumerServiceURL dans la demande SAML ne correspond pas à la valeur de l’URL de réponse ou au modèle configuré dans Azure AD. La valeur AssertionConsumerServiceURL dans la demande SAML est l’URL que vous voyez dans l’erreur. 

**Résolution :** 

Vérifiez que l’attribut `Issuer` de la requête SAML correspond à la valeur de l’identificateur configurée dans Azure AD. Si vous utilisez l’[expérience de test](../azuread-dev/howto-v1-debug-saml-sso-issues.md) dans le portail Azure avec l’extension de navigateur sécurisée Mes applications, vous n’avez pas besoin de suivre ces étapes manuellement.
 
1. Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**. 

2. Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche. 

3. Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**. 

4. Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory. 

5. Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications. 

   * Si l’application que vous recherchez n’apparaît pas ici, utilisez le contrôle **Filtrer** en haut de la liste **Toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.
  
6. Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7. Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

8. Une fois l’application chargée, ouvrez **Configuration SAML de base**. Vérifiez ou mettez à jour la valeur figurant dans la zone de texte URL de réponse pour qu’elle corresponde à la valeur `AssertionConsumerServiceURL` dans la requête SAML.    
    
Une fois que vous avez mis à jour la valeur de l’URL de réponse dans Azure AD et qu’elle correspond à celle envoyée par l’application dans la requête SAML, vous devez être en mesure de vous connecter à l’application.

## <a name="user-not-assigned-a-role"></a>Utilisateur non affecté à un rôle

*Erreur AADSTS50105 : L’utilisateur connecté `brian\@contoso.com` n’est pas affecté à un rôle pour l’application*

**Cause possible**

L’utilisateur ne dispose pas des autorisations nécessaires pour accéder à l’application dans Azure AD.

**Résolution :**

Pour affecter un ou plusieurs utilisateurs directement à une application, effectuez les étapes suivantes. Si vous utilisez l’[expérience de test](../azuread-dev/howto-v1-debug-saml-sso-issues.md) dans le portail Azure avec l’extension de navigateur sécurisée Mes applications, vous n’avez pas besoin de suivre ces étapes manuellement.

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

12. **Facultatif :** Si vous souhaitez **ajouter plusieurs utilisateurs**, entrez un autre **nom complet** ou une autre **adresse de messagerie** dans la zone de recherche **Rechercher par nom ou adresse de messagerie**, puis cochez la case pour ajouter cet utilisateur à la liste **Sélectionné**.

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

Le fournisseur d’application doit confirmer sa prise en charge de l’implémentation SAML Azure AD pour l’authentification unique.

## <a name="misconfigured-application"></a>Application mal configurée

*Erreur AADSTS650056 : Application mal configurée. La raison peut être l’une des suivantes : Le client n’a listé aucune autorisation parmi les autorisations nécessaires dans l’inscription d’application du client. Ou l’administrateur n’a pas donné son consentement dans le locataire. Vous pouvez aussi vérifier l’identificateur d’application dans la requête pour vous assurer qu’il correspond à l’identificateur d’application cliente configuré. Contactez votre administrateur pour corriger la configuration ou donner un consentement au nom du locataire.* .

**Cause possible**

L’attribut `Issuer` envoyé de l’application vers Azure AD dans la requête SAML ne correspond pas à la valeur de l’identificateur configurée pour l’application dans Azure AD.

**Résolution :**

Vérifiez que l’attribut `Issuer` de la requête SAML correspond à la valeur de l’identificateur configurée dans Azure AD. Si vous utilisez l’[expérience de test](../azuread-dev/howto-v1-debug-saml-sso-issues.md) dans le portail Azure avec l’extension de navigateur sécurisée Mes applications, vous n’avez pas besoin de suivre ces étapes manuellement :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

1.  Ouvrez l’**extension Azure Active Directory** en sélectionnant **Tous les services** en haut du menu de navigation principal de gauche.

1.  Entrez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

1.  Sélectionnez **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

1.  Sélectionnez **Toutes les applications** pour afficher la liste complète de vos applications.

    Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.

1.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

1.  Une fois l’application chargée, ouvrez **Configuration SAML de base**. Vérifiez que la valeur située dans la zone de texte de l’identificateur correspond à celle de l’identificateur mentionnée dans l’erreur.

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

Azure AD n'a pas pu identifier la requête SAML dans les paramètres URL de la requête HTTP. Cela peut se produire si l’application n’utilise pas la liaison de redirection HTTP pour l’envoi de la requête SAML vers Azure AD.

**Résolution :**

L’application doit envoyer la requête SAML encodée dans l’en-tête d’emplacement à l’aide de la liaison de redirection HTTP. Pour plus d'informations sur la mise en œuvre, lisez la section Liaison de redirection HTTP dans le [document de spécification du protocole SAML](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD envoie le jeton vers un point de terminaison incorrect

**Cause possible**

Lors de l’authentification unique, si la requête de connexion ne contient pas une URL de réponse explicite (URL Assertion Consumer Service), Azure AD sélectionne l’une des URL de réponse configurées pour cette application. Même si l’application possède une URL de réponse explicite configurée, l’utilisateur peut être redirigé vers https://127.0.0.1:444. 

Lorsque l’application a été ajoutée comme ne figurant pas sur galerie, Azure Active Directory a créé cette URL de réponse en tant que valeur par défaut. Ce comportement a changé et Azure Active Directory n’ajoute plus cette URL par défaut. 

**Résolution :**

Supprimez les URL de réponse non utilisées qui sont configurées pour l’application.

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

2.  Ouvrez l’**extension Azure Active Directory** en sélectionnant **Tous les services** en haut du menu de navigation principal de gauche.

3.  Entrez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Sélectionnez **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Sélectionnez **Toutes les applications** pour afficher la liste complète de vos applications.

    Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.

6.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

7.  Une fois l’application chargée, ouvrez **Configuration SAML de base**. Dans **URL de réponse (URL Assertion Consumer Service)** , supprimez les URL de réponse non utilisées ou par défaut que le système a créées. Par exemple : `https://127.0.0.1:444/applications/default.aspx`.



## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problème lors de la personnalisation des revendications SAML envoyées à une application

Pour savoir comment personnaliser les revendications d’attribut SAML envoyées à votre application, consultez [Claims mapping in Azure Active Directory (public preview)](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) (Mappage de revendications dans Azure Active Directory [préversion]) pour plus d’informations.

## <a name="next-steps"></a>Étapes suivantes
[Spécifications du protocole SAML d’authentification unique Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)
