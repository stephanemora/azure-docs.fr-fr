---
title: Prérequis pour accéder à l’API de création de rapports Azure Active Directory | Microsoft Docs
description: En savoir plus sur la configuration requise pour accéder à l’API de création de rapports Azure AD
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 6d842b1af74c1b276f367e0ff15703880f7560aa
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36224784"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Prérequis pour accéder à l’API de création de rapports Azure Active Directory

Les [API de création de rapports Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) vous fournissent un accès par programmation aux données par le biais d’un ensemble d’API REST. Vous pouvez appeler ces API à partir de divers outils et langages de programmation.

L’API de création de rapports utilise [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) pour autoriser l’accès aux API web.

Pour préparer votre accès à l’API de création de rapports, vous avez besoin d’effectuer ce qui suit :

1. Attribuer des rôles
2. Inscrire une application
3. Accorder des autorisations
4. Rassembler les paramètres de configuration



## <a name="assign-roles"></a>Attribuer des rôles

Pour accéder aux données de rapports via l’API, vous devez avoir un des rôles suivants :

- Lecteur de sécurité

- Administrateur de la sécurité

- Administrateur général




## <a name="register-an-application"></a>Inscrire une application

Vous devez inscrire une application, même si vous accédez à l’API de création de rapports à l’aide d’un script. Vous obtenez ainsi un **ID d’application**, qui est requis pour un appel d’autorisation et permet à votre code de recevoir des jetons.

Pour configurer votre annuaire et lui permettre d’accéder à l’API de création de rapports Azure AD, vous devez vous connecter au portail Azure avec un compte d’administrateur Azure, également membre du rôle d’annuaire **Administrateur général** dans votre locataire Azure AD.

> [!IMPORTANT]
> Les applications qui s’exécutent sous les informations d’identification pourvues de privilèges d’administrateur de ce type peuvent être très puissantes. Veillez donc à sécuriser les informations d’identification d’ID/du secret de l’application.
> 


**Pour inscrire une application Azure Active Directory :**

1. Dans le volet de navigation gauche du [Portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory**.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Dans la page **Azure Active Directory**, cliquez sur **Inscriptions des applications**.

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/02.png) 

3. Dans la page **Inscriptions des applications**, dans la barre d’outils en haut, cliquez sur **Nouvelle inscription d’application**.

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/03.png)

4. Dans la page **Créer**, effectuez les étapes suivantes :

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/04.png)

    a. Dans la zone de texte **Nom**, tapez `Reporting API application`.

    b. Sous **Type d’application**, sélectionnez **Application web/API**.

    c. Dans la zone de texte **URL d’authentification**, tapez `https://localhost`.

    d. Cliquez sur **Créer**. 


## <a name="grant-permissions"></a>Accorder des autorisations 

En fonction de l’API à laquelle vous voulez accéder, vous devez accorder les autorisations suivantes à votre application :  

| API | Autorisation |
| --- | --- |
| Microsoft Azure Active Directory | Lire les données du répertoire |
| Microsoft Graph | Lire toutes les données du journal d’audit |


![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/36.png)


La section suivante répertorie les étapes pour les deux API. Si vous ne voulez pas accéder à l’une des API, vous pouvez ignorer les étapes qui s’y rapportent.
 

**Pour accorder des autorisations d’utiliser les API à votre application :**

1. Dans la page **Inscriptions des applications**, dans la liste des applications, cliquez sur **Application API de création de rapports**.

2. Dans la page **Application API de création de rapports**, dans la barre d’outils en haut, cliquez sur **Paramètres**. 

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

3. Dans la page **Paramètres**, cliquez sur **Autorisations requises**. 

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/06.png)

4. Dans la page **Autorisations requises**, cliquez sur la liste **API**, puis sur **Microsoft Azure Active Directory**. 

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/07.png)

5. Dans la page **Activer l’accès**, cochez **Lire les données de l’annuaire** et décochez **Connecter et lire le profil utilisateur**. 

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/08.png)

6. Dans la barre d’outils située en haut, cliquez sur **Enregistrer**.

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/15.png)

7. Dans la page **Autorisations requises**, dans la barre d’outils en haut, cliquez sur **Ajouter**.

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/32.png)

8. Dans la page **Ajouter un accès d’API**, cliquez sur **Sélectionner une API**.

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/31.png)

9. Dans la page **Sélectionner une API**, cliquez sur **Microsoft Graph**, puis sur **Sélectionner**.

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/33.png)

10. Dans la page **Activer l’accès**, sélectionnez **Lire toutes les données du journal d’audit**, puis cliquez sur **Sélectionner**.  

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/34.png)


11. Dans la page **Ajouter un accès d’API**, cliquez sur **Terminé**.  

12. Dans la page **Autorisations requises**, dans la barre d’outils en haut, cliquez sur **Accorder des autorisations**, puis sur **Oui**.

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/17.png)


## <a name="gather-configuration-settings"></a>Rassembler les paramètres de configuration 

Cette section vous montre comment obtenir les paramètres suivants à partir de votre annuaire :

- Nom de domaine
- ID client
- Clé secrète client

Ces valeurs sont nécessaires lors de la configuration des appels à l’API de création de rapports. 

### <a name="get-your-domain-name"></a>Obtenir votre nom de domaine

**Pour obtenir votre nom de domaine :**

1. Dans le volet de navigation gauche du [Portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory**.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Dans la page **Azure Active Directory**, cliquez sur **Noms de domaine personnalisés**.

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/09.png) 

3. Copiez votre nom de domaine à partir de la liste des domaines.


### <a name="get-your-applications-client-id"></a>Obtenir l’ID client de l’application

**Pour obtenir l’ID client de votre application :**

1. Dans le volet de navigation gauche du [Portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory**.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Dans la page **Inscriptions des applications**, dans la liste des applications, cliquez sur **Application API de création de rapports**.

3. Dans la page **Application API de création de rapports**, dans **ID d’application**, cliquez sur **Cliquer pour copier**.

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/11.png) 



### <a name="get-your-applications-client-secret"></a>Obtenir la clé secrète client de l’application
Pour obtenir la clé secrète client de l’application, vous devez créer une nouvelle clé et enregistrer sa valeur lors de l’enregistrement de la nouvelle clé car il est impossible de récupérer cette valeur ultérieurement.

**Pour obtenir la clé secrète client de l’application :**

1. Dans le volet de navigation gauche du [Portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory**.
   
    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. Dans la page **Inscriptions des applications**, dans la liste des applications, cliquez sur **Application API de création de rapports**.


3. Dans la page **Application API de création de rapports**, dans la barre d’outils en haut, cliquez sur **Paramètres**. 

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

4. Dans la page **Paramètres**, dans la section **Accès APIR**, cliquez sur **Clés**. 

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/12.png)


5. Dans la page **Clés**, effectuez les étapes suivantes :

    ![Inscription de l’application](./media/active-directory-reporting-api-prerequisites-azure-portal/14.png)

    a. Dans la zone de texte **Description**, tapez `Reporting API`.

    b. Dans **Expire le**, sélectionnez **Dans 2 ans**.

    c. Cliquez sur **Enregistrer**.

    d. Copiez la valeur de la clé.


## <a name="next-steps"></a>Étapes suivantes

- [Obtenir des données à l’aide de l’API de création de rapports Azure Active Directory avec des certificats](active-directory-reporting-api-with-certificates.md)

- [Première impression des API de création de rapports](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [Créer votre propre solution](active-directory-reporting-api-getting-started-azure-portal.md#customize)

