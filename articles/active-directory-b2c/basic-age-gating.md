---
title: Utilisation de la vérification de l’âge dans Azure AD B2C | Microsoft Docs
description: Découvrez comment identifier les mineurs à l’aide de votre application.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/29/2018
ms.author: davidmu
ms.openlocfilehash: 3d6804f7e546547d734f966656362111b31078a4
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206319"
---
#<a name="using-age-gating-in-azure-ad-b2c"></a>Utilisation de la vérification de l’âge dans Azure AD B2C

>[!IMPORTANT]
>Cette fonctionnalité est en préversion privée.  Consultez notre [blog consacré au service](https://blogs.msdn.microsoft.com/azureadb2c/) pour plus d’informations ou contactez AADB2CFeedback@microsoft.com.  Tant que cette fonctionnalité n’est pas disponible généralement, ne l’utilisez PAS dans les répertoires de production. Sinon, cela risque d’entraîner une perte de données, ainsi que des modifications inattendues du comportement.  
>

##<a name="age-gating"></a>Vérification de l’âge
La vérification de l’âge vous permet d’utiliser Azure AD B2C pour identifier les mineurs dans votre application.  Vous pouvez choisir de bloquer la connexion de l’utilisateur à l’application ou d’autoriser l’utilisateur à revenir dans l’application avec des revendications supplémentaires qui identifient son âge et l’état du consentement parental.  

>[!NOTE]
>Le consentement parental est suivi dans un attribut utilisateur appelé `consentProvidedForMinor`.  Vous pouvez mettre à jour cette propriété via l’API Graph qui utilisera ce champ lors de la mise à jour de `legalAgeGroupClassification`.
>

##<a name="setting-up-your-directory-for-age-gating"></a>Configuration de votre répertoire pour la vérification de l’âge
Pour utiliser la vérification de l’âge dans un flux d’utilisateur, vous devez configurer votre répertoire avec des propriétés supplémentaires. Cette opération peut être effectuée via `Properties` dans le menu (disponible uniquement dans la préversion publique).  
1. Dans l’extension Azure AD B2C, cliquez sur les **propriétés** de votre locataire dans le menu de gauche.
2. Sous la section **Vérification de l’âge**, cliquez sur le bouton **Configurer**.
3. Attendez que l’opération soit terminée. Votre répertoire est alors configuré pour la vérification de l’âge.

##<a name="enabling-age-gating-in-your-user-flow"></a>Activation de la vérification de l’âge dans votre flux d’utilisateur
Une fois que votre répertoire est configuré pour utiliser la vérification de l’âge, vous pouvez ensuite utiliser cette fonctionnalité dans les flux d’utilisateurs en préversion.  Cette fonctionnalité nécessite des modifications qui la rendent incompatible avec les types de flux d’utilisateurs existants.  Pour activer la vérification de l’âge, procédez comme suit :
1. Créez un flux d’utilisateur en préversion.
2. Une fois qu’il a été créé, accédez à **Propriétés** dans le menu.
3. Dans la section **Vérification de l’âge**, appuyez sur le bouton bascule pour activer la fonctionnalité.
4. Vous pouvez ensuite choisir la façon dont vous souhaitez gérer les utilisateurs qui s’identifient comme étant mineurs.

##<a name="what-does-enabling-age-gating-do"></a>Fonctionnement de la vérification de l’âge
Une fois que la vérification de l’âge est activée dans votre flux d’utilisateur, l’expérience utilisateur change.  Lors de la création d’un compte, les utilisateurs sont maintenant invités à entrer leur date de naissance et leur pays de résidence, ainsi que les attributs d’utilisateur configurés pour le flux d’utilisateur.  Lors de la connexion, les utilisateurs qui n’ont pas encore entré de date de naissance ni de pays de résidence doivent indiquer ces informations à la prochaine connexion.  À partir de ces deux valeurs, Azure AD B2C peut déterminer si l’utilisateur est mineur et mettre à jour le champ `ageGroup`. Les valeurs possibles sont : `null`, `Undefined`, `Minor`, `Adult` et `NotAdult`.  Les champs `ageGroup` et `consentProvidedForMinor` sont ensuite utilisés pour calculer `legalAgeGroupClassification`. 

##<a name="age-gating-options"></a>Options de vérification de l’âge
Vous pouvez demander à Azure AD B2C de bloquer les mineurs sans consentement parental ou de les autoriser en laissant l’application décider ce qu’elle fera de ces utilisateurs.  

###<a name="allowing-minors-without-parental-consent"></a>Autoriser les mineurs sans consentement parental
Pour les flux d’utilisateurs impliquant des opérations de création de compte et/ou de connexion, vous pouvez choisir d’autoriser les mineurs sans consentement dans votre application.  Les mineurs sans consentement parental sont autorisés à se connecter ou à créer un compte normalement et un jeton d’ID est émis avec la revendication `legalAgeGroupClassification`.  En utilisant cette revendication, vous pouvez choisir l’expérience de ces utilisateurs, par exemple la collecte du consentement parental (et la mise à jour du champ `consentProvidedForMinor`).

###<a name="blocking-minors-without-parental-consent"></a>Bloquer les mineurs sans consentement parental
Pour les flux d’utilisateurs impliquant des opérations de création de compte et/ou de connexion, vous pouvez choisir de bloquer les mineurs sans consentement dans votre application.  Il existe deux options pour la gestion des utilisateurs bloqués dans Azure AD B2C :
* Envoyez du code JSON à l’application : cette option envoie à l’application une réponse indiquant que le mineur a été bloqué.
* Affichez une page d’erreur : l’utilisateur voit une page l’informant qu’il ne peut pas accéder à l’application

##<a name="known-issues"></a>Problèmes connus
###<a name="customization-unavailable-for-new-pages"></a>Personnalisation non disponible pour les nouvelles pages
Il existe deux nouvelles pages qui peuvent être disponibles dans votre flux d’utilisateur lorsque vous activez la vérification de l’âge.  Les pages de collecte du pays et de la date de naissance lors de la connexion et la page d’erreur ne peuvent pas être utilisées avec la personnalisation de la langue ou de la mise en page.  Cette option sera disponible dans une prochaine mise à jour.

###<a name="format-for-the-response-when-a-minor-is-blocked"></a>Format de la réponse envoyée lorsqu’un mineur est bloqué.
Actuellement, la réponse n’est pas correctement mise en forme. Ce bogue sera résolu dans une prochaine mise à jour.

###<a name="deleting-specific-attributes-that-were-added-during-setup-can-make-your-directory-unable-to-use-age-gating"></a>Si vous supprimez des attributs spécifiques qui ont été ajoutés pendant la configuration, il se peut que votre répertoire ne puisse pas utiliser la vérification de l’âge.
Dans la configuration de la vérification de l’âge, vous avez configuré votre répertoire via une option dans votre `Properties`.  Si vous supprimez `legalCountry` ou `dateOfBirth`, votre locataire ne peut plus utiliser la vérification de l’âge et ces propriétés ne peuvent pas être recréées.

###<a name="list-of-countries-is-incomplete"></a>La liste des pays est incomplète
Actuellement, la liste des pays dans legalCountry est incomplète. Nous ajouterons le reste des pays dans une prochaine mise à jour.