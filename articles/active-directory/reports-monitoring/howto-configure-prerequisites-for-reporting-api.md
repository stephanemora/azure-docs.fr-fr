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
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 03acd7c283fd1296af06dd19d0170a4b3c65eeb3
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352493"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Prérequis pour accéder à l’API de création de rapports Azure Active Directory

Les [API de création de rapports Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) vous fournissent un accès par programmation aux données par le biais d’un ensemble d’API REST. Vous pouvez appeler ces API à partir de divers outils et langages de programmation.

L’API de création de rapports utilise [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) pour autoriser l’accès aux API web.

Pour préparer votre accès à l’API de création de rapports, vous avez besoin d’effectuer ce qui suit :

1. [Attribuer des rôles](#assign-roles)
2. [Inscrire une application](#register-an-application)
3. [Accorder des autorisations](#grant-permissions)
4. [Rassembler les paramètres de configuration](#gather-configuration-settings)

## <a name="assign-roles"></a>Attribuer des rôles

Pour accéder aux données de rapports via l’API, vous devez avoir un des rôles suivants :

- Lecteur de sécurité

- Security Administrator

- Administrateur général


## <a name="register-an-application"></a>Inscrire une application

Vous devez inscrire une application, même si vous accédez à l’API de création de rapports à l’aide d’un script. Vous obtenez ainsi un **ID d’application**, qui est requis pour les appels d’autorisation et qui permet à votre code de recevoir des jetons.

Pour configurer votre annuaire et lui permettre d’accéder à l’API de création de rapports Azure AD, vous devez vous connecter au [portail Azure](https://portal.azure.com) avec un compte d’administrateur Azure, également membre du rôle d’annuaire **Administrateur général** dans votre locataire Azure AD.

> [!IMPORTANT]
> Les applications qui s’exécutent sous les informations d’identification pourvues de privilèges d’administrateur peuvent être très puissantes. Veillez donc à stocker les informations d’identification d’ID et du secret de l’application dans un emplacement sécurisé.
> 

**Pour inscrire une application Azure AD :**

1. Dans le panneau de navigation gauche du [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**.
   
    ![Inscription de l’application](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Dans la page **Azure Active Directory**, sélectionnez **Inscriptions des applications**.

    ![Inscription de l’application](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Dans la page **Inscriptions des applications**, sélectionnez **Nouvelle inscription d’application**.

    ![Inscription de l’application](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. Dans la page **Créer**, effectuez les étapes suivantes :

    ![Inscription de l’application](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. Dans la zone de texte **Nom**, tapez `Reporting API application`.

    b. Sous **Type d’application**, sélectionnez **Application web/API**.

    c. Dans la zone de texte **URL d’authentification**, tapez `https://localhost`.

    d. Sélectionnez **Créer**. 


## <a name="grant-permissions"></a>Accorder des autorisations 

En fonction de l’API à laquelle vous voulez accéder, vous devez accorder les autorisations suivantes à votre application :  

| API | Autorisation |
| --- | --- |
| Microsoft Azure Active Directory | Lire les données du répertoire |
| Microsoft Graph | Lire toutes les données du journal d’audit |


![Inscription de l’application](./media/howto-configure-prerequisites-for-reporting-api/36.png)

La section suivante répertorie les étapes pour les deux API. Si vous ne voulez pas accéder à l’une des API, vous pouvez ignorer les étapes qui s’y rapportent.

**Pour accorder des autorisations d’utiliser les API à votre application :**

1. Sélectionnez votre application dans la page **Inscriptions des applications** et sélectionnez **Paramètres**. 

    ![Inscription de l’application](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Dans la page **Paramètres**, sélectionnez **Autorisations requises**. 

    ![Inscription de l’application](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. Dans la page **Autorisations requises**, cliquez sur la liste **API**, puis sur **Microsoft Azure Active Directory**. 

    ![Inscription de l’application](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. Dans la page **Activer l’accès**, cochez **Lire les données de l’annuaire** et décochez **Connecter et lire le profil utilisateur**. 

    ![Inscription de l’application](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Dans la barre d’outils située en haut, cliquez sur **Enregistrer**.

    ![Inscription de l’application](./media/howto-configure-prerequisites-for-reporting-api/15.png)

6. Dans la page **Autorisations requises**, dans la barre d’outils en haut, cliquez sur **Ajouter**.

    ![Inscription de l’application](./media/howto-configure-prerequisites-for-reporting-api/32.png)

7. Dans la page **Ajouter un accès d’API**, cliquez sur **Sélectionner une API**.

    ![Inscription de l’application](./media/howto-configure-prerequisites-for-reporting-api/31.png)

8. Dans la page **Sélectionner une API**, cliquez sur **Microsoft Graph**, puis sur **Sélectionner**.

    ![Inscription de l’application](./media/howto-configure-prerequisites-for-reporting-api/33.png)

9. Dans la page **Activer l’accès**, sélectionnez **Lire toutes les données du journal d’audit**, puis cliquez sur **Sélectionner**.  

    ![Inscription de l’application](./media/howto-configure-prerequisites-for-reporting-api/34.png)

10. Dans la page **Ajouter un accès d’API**, cliquez sur **Terminé**.  

11. Dans la page **Autorisations requises**, dans la barre d’outils en haut, cliquez sur **Accorder des autorisations**, puis sur **Oui**.

    ![Inscription de l’application](./media/howto-configure-prerequisites-for-reporting-api/17.png)


## <a name="gather-configuration-settings"></a>Rassembler les paramètres de configuration 

Cette section vous montre comment obtenir les paramètres suivants à partir de votre annuaire :

- Nom de domaine
- ID client
- Clé secrète client

Ces valeurs sont nécessaires lors de la configuration des appels à l’API de création de rapports. 

### <a name="get-your-domain-name"></a>Obtenir votre nom de domaine

**Pour obtenir votre nom de domaine :**

1. Dans le panneau de navigation gauche du [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**.
   
    ![Inscription de l’application](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Dans la page **Azure Active Directory**, sélectionnez **Noms de domaine personnalisés**.

    ![Inscription de l’application](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Copiez votre nom de domaine à partir de la liste des domaines.


### <a name="get-your-applications-client-id"></a>Obtenir l’ID client de l’application

**Pour obtenir l’ID client de votre application :**

1. Dans le volet de navigation gauche du [Portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory**.
   
    ![Inscription de l’application](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Sélectionnez votre application dans la page **Inscriptions des applications**.

3. Dans la page de l’application, accédez à **ID d’application** et sélectionnez **Cliquer pour copier**.

    ![Inscription de l’application](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Obtenir la clé secrète client de l’application
Pour obtenir la clé secrète client de l’application, vous devez créer une nouvelle clé et enregistrer sa valeur lors de l’enregistrement de la nouvelle clé car il est impossible de récupérer cette valeur ultérieurement.

**Pour obtenir la clé secrète client de l’application :**

1. Dans le volet de navigation gauche du [Portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory**.
   
    ![Inscription de l’application](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Sélectionnez votre application dans la page **Inscriptions des applications**.

3. Dans la page Application, dans la barre d’outils en haut, cliquez sur **Paramètres**. 

    ![Inscription de l’application](./media/howto-configure-prerequisites-for-reporting-api/05.png)

4. Dans la page **Paramètres**, dans la section **Accès API**, cliquez sur **Clés**. 

    ![Inscription de l’application](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Dans la page **Clés**, effectuez les étapes suivantes :

    ![Inscription de l’application](./media/howto-configure-prerequisites-for-reporting-api/14.png)

    a. Dans la zone de texte **Description**, tapez `Reporting API`.

    b. Dans **Expire le**, sélectionnez **Dans 2 ans**.

    c. Cliquez sur **Enregistrer**.

    d. Copiez la valeur de la clé.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Résoudre les erreurs dans l'API de création de rapports

Cette section répertorie les messages d'erreur couramment rencontrés lors de l'accès aux rapports d'activité à l'aide de l'API MS Graph. Elle indique également les étapes à suivre pour les résoudre.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>Erreur interne du serveur HTTP 500 lors de l’accès au point de terminaison Microsoft Graph V2

Le point de terminaison Microsoft Graph v2 n’est pas pris en charge. Vous devez accéder aux journaux d’activité à l’aide du point de terminaison Microsoft Graph v1.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Error: Impossible d'obtenir les rôles d'utilisateur à partir d'AD Graph

Vous pouvez obtenir ce message d’erreur lorsque vous tentez d’accéder aux connexions à l’aide de l’Afficheur Graph. Vérifiez que vous êtes bien connecté à votre compte à l’aide des deux boutons de connexion de l’interface utilisateur de l’Afficheur Graph, comme illustré dans l’image suivante. 

![Afficheur Graph](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Error: Impossible de vérifier la licence premium à partir d'AD Graph 

Si vous rencontrez ce message d’erreur lorsque vous tentez d’accéder aux connexions à l’aide de l’Afficheur Graph, choisissez **Modifier les autorisations** sous votre compte, dans la barre de navigation gauche, puis sélectionnez **Tasks.ReadWrite** et **Directory.Read.All**. 

![Modifier l’interface utilisateur des autorisations](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Error: Aucun des locataires n'est B2C ou le locataire n'a pas de licence premium

L’accès aux rapports de connexion nécessite une licence Azure Active Directory Premium 1 (P1). Si vous voyez ce message d’erreur lorsque vous accédez aux connexions, vérifiez que votre locataire dispose bien d’une licence Azure AD P1.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Error: L'utilisateur ne dispose pas d'un rôle autorisé 

Si ce message d’erreur s’affiche quand vous tentez d’accéder aux journaux d’audit ou aux connexions à l’aide de l’API, vérifiez que votre compte est associé au rôle **Lecteur Sécurité** ou **Lecteur de rapport** dans votre locataire Azure Active Directory. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Error: L'application ne dispose pas de l'autorisation AAD « Lire les données de l'annuaire » 

Pour vérifier que votre application est exécutée avec le bon ensemble d’autorisations, suivez les étapes décrites dans les [Prérequis pour accéder à l’API de création de rapports Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md). 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Error: L'application ne dispose pas de l'autorisation de l'API MS Graph « Lire toutes les données du journal d'audit »

Pour vérifier que votre application est exécutée avec le bon ensemble d’autorisations, suivez les étapes décrites dans les [Prérequis pour accéder à l’API de création de rapports Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md). 

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir des données à l’aide de l’API de création de rapports Azure Active Directory avec des certificats](tutorial-access-api-with-certificates.md)
* [Informations de référence sur l’API d’audit](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Informations de référence sur l’API de création de rapports relatifs à l’activité de connexion](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
