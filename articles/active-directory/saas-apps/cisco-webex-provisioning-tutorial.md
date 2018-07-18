---
title: 'Tutoriel : configurer Cisco pour le provisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour provisionner et déprovisionner automatiquement des comptes utilisateur dans Cisco Webex.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: v-wingf
ms.openlocfilehash: fdaf77e3d8a1858372298fb0d67ca05c2717adf6
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2018
ms.locfileid: "36324164"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Tutoriel : configurer Cisco Webex pour le provisionnement automatique d’utilisateurs


L’objectif de ce tutoriel est de présenter les étapes à effectuer dans Cisco Webex et Azure Active Directory (Azure AD) en vue de configurer Azure AD pour le provisionnement et le déprovisionnement automatiques d’utilisateurs dans Cisco Webex.


> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

*   un locataire Azure AD ;
*   Un locataire Cisco Webex
*   Un compte d’utilisateur dans Cisco Webex avec des autorisations d’administrateur


> [!NOTE]
> L’intégration du provisionnement Azure AD repose sur le [service web Cisco Webex](https://developer.webex.com/getting-started.html), qui est disponible pour les équipes Cisco Webex.

## <a name="adding-cisco-webex-from-the-gallery"></a>Ajout de Cisco Webex à partir de la galerie
Avant de configurer Cisco Webex pour l’attribution automatique d’utilisateurs avec Azure AD, vous devez ajouter Cisco Webex à votre liste d’applications SaaS managées, à partir de la galerie d’applications Azure AD.

**Pour ajouter Cisco Webex à partir de la galerie d’applications Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise** > **Toutes les applications**.

    ![Section Applications d’entreprise][2]

3. Pour ajouter Cisco Webex, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Cisco Webex**.

    ![Provisionnement de Cisco Webex](./media/cisco-webex-provisioning-tutorial/AppSearch.png)

5. Dans le volet de résultats, sélectionnez **Cisco Webex**, puis cliquez sur le bouton **Ajouter** pour ajouter Cisco Webex à votre liste d’applications SaaS.

    ![Provisionnement de Cisco Webex](./media/cisco-webex-provisioning-tutorial/AppSearchResults.png)

    ![Provisionnement de Cisco Webex](./media/cisco-webex-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cisco-webex"></a>Attribution d’utilisateurs à Cisco Webex

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique d’utilisateurs, seuls les utilisateurs et/ou les groupes qui ont été « assignés » à une application dans Azure AD sont synchronisés.

Avant de configurer et d’activer le provisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs Azure AD ont besoin d’accéder à Cisco Webex. Une fois que vous avez choisi, vous pouvez attribuer ces utilisateurs à Cisco Webex en suivant les instructions fournies ici :

*   [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Conseils importants pour l’attribution d’utilisateurs à Cisco Webex

*   Il est recommandé de n’attribuer qu’un seul utilisateur Azure AD à Cisco Webex afin de tester la configuration du provisionnement automatique d’utilisateurs. Les autres utilisateurs peuvent être affectés ultérieurement.

*   Quand vous attribuez un utilisateur à Cisco Webex, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’attribution. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Configuration du provisionnement automatique d’utilisateurs à Cisco Webex

Cette section vous guide tout au long des étapes de configuration du service de provisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs dans Cisco Webex en fonction des attributions d’utilisateurs dans Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Pour configurer le provisionnement automatique d’utilisateurs Cisco Webex dans Azure AD :


1. Connectez-vous au [portail Azure](https://portal.azure.com), puis accédez à la section **Azure Active Directory > Applications d’entreprise > Toutes les applications**.

2. Sélectionnez Cisco Webex dans votre liste d’applications SaaS.

    ![Provisionnement de Cisco Webex](./media/cisco-webex-provisioning-tutorial/Successcenter2.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Provisionnement de Cisco Webex](./media/cisco-webex-provisioning-tutorial/ProvisioningTab.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Provisionnement de Cisco Webex](./media/cisco-webex-provisioning-tutorial/ProvisioningCredentials.png)

5. Sous la section **Informations d’identification de l’administrateur**, entrez **l’URL du locataire** et le **jeton secret** de votre compte Cisco Webex.

    *   Dans le champ **URL du locataire**, entrez l’URL de l’API SCIM Cisco Webex de votre locataire, qui est au format `https://api.webex.com/v1/scim/[Tenant ID]/`, où `[Tenant ID]` est une chaîne alphanumérique, comme décrit à l’étape 6.

    *   Dans le champ **Jeton secret**, spécifiez le jeton secret comme décrit à l’étape 6.

1. Pour obtenir **l’ID de locataire** et le **jeton secret** de votre compte Cisco Webex, connectez-vous au [site des développeurs Cisco Webex](https://developer.webex.com/) avec votre compte d’administrateur. Une fois connecté :
    * Accédez à la page [Getting Started](https://developer.webex.com/getting-started.html) (Prise en main).
    * Faites défiler jusqu'à la [section Authentication (Authentification)](https://developer.webex.com/getting-started.html#authentication)
    ![Cisco Webex Authentication Token](./media/cisco-webex-provisioning-tutorial/SecretToken.png) (Jeton d’authentification Cisco Webex).
    * La chaîne alphanumérique dans la zone de texte est votre **jeton secret**. Copiez ce jeton dans le Presse-papiers.
    * Accédez à la page [Get My Own Details](https://developer.webex.com/endpoint-people-me-get.html) (Obtenir mes informations).
        * Vérifiez que le mode Test est activé.
        * Tapez le mot « Bearer » suivi d’un espace, puis collez le jeton secret dans le champ d’autorisation ![Cisco Webex Authentication Token](./media/cisco-webex-provisioning-tutorial/GetMyDetails.png) (Jeton d’authentification Cisco Webex).
        * Cliquez sur Exécuter.
    * Dans le texte de réponse sur la droite, **l’ID de locataire** correspond à « orgId » :

    ```json
    {
        "id": "(...)",
        "emails": [
            "admin.user@contoso.com"
        ],
        "displayName": "John Smith",
        "nickName": "John",
        "orgId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
        (...)
    }
    ```

1. Après avoir renseigné les champs indiqués à l’étape 5, cliquez sur **Tester la connexion** pour vous vérifier qu’Azure AD peut se connecter à Cisco Webex. Si la connexion échoue, vérifiez que votre compte Cisco Webex dispose des autorisations d’administrateur, puis réessayez.

    ![Provisionnement de Cisco Webex](./media/cisco-webex-provisioning-tutorial/TestConnection.png)

8. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![Provisionnement de Cisco Webex](./media/cisco-webex-provisioning-tutorial/EmailNotification.png)

9. Cliquez sur **Enregistrer**.

10. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Cisco Webex**.

    ![Provisionnement de Cisco Webex](./media/cisco-webex-provisioning-tutorial/UserMapping.png)

11. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Cisco Webex. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés dans le but de faire correspondre les comptes d’utilisateur dans Cisco Webex pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Provisionnement de Cisco Webex](./media/cisco-webex-provisioning-tutorial/UserMappingAttributes.png)

12. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../active-directory-saas-scoping-filters.md).

13. Pour activer le service de provisionnement Azure AD pour Cisco Webex, affectez au paramètre **État du provisionnement** la valeur **Activé** dans la section **Paramètres**.

    ![Provisionnement de Cisco Webex](./media/cisco-webex-provisioning-tutorial/ProvisioningStatus.png)

14. Définissez les utilisateurs et/ou groupes que vous souhaitez provisionner dans Cisco Webex en choisissant les valeurs souhaitées dans **Étendue**, dans la section **Paramètres**.

    ![Provisionnement de Cisco Webex](./media/cisco-webex-provisioning-tutorial/SyncScope.png)

15. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Provisionnement de Cisco Webex](./media/cisco-webex-provisioning-tutorial/Save.png)


Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour surveiller la progression et les liens vers les rapports d’activité du provisionnement, qui décrivent toutes les actions effectuées par le service de provisionnement Azure AD sur Cisco Webex.

Pour plus d’informations sur la lecture des journaux d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux et obtenir des rapports sur l’activité d’approvisionnement](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_03.png