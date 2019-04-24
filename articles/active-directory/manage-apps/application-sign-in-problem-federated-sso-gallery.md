---
title: Problèmes de connexion à une application de la galerie configurée pour l’authentification unique fédérée | Microsoft Docs
description: Instructions sur la manière de résoudre les problèmes rencontrés lors de la connexion à une application configurée pour l’authentification unique SAML fédérée avec Azure AD
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
ms.date: 02/18/2019
ms.author: celested
ms.reviewer: luleon, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 623d684f701df8b1a7c4b84a2bd3840f039ad174
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60292212"
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>Problèmes de connexion à une application de la galerie configurée pour l’authentification unique fédérée

Pour résoudre les problèmes de connexion ci-dessous, nous vous recommandons de que suivre ces suggestion pour obtenir le meilleur diagnostic et d’automatiser les étapes de résolution :

- Installer le [mon Extension de navigateur sécuriser les applications](access-panel-extension-problem-installing.md) pour aider à Azure Active Directory (Azure AD) pour fournir le meilleur diagnostic et résolutions lorsque vous utilisez le test rencontrer dans le portail Azure.
- Reproduisez l’erreur à l’aide de l’expérience de test dans la page de configuration d’application dans le portail Azure. En savoir plus sur [basée sur SAML de déboguer des applications d’authentification uniques](../develop/howto-v1-debug-saml-sso-issues.md)


## <a name="application-not-found-in-directory"></a>Application introuvable dans le répertoire

*Erreur AADSTS70001 : Application avec l’identificateur « https :\//contoso.com » est introuvable dans le répertoire*.

**Cause possible**

Le `Issuer` attribut envoyé à partir de l’application à Azure AD dans la demande SAML ne correspond pas à la valeur d’identificateur qui est configurée pour l’application dans Azure AD.

**Résolution :**

Vérifiez que le `Issuer` attribut dans la demande SAML correspond à la valeur d’identificateur configurée dans Azure AD. Si vous utilisez le [test expérience](../develop/howto-v1-debug-saml-sso-issues.md) dans le portail Azure avec l’Extension mon navigateur de sécuriser les applications, vous n’avez pas besoin manuellement, suivez ces étapes.

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

1.  Ouvrez le **Extension Azure Active Directory** en sélectionnant **tous les services** en haut du menu principal de navigation de gauche.

1.  Type **« Azure Active Directory »** dans la zone de recherche de filtre, puis sélectionnez le **Azure Active Directory** élément.

1.  Sélectionnez **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

1.  Sélectionnez **Toutes les applications** pour afficher la liste complète de vos applications.

    Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.

1.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

1.  Une fois l’application chargée, ouvrez **configuration SAML base**. Vérifiez que la valeur dans la zone de texte identificateur correspond à la valeur de la valeur d’identificateur mentionnée dans l’erreur.



## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>L’adresse de réponse ne correspond pas à l’adresse de réponse configurée pour l’application

*Erreur AADSTS50011 : L’adresse de réponse « https :\//contoso.com' ne correspond pas à des adresses de réponse configurées pour l’application*

**Cause possible**

La `AssertionConsumerServiceURL` valeur dans la demande SAML ne correspond pas à la valeur de l’URL de réponse ou d’un modèle configuré dans Azure AD. La `AssertionConsumerServiceURL` valeur dans la demande SAML est l’URL que vous voyez dans l’erreur.

**Résolution :**

Vérifiez que le `AssertionConsumerServiceURL` valeur dans la demande SAML correspond à la valeur de l’URL de réponse configurée dans Azure AD. Si vous utilisez le [test expérience](../develop/howto-v1-debug-saml-sso-issues.md) dans le portail Azure avec l’Extension mon navigateur de sécuriser les applications, vous n’avez pas besoin manuellement, suivez ces étapes.

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

1.  Ouvrez le **Extension Azure Active Directory** en sélectionnant **tous les services** en haut du menu principal de navigation de gauche.

1.  Type **« Azure Active Directory »** dans la zone de recherche de filtre, puis sélectionnez le **Azure Active Directory** élément.

1.  Sélectionnez **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

1.  Sélectionnez **Toutes les applications** pour afficher la liste complète de vos applications.

    Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.

1.  Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

1.  Une fois l’application chargée, ouvrez **configuration SAML base**. Vérifier ou mettre à jour la valeur dans la zone de texte URL de réponse pour faire correspondre la `AssertionConsumerServiceURL` valeur dans la demande SAML.    
    
Une fois que vous avez mis à jour la valeur de l’URL de réponse dans Azure AD, et il correspond à la valeur envoyée par l’application dans la demande SAML, il se peut que vous devez être en mesure de se connecter à l’application.

## <a name="user-not-assigned-a-role"></a>Utilisateur non affecté à un rôle

*Erreur AADSTS50105 : L’utilisateur connecté ' brian\@contoso.com' n’est pas affecté à un rôle d’application*.

**Cause possible**

L’utilisateur ne dispose pas des autorisations nécessaires pour accéder à l’application dans Azure AD.

**Résolution :**

Pour affecter un ou plusieurs utilisateurs à une application directement, suivez les étapes ci-dessous. Si vous utilisez le [test expérience](../develop/howto-v1-debug-saml-sso-issues.md) dans le portail Azure avec l’Extension mon navigateur de sécuriser les applications, vous n’avez pas besoin manuellement, suivez ces étapes.

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’administrateur général**.

1.  Ouvrez le **Extension Azure Active Directory** en sélectionnant **tous les services** en haut du menu principal de navigation de gauche.

1.  Type **« Azure Active Directory**» dans la zone de recherche de filtre, puis sélectionnez le **Azure Active Directory** élément.

1.  Sélectionnez **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

1.  Sélectionnez **Toutes les applications** pour afficher la liste complète de vos applications.

    Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.

1.  Dans la liste des applications, sélectionnez celui que vous souhaitez affecter un utilisateur.

1.  Une fois l’application chargée, sélectionnez **utilisateurs et groupes** à partir du menu de navigation de gauche de l’application.

1.  Cliquez sur le bouton **Ajouter** en haut de la liste **Utilisateurs et groupes** pour ouvrir le volet **Ajouter une attribution**.

1.  Sélectionnez le sélecteur **Utilisateurs et groupes** à partir du volet **Ajouter une attribution**.

1. Dans le **recherche par nom ou adresse e-mail** zone de recherche, tapez le nom complet ou adresse e-mail de l’utilisateur que vous souhaitez ajouter.

1. Pointez sur **l’utilisateur** dans la liste pour afficher une **case à cocher**. Cliquez sur la case à cocher en regard de la photo de profil ou le logo pour ajouter l’utilisateur à l’utilisateur le **sélectionnés** liste.

1. **Facultatif** : Si vous souhaitez **ajouter plusieurs utilisateurs**, tapez un autre nom complet ou une adresse dans e-mail le **recherche par nom ou adresse e-mail** zone de recherche, puis cliquez sur la case à cocher pour ajouter l’utilisateur à la **sélectionnés**  liste.

1. Lorsque vous avez fini de sélectionner les utilisateurs, cliquez sur le **sélectionnez** pour les ajouter à la liste des utilisateurs et groupes à affecter à l’application.

1. **Facultatif** : Cliquez sur le **sélectionner un rôle** sélecteur dans la **ajouter une attribution** volet pour sélectionner un rôle à affecter aux utilisateurs que vous avez sélectionné.

1. Cliquez sur le bouton **Attribuer** pour affecter l’application aux utilisateurs sélectionnés.

Après une courte période de temps, les utilisateurs que vous avez sélectionné sera en mesure de lancer ces applications à l’aide des méthodes décrites dans la section de description de solution.

## <a name="not-a-valid-saml-request"></a>Demande SAML non valide

*Erreur AADSTS75005 : La requête n’est pas un message de protocole Saml2 valide.*

**Cause possible**

Azure AD ne prend pas en charge la demande SAML envoyée par l’application pour l’authentification unique. Voici certains problèmes courants :

-   Des champs obligatoires sont manquants dans la demande SAML
-   La méthode de demande SAML encodée

**Résolution :**

1. Capturer la demande SAML. Suivez le didacticiel [comment déboguer basée sur SAML SSO aux applications dans Azure AD](../develop/howto-v1-debug-saml-sso-issues.md) pour savoir comment capturer la demande SAML.

1. Contactez le fournisseur de l’application et de partager les informations suivantes :

   -   Demande SAML

   -   [Spécifications du protocole SAML d’authentification unique Azure AD](../develop/single-sign-on-saml-protocol.md)

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

*Erreur AADSTS50003 : Aucune clé de signature configurée.*

**Cause possible**

L’objet d’application est endommagé et Azure AD ne reconnaît pas le certificat configuré pour l’application.

**Résolution :**

Pour supprimer et créer un nouveau certificat, effectuez les étapes suivantes :

1. Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général** ou que **Coadministrateur**.

1. Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

1. Type **« Azure Active Directory »** dans la zone de recherche de filtre, puis sélectionnez le **Azure Active Directory** élément.

1. Sélectionnez **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

1. Sélectionnez **Toutes les applications** pour afficher la liste complète de vos applications.

    Si l’application que vous recherchez ne figure pas dans la liste, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications**.

1. Sélectionnez l’application pour laquelle vous souhaitez configurer l’authentification unique.

1. Une fois l’application chargée, cliquez sur **Authentification unique** dans le menu de navigation de gauche de l’application.

1. Sélectionnez **créer un certificat** sous le **certificat de signature SAML** section.

1. Sélectionnez la date d’Expiration, puis cliquez **enregistrer**.

1. Cochez la case **Activer le nouveau certificat** pour substituer le certificat actif. Ensuite, cliquez sur **Enregistrer** en haut du volet, puis acceptez d’activer le certificat de substitution.

1. Dans la section **Certificat de signature SAML**, cliquez sur **Supprimer** pour supprimer le certificat **Inutilisé**.

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

Pour savoir comment personnaliser les revendications d’attribut SAML envoyées à votre application, consultez [mappage des revendications dans Azure Active Directory](../develop/active-directory-claims-mapping.md).

## <a name="next-steps"></a>Étapes suivantes

[Comment déboguer une authentification unique SAML pour des applications dans Azure AD](../develop/howto-v1-debug-saml-sso-issues.md)
