---
title: Proxy d’application Azure Active Directory et Tableau | Microsoft Docs
description: Découvrez comment utiliser Proxy d’application Azure Active Directory (Azure AD) pour fournir un accès à distance à votre déploiement de Tableau.  .
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 7fa8654e413ac337994d35afdce7bd3478127f64
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34070870"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Proxy d’application Azure Active Directory et Tableau 

Proxy d’application Azure Active Directory et Tableau se sont associés afin de vous permettre d’utiliser facilement Proxy d’application pour fournir un accès à distance à votre déploiement Tableau. Cet article explique comment configurer ce scénario.  

## <a name="prerequisites"></a>Prérequis
 

Le scénario décrit dans cet article suppose ce qui suit :

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) est configuré. 

- Vous avez installé un [connecteur Proxy d’application](manage-apps/application-proxy-enable.md). 

 

## <a name="enabling-application-proxy-for-tableau"></a>Activation de Proxy d’application pour Tableau 

Pour utiliser Proxy d’application pour Tableau, vous avez besoin d’envoyer un e-mail à [aadapfeedback@microsoft.com](mailto:aadapfeedback@microsoft.com) afin de faire activer ce scénario.
Dans votre e-mail :

-   Utilisez Activer Proxy d’application pour Tableau en tant qu’objet.
-   Ajoutez votre ID de locataire dans le corps.    

Vous obtenez une confirmation quand vous êtes prêt à utiliser l’application. En règle générale, cela prend environ une semaine. Toutefois, vous pouvez terminer les configurations en attendant cette confirmation.


 

## <a name="publish-your-applications-in-azure"></a>Publier vos applications dans Azure 

Pour publier Tableau, vous devez publier une application dans le portail Azure.

Pour :

- des instructions détaillées des étapes 1 à 8, consultez [Publication d’applications à l’aide du proxy d’application Azure AD](manage-apps/application-proxy-publish-azure-portal.md) ; 
- des informations sur la façon de rechercher des valeurs Tableau pour des champs Proxy d’application, consultez la documentation de Tableau.  

**Pour publier votre application** : 


1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur. 

2. Sélectionnez **Azure Active Directory > Applications d’entreprise**. 

3. Sélectionnez **Ajouter** en haut du panneau. 

4. Sélectionnez **Application locale**. 

5. Saisissez les informations concernant votre nouvelle application dans les champs requis. Suivez les conseils ci-dessous pour les paramètres : 

    - **URL interne** : cette application doit avoir une URL interne qui est l’URL Tableau proprement dite. Par exemple : `https://adventure-works.tableau.com`. 

    - **Méthode de pré-authentification** : Azure Active Directory (recommandée mais pas obligatoire). 

6. Sélectionnez **Ajouter** en haut du panneau. Votre application est ajoutée et le menu de démarrage rapide s’ouvre. 

7. Dans le menu de démarrage rapide, sélectionnez **Assigner un utilisateur à des fins de test** et ajoutez au moins un utilisateur à l’application. Vérifiez que ce compte de test a accès à l’application locale. 

8. Sélectionnez **Affecter** pour enregistrer l’affectation de l’utilisateur de test. 

9. (Facultatif) Dans la page de gestion de l’application, sélectionnez **Authentification unique**. Choisissez **Authentification Windows intégrée** dans le menu déroulant et renseignez les champs obligatoires en fonction de votre configuration de Tableau. Sélectionnez **Enregistrer**. 

 

## <a name="testing"></a>Test 

Votre application est maintenant prête à être testée. Accédez à l’URL externe que vous avez utilisée pour publier Tableau et connectez-vous en tant qu’utilisateur affecté aux deux applications.



## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Proxy d’application Azure AD, consultez [Offrir un accès à distance sécurisé aux applications locales](manage-apps/application-proxy.md).

