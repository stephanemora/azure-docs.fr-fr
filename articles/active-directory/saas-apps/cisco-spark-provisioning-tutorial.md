---
title: 'Tutoriel : Configurer Cisco Spark pour l’attribution automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour provisionner et déprovisionner automatiquement des comptes utilisateur dans Cisco Spark.
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
ms.openlocfilehash: bb3b3061c15a661caff778ca5c0ec48b0434c718
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2018
ms.locfileid: "42144217"
---
# <a name="tutorial-configure-cisco-spark-for-automatic-user-provisioning"></a>Tutoriel : Configurer Cisco Spark pour l’attribution automatique d’utilisateurs


L’objectif de ce tutoriel est de présenter les étapes à effectuer dans Cisco Spark et Azure Active Directory (Azure AD) en vue de configurer Azure AD pour le provisionnement et le déprovisionnement automatiques d’utilisateurs dans Cisco Spark.


> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](./../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

*   un locataire Azure AD ;
*   Un locataire Cisco Spark
*   Un compte d’utilisateur dans Cisco Spark avec des autorisations d’administrateur


> [!NOTE]
> L’intégration du provisionnement Azure AD repose sur le [service web Cisco Spark](https://developer.webex.com/getting-started.html), qui est disponible pour les équipes Cisco Spark.

## <a name="adding-cisco-spark-from-the-gallery"></a>Ajout de Cisco Spark à partir de la galerie
Avant de configurer Cisco Spark pour l’attribution automatique d’utilisateurs avec Azure AD, vous devez ajouter Cisco Spark à votre liste d’applications SaaS managées, à partir de la galerie d’applications Azure AD.

**Pour ajouter Cisco Spark à partir de la galerie d’applications Azure AD, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise** > **Toutes les applications**.

    ![Section Applications d’entreprise][2]

3. Pour ajouter Cisco Spark, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Cisco Spark**.

    ![Provisionnement Cisco Spark](./media/cisco-spark-provisioning-tutorial/AppSearch.png)

5. Dans le volet de résultats, sélectionnez **Cisco Spark**, puis cliquez sur le bouton **Ajouter** pour ajouter Cisco Spark à votre liste d’applications SaaS.

    ![Provisionnement Cisco Spark](./media/cisco-spark-provisioning-tutorial/AppSearchResults.png)

    ![Provisionnement Cisco Spark](./media/cisco-spark-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cisco-spark"></a>Attribution d’utilisateurs à Cisco Spark

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique d’utilisateurs, seuls les utilisateurs et/ou les groupes qui ont été « assignés » à une application dans Azure AD sont synchronisés.

Avant de configurer et d’activer l’attribution automatique d’utilisateurs, vous devez décider quels utilisateurs Azure AD ont besoin d’accéder à Cisco Spark. Une fois que vous avez choisi, vous pouvez attribuer ces utilisateurs à Cisco Spark en suivant les instructions fournies ici :

*   [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-spark"></a>Conseils importants pour l’attribution d’utilisateurs à Cisco Spark

*   Il est recommandé de n’attribuer qu’un seul utilisateur Azure AD à Cisco Spark afin de tester la configuration de l’attribution automatique d’utilisateurs. Les autres utilisateurs peuvent être affectés ultérieurement.

*   Quand vous attribuez un utilisateur à Cisco Spark, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’attribution. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configuring-automatic-user-provisioning-to-cisco-spark"></a>Configuration de l’attribution automatique d’utilisateurs à Cisco Spark

Cette section vous guide tout au long des étapes de configuration du service de provisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs dans Cisco Spark en fonction des attributions d’utilisateurs dans Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-cisco-spark-in-azure-ad"></a>Pour configurer l’attribution automatique d’utilisateurs Cisco Spark dans Azure AD :


1. Connectez-vous au [portail Azure](https://portal.azure.com), puis accédez à la section **Azure Active Directory > Applications d’entreprise > Toutes les applications**.

2. Sélectionnez Cisco Spark dans votre liste d’applications SaaS.

    ![Provisionnement Cisco Spark](./media/cisco-spark-provisioning-tutorial/Successcenter2.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Provisionnement Cisco Spark](./media/cisco-spark-provisioning-tutorial/ProvisioningTab.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Provisionnement Cisco Spark](./media/cisco-spark-provisioning-tutorial/ProvisioningCredentials.png)

5. Sous la section **Informations d’identification de l’administrateur**, entrez **l’URL du locataire** et le **jeton secret** de votre compte Cisco Spark.

    *   Dans le champ **URL du locataire**, entrez l’URL de l’API SCIM Cisco Spark de votre locataire, qui est au format `https://api.ciscospark.com/v1/scim/[Tenant ID]/`, où `[Tenant ID]` est une chaîne alphanumérique, comme décrit à l’étape 6.

    *   Dans le champ **Jeton secret**, spécifiez le jeton secret comme décrit à l’étape 6.

1. Pour obtenir **l’ID de locataire** et le **jeton secret** de votre compte Cisco Spark, connectez-vous au [site des développeurs Cisco Spark](https://developer.webex.com/) avec votre compte d’administrateur. Une fois connecté :
    * Accédez à la page [Getting Started](https://developer.webex.com/getting-started.html) (Prise en main).
    * Faites défiler jusqu’à la [section Authentication (Authentification)](https://developer.webex.com/getting-started.html#authentication)
    ![Cisco Spark Authentication Token](./media/cisco-spark-provisioning-tutorial/SecretToken.png) (Jeton d’authentification Cisco Spark).
    * La chaîne alphanumérique dans la zone de texte est votre **jeton secret**. Copiez ce jeton dans le Presse-papiers.
    * Accédez à la page [Get My Own Details](https://developer.webex.com/endpoint-people-me-get.html) (Obtenir mes informations).
        * Vérifiez que le mode Test est activé.
        * Tapez le mot « Bearer » suivi d’un espace, puis collez le jeton secret dans le champ d’autorisation ![Cisco Spark Authentication Token](./media/cisco-spark-provisioning-tutorial/GetMyDetails.png) (Jeton d’authentification Cisco Spark).
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

1. Après avoir renseigné les champs indiqués à l’étape 5, cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Cisco Spark. Si la connexion échoue, vérifiez que votre compte Cisco Spark dispose des autorisations d’administrateur, puis réessayez.

    ![Provisionnement Cisco Spark](./media/cisco-spark-provisioning-tutorial/TestConnection.png)

8. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![Provisionnement Cisco Spark](./media/cisco-spark-provisioning-tutorial/EmailNotification.png)

9. Cliquez sur **Enregistrer**.

10. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Cisco Spark**.

    ![Provisionnement Cisco Spark](./media/cisco-spark-provisioning-tutorial/UserMapping.png)

11. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Cisco Spark. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés dans le but de faire correspondre les comptes d’utilisateur dans Cisco Spark pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Provisionnement Cisco Spark](./media/cisco-spark-provisioning-tutorial/UserMappingAttributes.png)

12. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../active-directory-saas-scoping-filters.md).

13. Pour activer le service de provisionnement Azure AD pour Cisco Spark, affectez au paramètre **État de l’approvisionnement** la valeur **Activé** dans la section **Paramètres**.

    ![Provisionnement Cisco Spark](./media/cisco-spark-provisioning-tutorial/ProvisioningStatus.png)

14. Définissez les utilisateurs et/ou groupes que vous souhaitez provisionner dans Cisco Spark en choisissant les valeurs souhaitées dans **Étendue**, dans la section **Paramètres**.

    ![Provisionnement Cisco Spark](./media/cisco-spark-provisioning-tutorial/SyncScope.png)

15. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Provisionnement Cisco Spark](./media/cisco-spark-provisioning-tutorial/Save.png)


Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour surveiller la progression et les liens vers les rapports d’activité de provisionnement, qui décrivent toutes les actions effectuées par le service de provisionnement Azure AD sur Cisco Spark.

Pour plus d’informations sur la lecture des journaux d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../active-directory-saas-provisioning-reporting.md).

## <a name="connector-limitations"></a>Limitations du connecteur

* Cisco Spark se trouve actuellement dans la phase des premiers essais sur le terrain (EFT) de Cisco. Pour plus d’informations, veuillez contacter le [Support technique Cisco](https://www.webex.co.in/support/support-overview.html). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux et obtenir des rapports sur l’activité d’approvisionnement](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_03.png
