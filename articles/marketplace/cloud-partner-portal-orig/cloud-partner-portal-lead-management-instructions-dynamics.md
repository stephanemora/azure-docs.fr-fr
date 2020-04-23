---
title: Dynamics CRM | Place de marché Azure
description: Configurez la gestion des prospects pour Dynamics CRM.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: d64c8460f5653f28b96396025f29ea13af15c8c3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416318"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>Configurez la gestion des prospects pour Dynamics CRM Online.

Cet article explique comment configurer Dynamics CRM Online pour traiter les prospects commerciaux.

## <a name="prerequisites"></a>Prérequis

Avant de poursuivre cet article, vérifiez que vous disposer des autorisations utilisateur suivantes :
- Vous devez être administrateur sur votre instance Dynamics CRM Online pour installer une solution.
- Vous devez être administrateur client pour créer un compte de service pour le service de prospects.

<a name="install-the-solution"></a>Installer la solution
--------------------

1.  Téléchargez la [solution Microsoft Marketplace Lead Writer](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) et enregistrez-la localement.

2.  Ouvrez Dynamics CRM Online et accédez à Paramètres.
    >[!NOTE]
    >Si vous ne voyez pas les options de la capture d’écran suivante, cela signifie que vous ne disposez pas des autorisations requises.
 
       ![Affichage de configuration de Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  Sélectionnez **Importer**, puis sélectionnez la solution que vous avez téléchargée à l’étape 1.
 
    ![Options d’importation de Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  Achevez l’installation de la solution.

## <a name="configure-user-permissions"></a>Configurer les autorisations utilisateur

Pour écrire des prospects dans votre instance Dynamics CRM, vous devez partager un compte de service avec nous et configurer les autorisations pour le compte.

Pour créer le compte de service et attribuer les autorisations, procédez comme suit. Vous pouvez utiliser **Azure Active Directory** ou **Office 365**.

### <a name="azure-active-directory"></a>Azure Active Directory

Nous recommandons cette option car vous bénéficiez de l’avantage supplémentaire de ne jamais avoir besoin de mettre à jour votre nom d’utilisateur/mot de passe afin de continuer à obtenir des prospects. Pour utiliser l’option Azure Active Directory, vous devez fournir l’identifiant de l’application, la clé d’application et l’identifiant de répertoire de votre application Active Directory.

Pour configurer Azure Active Directory pour Dynamics CRM, procédez comme suit.

1.  Connectez-vous au [portail Azure](https://portal.azure.com/) et sélectionnez le service Azure Active Directory.

2.  Sélectionnez **Propriétés**, puis copiez l’**ID de répertoire**. Il s’agit de l’identification de votre compte client que vous devez utiliser dans le portail Microsoft Cloud Partner.

    ![Obtenir l’ID de répertoire](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  Sélectionnez **Inscriptions d’applications**, puis **Nouvelle inscription d’application**.
4.  Entrez le nom de l’application.
5.  Comme Type, sélectionnez **Application web/API**.
6.  Fournissez une URL. Ce champ n’est pas nécessaire pour les prospects, mais il l’est pour créer une application.
7. Sélectionnez **Create** (Créer).
8.  Maintenant que votre application est enregistrée, sélectionnez **Propriétés**, puis sélectionnez **Copier l’ID d’application**. Vous utiliserez ces informations de connexion dans le Portail Cloud Partner.
9.  Dans Propriétés, réglez l’application sur Mutualisé, puis sélectionnez **Enregistrer**.

10. Sélectionnez **Clés** et créez une clé dont la durée est définie sur *Pas de date d’expiration*. Sélectionnez **Enregistrer** pour créer la clé. 
11. Dans le menu Clé, sélectionnez **Copier la valeur de la clé.** Enregistrez une copie de cette valeur car vous en aurez besoin pour le Portail Cloud Partner.
    
    ![Clé enregistrée récupérée par Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
    
12. Sélectionnez **Autorisations requises**, puis **Ajouter**. 
13. Sélectionnez **Dynamics CRM Online** en tant que nouvelle API et sélectionnez l’autorisation *Accéder à CRM Online en tant qu’utilisateurs de l’organisation*.

14. Dans Dynamics CRM, accédez à Utilisateurs et sélectionnez la liste déroulante « Utilisateurs activés » pour basculer vers **Utilisateurs de l’application**.
    
    ![Utilisateurs de l’application](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. Sélectionnez **Nouveau** pour créer un utilisateur. Sélectionnez la liste déroulante **UTILISATEUR : UTILISATEURS DE L’APPLICATION**.
    
    ![Ajouter un nouvel utilisateur de l’application](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. Dans **Nouvel utilisateur**, indiquez le nom et l’adresse e-mail que vous souhaitez utiliser avec cette connexion. Dans **ID d’application**, collez l’application que vous avez créée dans le portail Azure.

     ![Configurer un nouvel utilisateur](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. Accédez à « Paramètres de sécurité » dans cet article pour terminer la configuration de la connexion pour cet utilisateur.

### <a name="office-365"></a>Office 365

Si vous ne souhaitez pas utiliser Azure Active Directory, vous pouvez inscrire un nouvel utilisateur dans le *centre d’administration Microsoft 365*. Vous devrez mettre à jour votre nom d’utilisateur/mot de passe tous les 90 jours pour continuer à obtenir des prospects.

Pour configurer Azure Active Office 365 pour Dynamics CRM, procédez comme suit.

1. Connectez-vous au [centre d’administration Microsoft 365](https://admin.microsoft.com).

2. Sélectionnez la vignette **Administrateur**.

    ![Administrateur Office Online](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. Sélectionnez **Ajouter un utilisateur**.

    ![Ajouter un utilisateur](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. Créez un utilisateur pour le service d’écriture de prospect. Configurez les paramètres suivants :

    -   Fournissez un mot de passe et désactivez la case à cocher « Amener cet utilisateur à modifier son mot de passe lors de sa première connexion ».
    -   Sélectionnez « Utilisateur (pas d’accès administrateur) » en tant que rôle de l’utilisateur.
    -   Sélectionnez la licence comme indiqué dans la capture d’écran suivante. Vous êtes facturé pour la licence sélectionnée. La solution fonctionnera également avec licence Dynamics CRM Online De base.
    
    ![Configurer les autorisations utilisateur et la licence](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>Paramètres de sécurité

L’étape finale consiste à permettre à l’utilisateur que vous avez créé d’écrire les prospects.

1.  Connectez-vous à Dynamics CRM Online.
2.  Dans **Paramètres**, sélectionnez **Sécurité**.
    
    ![Paramètres de sécurité](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  Sélectionnez l’utilisateur que vous avez créé dans **Autorisations utilisateur**, puis sélectionnez **Gérer les rôles d’utilisateurs**. Sélectionnez **Rédacteur de prospects de la Place de marché Microsoft** pour attribuer le rôle.

    ![Affecter un rôle utilisateur](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >Ce rôle est créé par la solution que vous avez importée, et dispose uniquement des autorisations d’écrire les prospects et de suivre la version de la solution pour garantir la compatibilité.

4.  Dans sécurité, sélectionnez **Rôles de sécurité** et trouvez le rôle Rédacteur de prospects de la Place de marché Microsoft.
    
    ![Configurer le rédacteur de prospects de sécurité](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. Sélectionnez l’onglet **Enregistrements principaux**. Activez les autorisations Créer/Lire/Écrire pour l’interface utilisateur de l’entité utilisateur.

    ![Activer les autorisations Créer/Lire/Écrire pour l’utilisateur](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>Conclusion

Terminez la configuration de Dynamics CRM pour la gestion des prospects en ajoutant les informations de compte générées sur le Portail Microsoft Cloud Partner. Par exemple :

-   **ID d'application** - **Azure Active Directory** (exemple : *23456052-aaaa-bbbb-8662-1234df56788f*), **ID de répertoire** (exemple : *12345678-8af1-4asf-1234-12234d01db47*), et **clé d’application** (exemple : *1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=* ).
-   **Office 365** - **URL** (exemple : **`https://contoso.crm4.dynamics.com`** ), **Nom d’utilisateur** (exemple : **`contoso\@contoso.onmicrosoft.com`** ) et **Mot de passe** (exemple : *P\@ssw0rd*).
