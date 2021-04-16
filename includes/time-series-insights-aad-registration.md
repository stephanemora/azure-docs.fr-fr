---
title: Fichier include
description: Fichier include
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 10/02/2020
ms.openlocfilehash: 22411e5a80f555a3ead05d39466a7a175923d9bc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105104967"
---
* Après avoir sélectionné la plateforme appropriée à l’étape 4 de la section [Configurer les paramètres de la plateforme,](../articles/active-directory/develop/quickstart-register-app.md#configure-platform-settings) configurez vos **URI de redirection** et **Jetons d’accès** dans le volet latéral situé à droite de l’interface utilisateur.

    * **L’URI de redirection** doit correspondre à l’adresse fournie par la requête d’authentification :

        * Pour les applications hébergées dans un environnement de développement local, sélectionnez **Client public (mobile et bureau)** . Veillez à affecter la valeur **Oui** à **Client public**.
        * Pour les applications monopages hébergées sur Azure App Service, sélectionnez **Web**.

    * Déterminez si une **URL de déconnexion** est appropriée.

    * Activez le flux d’octroi implicite en cochant les **Jeton d'accès** ou **Jetons d'ID**.

    [![Créer des URI de redirection](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    Cliquez sur **Configurer**, puis sur **Enregistrer**.

* Associez votre application Azure Active Directory à Azure Time Series Insights. Sélectionnez **Autorisations de l’API** > **Ajouter une autorisation** > **API que mon organisation utilise**.

    [![Associer une API à votre application Azure Active Directory](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Saisissez `Azure Time Series Insights` dans la barre de recherche, puis sélectionnez `Azure Time Series Insights`.

* Ensuite, spécifiez le genre d’autorisation d’API exigé par votre application. Par défaut, **Autorisations déléguées** est en surbrillance. Choisissez un type d’autorisation puis, sélectionnez **Ajouter les autorisations**.

    [![Spécifier le type d’autorisation d’API que votre application exige](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)

* [Ajoutez des informations d’identification](../articles/active-directory/develop/quickstart-register-app.md#add-credentials) si l’application doit appeler les API de votre environnement elle-même. Les informations d’identification permettent à votre application de s’authentifier de façon autonome, sans qu’aucune interaction utilisateur ne soit nécessaire au moment de l’exécution.