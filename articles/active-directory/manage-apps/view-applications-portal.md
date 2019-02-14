---
title: Afficher les applications du tenant - Azure Active Directory | Microsoft Docs
description: Utilisez le portail Azure pour afficher les applications dans votre tenant Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/25/2018
ms.author: celested
ms.reviewer: arvinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec039beecdcfa6dacd19e5910209c58f13543fce
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56199882"
---
# <a name="view-your-azure-active-directory-tenant-applications"></a>Afficher les application du tenant Azure Active Directory

Ce démarrage rapide utilise le portail Azure pour afficher les applications dans votre tenant Azure Active Directory (Azure AD).

## <a name="before-you-begin"></a>Avant de commencer

Pour afficher des résultats, vous devez avoir au moins une application dans votre tenant Azure AD. Pour ajouter une application, consultez le démarrage rapide [d’ajout d’une application](add-application-portal.md).

Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général pour votre tenant Azure AD, administrateur d’application cloud ou administrateur d’application.

## <a name="find-the-list-of-tenant-applications"></a>Trouver la liste des applications du tenant

Vos applications de tenant Azure AD sont visibles dans la section **Applications d’entreprise** du portail Azure.

Pour rechercher les applications de votre tenant :

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur **Azure Active Directory**. 

2. Dans le panneau Azure Active Directory, cliquez sur **Applications d’entreprise**. 

3. À partir du menu déroulant **Type d’Application**, sélectionnez **Toutes les Applications**, puis cliquez sur **Appliquer**. Un échantillon aléatoire des applications de votre tenant s’affiche.

    ![Applications Entreprise](media/view-applications-portal/open-enterprise-apps.png)
   
4. Pour afficher davantage d’applications, cliquez sur **Afficher plus** en bas de la liste. Selon le nombre d’applications de votre tenant, il peut être plus facile de [rechercher une application particulière](#search-for-a-tenant-application) que de faire défiler la liste.

## <a name="select-viewing-options"></a>Sélectionner les options d'affichage

Dans cette section, sélectionnez les options en fonction de ce que vous recherchez.

1. Vous pouvez afficher les applications en fonction des options pour **Type d’Application**, **État de l’Application**, et **Visibilité de l’Application**. 

    ![Options de recherche](media/view-applications-portal/search-options.png)

2. Sous **Type d’Application**, choisissez l’une des options suivantes :

    - **Applications d’entreprise** montre les applications non Microsoft.
    - **Applications Microsoft** montre les applications Microsoft.
    - **Toutes les Applications** montre les applications non Microsoft et Microsoft.

3. Sous **État de l’Application**, choisissez **Quelconque**, **Désactivé**, ou **Activé**. L’option **Quelconque** inclut les applications activées et désactivées.

4. Sous **Visibilité de l’Application**, choisissez **Quelconque** ou **Masqué**. L’option **Masqué** affiche les applications qui sont dans le tenant, mais ne sont pas visibles pour les utilisateurs.

5. Après avoir choisi les options souhaitées, cliquez sur **Appliquer**.
 

## <a name="search-for-a-tenant-application"></a>Rechercher une application de tenant

Pour rechercher une application particulière :

1. À partir du menu déroulant **Type d’Application**, sélectionnez **Toutes les Applications**, puis cliquez sur **Appliquer**.

2. Entrez le nom de l’application recherchée. Si l’application a été ajoutée à votre tenant Azure AD, elle apparaît dans les résultats de recherche. Cet exemple montre que GitHub n’a pas été ajouté aux applications du tenant.

    ![Rechercher une application](media/view-applications-portal/search-for-tenant-application.png)

3. Essayez d’entrer les premières lettres du nom de l’application.  Cet exemple montre toutes les applications qui commencent par **Sales**.

    ![Recherche avec un préfixe](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris comment afficher les applications dans votre tenant Azure AD et filtrer la liste des applications par type d’application, état et visibilité. Vous avez également appris comment rechercher une application particulière.

Maintenant que vous avez trouvé l’application que vous cherchiez, vous pouvez continuer à [ajouter d’autres applications à votre tenant](add-application-portal.md), ou cliquer sur l’application pour afficher ou modifier les propriétés et les options de configuration. Vous pouvez par exemple configurer l’authentification unique. 

> [!div class="nextstepaction"]
> [Configurer l’authentification unique](configure-single-sign-on-portal.md)


