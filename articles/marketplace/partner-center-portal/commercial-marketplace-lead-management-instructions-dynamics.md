---
title: Gestion des prospects pour Dynamics 365 for Customer Engagement | Place de marché Azure
description: Configurez la gestion des prospects pour Dynamics 365 for Customer Engagement.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 5b3e35b6d19905e3c5262dfea3e52511510c9ffe
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81252780"
---
# <a name="configure-lead-management-for-dynamics-365-for-customer-engagement"></a>Configurer la gestion des prospects pour Dynamics 365 for Customer Engagement

Cet article explique comment configurer Dynamics 365 for Customer Engagement (précédemment Dynamics CRM Online ; plus d’informations sur le changement [ici](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online)) pour traiter des prospects à partir de votre offre de la Place de marché. 

>[!Note]
>Ces instructions sont spécifiques de l’environnement Dynamics 365 for Customer Engagement dans le cloud hébergé par Microsoft. La connexion directe à un environnement Dynamics local n’est pas prise en charge actuellement. D’autres options permettent de recevoir des prospects, telles que la configuration d’un [point de terminaison HTTPS](./commercial-marketplace-lead-management-instructions-https.md) ou d’une [table Azure](./commercial-marketplace-lead-management-instructions-azure-table.md).

## <a name="prerequisites"></a>Prérequis

Pour suivre les étapes décrites dans cet article, vous devez disposer des autorisations utilisateur suivantes :

* Pour pouvoir installer une solution et suivre ces instructions, vous devez être administrateur de votre instance Dynamics 365 for Customer Engagement.
* Pour créer un compte pour le service utilisé pour envoyer des prospects à partir des offres de la Place de marché, vous devez être administrateur de locataire.
* Vous devez avoir accès au portail d’administration Office 365.
* Vous devez avoir accès au Portail Azure.

## <a name="install-the-solution"></a>Installer la solution

1.  Téléchargez la [solution Microsoft Marketplace Lead Writer](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) et enregistrez-la sur votre ordinateur.

2.  Ouvrez Dynamics 365 for Customer Engagement en accédant à l’URL de votre instance Dynamics (par exemple, `https://tenant.crm.dynamics.com`).

3.  Accédez aux paramètres en sélectionnant l’icône d’engrenage, puis **Paramètres avancés** dans la barre de navigation supérieure.
 
    ![Dynamics – Paramètres avancés](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

4.  Dans la page Paramètres, accédez au menu Paramètres dans la barre de navigation supérieure, puis sélectionnez **Solutions**.

    >[!Note]
    >Si vous ne voyez pas les options présentées dans la capture d’écran suivante, cela signifie que vous ne disposez pas des autorisations requises. Dans ce cas, adressez-vous à un administrateur sur votre instance Dynamics 365 for Customer Engagement.

    ![Dynamics 365 – Solutions](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

5. Dans la page Solutions, sélectionnez **Importer**, puis accédez à l’emplacement où vous avez enregistré la solution *Microsoft Marketplace Lead Writer* que vous avez téléchargée à l’étape 1.

    ![Dynamics 365 for Customer Engagement – Importer](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

6. Terminez l’importation de la solution en suivant les instructions de l’Assistant Importation de solution.

## <a name="configure-user-permissions"></a>Configurer les autorisations utilisateur

Pour écrire des prospects dans votre instance Dynamics 365 for Customer Engagement, vous devez partager un compte de service avec nous et configurer des autorisations pour celui-ci.

Pour créer le compte de service et attribuer les autorisations, procédez comme suit. Vous pouvez utiliser **Azure Active Directory** ou **Office 365**.

>[!Note]
>Selon l’option d’authentification sélectionnée, vous pouvez passer aux instructions correspondantes. Voir [Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory) ou [Office 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

Nous recommandons cette option car vous bénéficiez de l’avantage supplémentaire de ne jamais avoir besoin de mettre à jour votre nom d’utilisateur/mot de passe afin de continuer à obtenir des prospects. Pour utiliser l’option Azure Active Directory, vous devez fournir l’ID de l’application, la Clé de l’application et l’ID de répertoire de votre application Active Directory.

Pour configurer Azure Active Directory pour Dynamics 365 for Customer Engagement, procédez comme suit.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) et sélectionnez le service Azure Active Directory dans le volet de navigation gauche.

2. Sélectionnez **Propriétés** dans le volet de navigation gauche d’Azure Active Directory, puis copiez la valeur **ID de répertoire** affichée sur cette page. Enregistrez cette valeur, car il s’agit de la valeur d’*ID de répertoire* que vous devez fournir dans le portail de publication afin de recevoir des prospects pour votre offre de la Place de marché.

    ![Azure Active Directory – Propriétés](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

3. Dans le volet de navigation gauche d’Azure Active Directory, sélectionnez **Inscriptions d’applications**, puis **Nouvelle inscription** dans la page qui s’affiche.
4. Entrez un nom pour l’application. Choisissez un nom d’application explicite.
5. Sous Types de comptes pris en charge, sélectionnez **Comptes dans un annuaire organisationnel**.
6. Sous URI de redirection, sélectionnez **Web**, puis fournissez un URI (tel que `https://contosoapp1/auth`). 
7. Sélectionnez **Inscription**.

    ![Inscrire une application](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

8. Maintenant que votre application est inscrite, accédez à la page de vue d’ensemble de l’application, puis copiez la valeur **ID d’application (client)** sur cette page. Enregistrez cette valeur, car il s’agit de la valeur d’*ID d’application (client)* que vous devez fournir dans le portail de publication et dans Dynamics afin de recevoir des prospects pour votre offre de la Place de marché.

    ![ID d’application (client)](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

9. Dans le volet de navigation gauche de l’application, sélectionnez **Certificats et secrets**, puis **Nouveau secret client** dans la page qui s’affiche. Entrez une description explicite de la clé secrète client, puis, sous Expire, sélectionnez l’option **Jamais**. Sélectionnez **Ajouter** pour créer la clé secrète client.

    ![Application – Certification et secrets](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

10. Dès que la clé secrète client est correctement créée, **copiez sa valeur**. Vous ne pourrez pas récupérer la valeur une fois que vous aurez quitté la page. Enregistrez cette valeur, car il s’agit de la valeur *Clé secrète client* que vous devez fournir dans le portail de publication afin recevoir des prospects pour votre offre de la Place de marché. 
11. Dans le volet de navigation gauche de l’application, sélectionnez **Autorisations de l’API**, puis sélectionnez **Ajouter une autorisation**.
12. Sélectionnez API Microsoft, puis l’API **Dynamics CRM**.
13. Sous *Quel type d’autorisation votre application nécessite-t-elle ?* , assurez-vous que l’option **Autorisations déléguées** est sélectionnée. Pour **user_impersonation**, activez l'autorisation *Accéder à Common Data Service en tant qu'utilisateurs de l'organisation*. Sélectionnez **Ajouter des autorisations**.

    ![Ajout d’autorisations](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

14. Après avoir suivi les étapes 1 à 13 sur le portail Azure, accédez à votre instance de Dynamics 365 for Customer Engagement via son URL (par exemple, `https://tenant.crm.dynamics.com`).
15. Accédez à Paramètres en sélectionnant l’icône d’engrenage, puis à **Paramètres avancés** dans la barre de navigation supérieure.
16. Dans la page Paramètres, accédez au menu Paramètres dans la barre de navigation supérieure, puis sélectionnez **Sécurité**.
17. Dans la page Sécurité, sélectionnez **Utilisateurs**.  Dans la page Utilisateurs, sélectionnez la liste déroulante « Utilisateurs activés » pour basculer vers **Utilisateurs de l’application**.
18. Sélectionnez **Nouveau** pour créer un utilisateur. 

    ![Créer un utilisateur](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

19. Dans **Nouvel utilisateur**, assurez-vous que UTILISATEUR :  UTILISATEUR DE L'APPLICATION est sélectionné. Spécifiez un nom d’utilisateur, un nom complet et une adresse e-mail pour l’utilisateur que vous souhaitez utiliser avec cette connexion. Par ailleurs, collez **l’ID d’application** de l’application que vous avez créée dans le portail Azure à l’étape 8. Sélectionnez **Enregistrer et fermer** pour terminer l’ajout de l’utilisateur.

    ![Nouvel utilisateur](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

20. Accédez à « Paramètres de sécurité » dans cet article pour terminer la configuration de la connexion pour cet utilisateur.

### <a name="office-365"></a>Office 365

Si vous ne souhaitez pas utiliser Azure Active Directory, vous pouvez inscrire un nouvel utilisateur dans le *centre d’administration Microsoft 365*. Vous devrez mettre à jour votre nom d’utilisateur/mot de passe tous les 90 jours pour continuer à obtenir des prospects.

Pour configurer Office 365 pour Dynamics 365 for Customer Engagement, procédez comme suit.

1. Connectez-vous au [centre d’administration Microsoft 365](https://admin.microsoft.com).

2. Sélectionnez **Ajouter un utilisateur**.

    ![Centre d’administration Microsoft 365 – Ajouter un utilisateur](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

4. Créez un utilisateur pour le service d’écriture de prospect. Configurez les paramètres suivants :

    * Fournir un nom d’utilisateur
    * Fournissez un mot de passe et désactivez la case à cocher « Demander à cet utilisateur de modifier son mot de passe lors de sa première connexion ».
    * Sélectionnez « Utilisateur (pas d’accès administrateur) » en tant que rôle de l’utilisateur.
    * Sélectionnez « Offre Dynamics 365 for Customer Engagement » en tant que licence de produit, comme dans la capture d’écran suivante. Vous êtes facturé pour la licence sélectionnée. 

Enregistrez ces valeurs, car il s’ agit des valeurs *Nom d’utilisateur et Mot de passe* que vous devez fournir sur le portail de publication afin de recevoir des prospects pour votre offre de la Place de marché.

![Centre d’administration Microsoft 365 – Nouvel utilisateur](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Paramètres de sécurité

L’étape finale consiste à permettre à l’utilisateur que vous avez créé d’écrire les prospects.

1. Ouvrez Dynamics 365 for Customer Engagement en accédant à l’URL de votre instance Dynamics (par exemple, `https://tenant.crm.dynamics.com`).
2. Accédez aux paramètres en sélectionnant l’icône d’engrenage, puis **Paramètres avancés** dans la barre de navigation supérieure.
3. Dans la page Paramètres, accédez au menu Paramètres dans la barre de navigation supérieure, puis sélectionnez **Sécurité**.
4. Dans la page Sécurité, sélectionnez **Utilisateurs**, choisissez l’utilisateur que vous avez créé dans la section Configurer les autorisations utilisateur de ce document, puis sélectionnez **Gérer les rôles**. 

    ![Gérer les rôles](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

5. Recherchez le nom de rôle « Microsoft Marketplace Lead Writer », puis sélectionnez-le pour attribuer le rôle à l’utilisateur.

    ![Gérer les rôles d’utilisateur](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!Note]
    >Ce rôle est créé par la solution que vous avez importée, et dispose uniquement des autorisations d’écrire les prospects et de suivre la version de la solution pour garantir la compatibilité.

6. Revenez à la page Sécurité, puis sélectionnez **Rôles de sécurité**. Recherchez le rôle « Microsoft Marketplace Lead Writer » et sélectionnez-le.

    ![Rôles de sécurité](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

7. Une fois dans le rôle de sécurité, sélectionnez l’onglet **Enregistrements principaux**. Recherchez l’entité « Paramètres d’interface utilisateur de l’entité utilisateur » et activez les autorisations Créer, Lire et Écrire pour l’utilisateur (1/4 de cercle jaune) pour cette entité en cliquant une fois dans chacun des cercles correspondants.

    ![Microsoft Marketplace Lead Writer – Enregistrements principaux](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

8. À présent, accédez à l’onglet **Personnalisation**. Recherchez l’entité « Tâche système » et activez les autorisations Lire, Écrire et AppendTo pour Organisation (vert en aplat) pour cette entité en cliquant quatre fois sur chacun des cercles correspondants.

    ![Microsoft Marketplace Lead Writer – Personnalisation](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

9. **Enregistrez et fermez**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-for-customer-engagement"></a>Configurer votre offre pour envoyer des prospects à Dynamics 365 for Customer Engagement

Lorsque vous êtes prêt à configurer les informations de gestion des prospects pour votre offre sur le portail de publication, procédez comme suit :

1. Accédez à la page **Configuration de l'offre** de votre offre.
2. Sélectionnez **Connexion** dans la section Gestion des prospects.

    ![Se connecter à Gestion des prospects](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

3. Dans la fenêtre contextuelle Détails de la connexion, pour Destination du prospect, sélectionnez **Dynamics 365 for Customer Engagement**.

    ![Détails de la connexion – Destination du prospect](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

4. Fournissez l’**URL d’instance Dynamics 365**, par exemple `https://contoso.crm4.dynamics.com`.

5. Sélectionnez la méthode d’**Authentification** : Azure Active Directory ou Office 365. 
6. Si vous sélectionnez Azure Active Directory, fournissez l’**ID d’application (client)** (par exemple, `23456052-aaaa-bbbb-8662-1234df56788f`), l’**ID de répertoire** (par exemple, `12345678-8af1-4asf-1234-12234d01db47`) et la **Clé secrète client** (par exemple, `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`).

    ![Détails de la connexion – Active Directory de Azure](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

7. Si vous sélectionnez Office 365, fournissez le **Nom d’utilisateur** (par exemple, `contoso@contoso.onmicrosoft.com`), et le Mot de passe (par exemple, `P@ssw0rd`).

    ![Détails de la connexion – Nom d’utilisateur](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

8. **E-mail du contact** : fournissez les e-mails des personnes de votre société qui doivent recevoir des notifications par e-mail lors de la réception d’un nouveau prospect. Vous pouvez fournir plusieurs e-mails en les séparant par un point-virgule.
9. Sélectionnez **OK**.

Pour vous assurer que vous vous êtes bien connecté à une destination de prospect, cliquez sur le bouton Valider. En cas de réussite, un prospect de test se trouve dans la destination du prospect.

![Gestion des prospects - compte de stockage des détails de connexion](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-connection-details.png)

>[!Note]
>Vous devez terminer la configuration du reste de l'offre et la publier avant de pouvoir recevoir des prospects pour cette offre.
