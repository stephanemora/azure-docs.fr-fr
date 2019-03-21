---
title: Ajouter une application client native- Azure Active Directory B2C | Microsoft Docs
description: Apprenez à ajouter une application client native à votre locataire Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 3f988e61c152be820e7e490e13908fb4ab54e6c5
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673390"
---
# <a name="add-a-native-client-application-to-your-azure-active-directory-b2c-tenant"></a>Ajouter une application client native à votre locataire Azure Active Directory B2C

Les ressources client natives doivent être inscrites dans votre locataire pour que votre application puisse communiquer avec lui via Azure Active Directory B2C.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Applications**, puis **Ajouter**.
2. Entrez un nom pour l’application. Par exemple, *nativeapp1*.
3. Pour **Inclure une application/API web**, sélectionnez **Non**.
4. Pour **Inclure un client natif**, sélectionnez **Oui**.
5. Pour l’**URI de redirection**, entrez un URI de redirection avec un schéma personnalisé. Deux points importants sont à prendre en compte quand vous choisissez un URI de redirection :

    - **Unique** : Le schéma de l’URI de redirection doit être unique pour chaque application. Dans l’exemple `com.onmicrosoft.contoso.appname://redirect/path`, `com.onmicrosoft.contoso.appname` est le schéma. Ce modèle doit être suivi. Si deux applications partagent le même schéma, l’utilisateur doit choisir une application. Si l’utilisateur fait un choix inapproprié, la connexion échoue.
    - **Complet** : L’URI de redirection doit avoir un schéma et un chemin. Le chemin d’accès doit contenir au moins une barre oblique après le domaine. Par exemple, `//contoso/` fonctionne et `//contoso` échoue. Veillez à ce que l’URI de redirection n’ait pas de caractères spéciaux comme des traits de soulignement.

6. Cliquez sur **Créer**.
7. Dans la page des propriétés, enregistrez l’ID d’application que vous utiliserez pour configurer votre application client native.
