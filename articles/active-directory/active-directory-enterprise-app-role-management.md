---
title: Configuration des revendications de rôle émises dans le jeton SAML pour les applications d’entreprise dans Azure Active Directory | Microsoft Docs
description: Découvrez comment configurer les revendications de rôle émises dans le jeton SAML pour les applications d’entreprise dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: jeevansd
manager: mtillman
editor: ''
ms.assetid: eb2b3741-3cde-45c8-b639-a636f3df3b74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 43a4db9114cd47da5bef98ed634847b547589b47
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2018
---
# <a name="configuring-role-claim-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>Configuration des revendications de rôle émises dans le jeton SAML pour les applications d’entreprise dans Azure Active Directory

Cette fonctionnalité permet aux utilisateurs de personnaliser le type de revendication pour la revendication « rôles » dans le jeton de réponse reçu lors de l’autorisation d’une application à l’aide d’Azure AD.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure AD avec l’installation des répertoires
- Un abonnement pour lequel l’authentification unique est activée
- Vous devez configurer l’authentification unique avec votre application

## <a name="when-to-use-this-feature"></a>Quand utiliser cette fonctionnalité

Si votre application s’attend à voir passer dans la réponse SAML des rôles personnalisés, vous devez utiliser cette fonctionnalité. Cette fonctionnalité vous permet de créer autant de rôles que nécessaire à passer à nouveau d’Azure AD vers votre application.

## <a name="steps-to-use-this-feature"></a>Étapes d’utilisation de cette fonctionnalité

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, saisissez le nom de votre application, sélectionnez votre application dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Application dans la liste des résultats](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Une fois l’application ajoutée, allez à la page **Propriétés** et copiez **l’ID de l’objet**.

<!-- ![Properties Page](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png) Note: Image is missing. -->

6. Ouvrez [l’Explorateur graphique Azure AD](https://developer.microsoft.com/graph/graph-explorer) dans une autre fenêtre.

    a. Connectez-vous au site de l’Explorateur graphique en utilisant les informations d’identification d’administrateur/Co-admin générales de votre abonné.

    b. Vous devez disposer des autorisations suffisantes pour créer les rôles. Cliquez sur **modifier les autorisations** pour obtenir les autorisations requises. 

    ![Boîte de dialogue de l’Explorateur graphique](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Sélectionnez les autorisations suivantes dans la liste (si vous ne l’avez pas déjà fait) et cliquez sur « Modifier les autorisations » 

    ![Boîte de dialogue de l’Explorateur graphique](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    d. Vous serez alors invité à vous connecter à nouveau et à accepter le consentement. Après avoir accepté le consentement, vous êtes connecté au système à nouveau.

    e. Modifiez la version sur **bêta** et extrayez la liste des principaux du service à partir de votre abonné à l’aide de la requête suivante :
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    Si vous utilisez plusieurs répertoires, suivez alors le modèle suivant `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Boîte de dialogue de l’Explorateur graphique](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
    f. Dans la liste des principaux du service extraits, obtenez celui que vous souhaitez modifier. Vous pouvez également utiliser les touches Ctrl + F pour rechercher l’application dans la liste les principaux du service. Recherchez **l’ID de l’objet** que vous avez copié à partir de la page Propriétés et utilisez la requête suivante pour accéder au principal du service respectif.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Boîte de dialogue de l’Explorateur graphique](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Extrayez la propriété appRoles à partir de l’objet du principal du service. 

    ![Boîte de dialogue de l’Explorateur graphique](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

    > [!Note]
    > Si vous utilisez l’application (non galerie) personnalisée, les deux rôles par défaut s’affichent : msiam_access et utilisateur. En cas d’application de la galerie, msiam_access est le seul rôle par défaut. Vous n’avez pas besoin d’apporter des modifications aux rôles par défaut.

    h. À présent, vous devez générer de nouveaux rôles pour votre application. 

    i. Vous trouverez en-dessous de JSON un exemple d’objet appRoles. Créez un objet similaire pour ajouter les rôles que vous voulez pour votre application. 

    ```
    {
       "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "b9632174-c057-4f7e-951b-be3adc52bfe6",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Administrators Only",
            "displayName": "Admin",
            "id": "4f8f8640-f081-492d-97a0-caf24e9bc134",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "Administrator"
        }
    ]
    }
    ```
    > [!Note]
    > Vous pouvez uniquement ajouter de nouveaux rôles après le **msiam_access** de l’opération de correction. En outre, vous pouvez ajouter autant de rôles que vous le souhaitez selon les besoins de votre organisation. Azure AD envoie la **valeur** de ces rôles conformément à la valeur de revendication dans la réponse SAML.
    
    j. Revenez à Graph Explorer et modifiez la méthode de **GET** à **PATCH**. Corrigez l’objet du principal du service pour obtenir les rôles souhaités en mettant à jour la propriété appRoles similaire à celle affichée dans l’exemple ci-dessus. Cliquez sur **Exécuter la requête** pour exécuter l’opération de correction. Un message de réussite confirme la création du rôle.

    ![Boîte de dialogue de l’Explorateur graphique](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Une fois le principal du service corrigé avec d’autres rôles, vous pouvez assigner des utilisateurs aux rôles respectifs. Pour ce faire, accédez au portail et à l’application respective. Cliquez sur l’onglet **Utilisateurs et groupes** dans la partie supérieure. Celui-ci répertorie tous les utilisateurs et groupes déjà assignés à l’application. Vous pouvez ajouter de nouveaux utilisateurs sur le nouveau rôle et sélectionner l’utilisateur existant, puis cliquez sur **Modifier** pour modifier le rôle.

    ![Configurer l’authentification unique Add](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

     Pour assigner le rôle à n’importe quel utilisateur, sélectionnez le nouveau rôle, puis cliquez sur le bouton **Assigner** dans la partie inférieure de la page.

    ![Configurer l’authentification unique Add](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Notez que vous devez actualiser votre session dans le portail Azure pour afficher les nouveaux rôles.

8. Après l’assignation des rôles aux utilisateurs, nous devons mettre à jour la table **Attributs** pour définir un mappage personnalisé de revendication de **rôle**.

9. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez l’attribut de jeton SAML comme sur l’image et procédez comme suit :
    
    | Nom de l'attribut | Valeur de l’attribut |
    | -------------- | ----------------|    
    | Nom du rôle      | user.assignedrole |

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configurer l’authentification unique Add](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

    ![Configuration d’attribut de l’authentification unique](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. Dans la zone de texte **Nom**, saisissez le nom de l’attribut, si nécessaire. Dans cet exemple, nous avons utilisé **Nom de rôle** comme nom de la revendication.

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.

    d. Laissez le champ **Espace de noms** vide.
    
    e. Cliquez sur **OK**.

10. Pour tester votre application dans l’authentification unique initiée par IDP, connectez-vous au panneau d’accès (https://myapps.microsoft.com), puis cliquez sur la vignette de votre application. Dans le jeton SAML, vous devez voir tous les rôles assignés à l’utilisateur avec le nom de la revendication que vous avez attribué.

## <a name="update-existing-role"></a>Mettre à jour un rôle existant

Pour mettre à jour un rôle existant, procédez comme suit :

1. Ouvrez [Afficheur de Graph Azure AD](https://developer.microsoft.com/graph/graph-explorer).

2. Connectez-vous au site de l’Explorateur graphique en utilisant les informations d’identification d’administrateur/Co-admin générales de votre abonné.
    
3. Modifiez la version sur **bêta** et extrayez la liste des principaux du service à partir de votre abonné à l’aide de la requête suivante :
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Si vous utilisez plusieurs répertoires, suivez alors le modèle suivant `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Boîte de dialogue de l’Explorateur graphique](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. Dans la liste des principaux du service extraits, obtenez celui que vous souhaitez modifier. Vous pouvez également utiliser les touches Ctrl + F pour rechercher l’application dans la liste les principaux du service. Recherchez **l’ID de l’objet** que vous avez copié à partir de la page Propriétés et utilisez la requête suivante pour accéder au principal du service respectif.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Boîte de dialogue de l’Explorateur graphique](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Extrayez la propriété appRoles à partir de l’objet du principal du service.
    
    ![Boîte de dialogue de l’Explorateur graphique](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)
    
6. Pour mettre à jour le rôle existant, procédez comme suit :

    ![Boîte de dialogue de l’Explorateur graphique](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    * Modifiez la méthode de **GET** à **PATCH**.

    * Copiez les rôles existants et collez-les dans le **Corps de la demande**.

    * Mettez à jour la valeur de rôle en mettant à jour **Description du rôle**, **Valeur du rôle** ou **Nom complet du rôle** en fonction des besoins.

    * Après avoir mis à jour tous les rôles requis, cliquez sur **Exécuter la requête**.
        
## <a name="delete-existing-role"></a>Supprimer un rôle existant

Pour supprimer un rôle existant, procédez comme suit :

1. Ouvrez [l’Explorateur graphique Azure AD](https://developer.microsoft.com/graph/graph-explorer) dans une autre fenêtre.

2. Connectez-vous au site de l’Explorateur graphique en utilisant les informations d’identification d’administrateur/Co-admin générales de votre abonné.

3. Modifiez la version sur **bêta** et extrayez la liste des principaux du service à partir de votre abonné à l’aide de la requête suivante :
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Si vous utilisez plusieurs répertoires, suivez alors le modèle suivant `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Boîte de dialogue de l’Explorateur graphique](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. Dans la liste des principaux du service extraits, obtenez celui que vous souhaitez modifier. Vous pouvez également utiliser les touches Ctrl + F pour rechercher l’application dans la liste les principaux du service. Recherchez **l’ID de l’objet** que vous avez copié à partir de la page Propriétés et utilisez la requête suivante pour accéder au principal du service respectif.
     
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Boîte de dialogue de l’Explorateur graphique](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Extrayez la propriété appRoles à partir de l’objet du principal du service.
    
    ![Boîte de dialogue de l’Explorateur graphique](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Pour supprimer le rôle existant, procédez comme suit :

    ![Boîte de dialogue de l’Explorateur graphique](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    * Modifiez la méthode de **GET** à **PATCH**.

    * Copiez les rôles existants à partir de l’application et collez-les dans le **Corps de la demande**.
        
    * Définissez la valeur **IsEnabled** sur **false** pour le rôle que vous souhaitez supprimer.

    * Cliquez sur **Exécuter la requête**.
    
    > [!NOTE] 
    > Assurez-vous que vous avez le rôle d’utilisateur **msiam_access** et que l’ID correspond au rôle généré.
    
7. Une fois le rôle désactivé, supprimez ce bloc de rôles à partir de la section appRoles, conservez la méthode en tant que **PATCH** et cliquez sur **Exécuter la requête**.
    
8. Le rôle sera supprimé après l’exécution de la requête.
    
    > [!NOTE]
    > Le rôle doit d’abord être désactivé avant de pouvoir être supprimé. 

## <a name="next-steps"></a>Étapes suivantes

Consultez [Documentation de l’application ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) pour connaître les étapes supplémentaires.

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
