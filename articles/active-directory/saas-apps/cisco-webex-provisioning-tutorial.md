---
title: 'Tutoriel : Configurer Cisco Webex pour l’attribution automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour provisionner et déprovisionner automatiquement des comptes utilisateur dans Cisco Webex.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 0075783c049e7f48645f768026dd9d5ec0ead821
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77058494"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Tutoriel : Configurer Cisco Webex pour le l’approvisionnement automatique d’utilisateurs

L’objectif de ce tutoriel est de présenter les étapes à effectuer dans Cisco Webex et Azure Active Directory (Azure AD) afin de configurer Azure AD pour l’approvisionnement et le désapprovisionnement automatiques d’utilisateurs dans Cisco Webex.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ce connecteur est en préversion. Pour plus d’informations sur les conditions d’utilisation Microsoft Azure générales relatives aux fonctionnalités d’évaluation, consultez [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Conditions préalables requises

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* Un locataire Azure AD.
* [Un locataire Cisco Webex](https://www.webex.com/pricing/index.html).
* Un compte d’utilisateur dans Cisco Webex avec des autorisations d’administrateur.

## <a name="adding-cisco-webex-from-the-gallery"></a>Ajout de Cisco Webex à partir de la galerie

Avant de configurer Cisco Webex pour l’attribution automatique d’utilisateurs avec Azure AD, vous devez ajouter Cisco Webex à votre liste d’applications SaaS managées, à partir de la galerie d’applications Azure AD.

**Pour ajouter Cisco Webex à partir de la galerie d’applications Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Cisco Webex**, sélectionnez **Cisco Webex** dans le panneau des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Cisco Webex dans la liste des résultats](common/search-new-app.png)

## <a name="assigning-users-to-cisco-webex"></a>Attribution d’utilisateurs à Cisco Webex

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique d’utilisateurs, seuls les utilisateurs et/ou les groupes qui ont été « assignés » à une application dans Azure AD sont synchronisés.

Avant de configurer et d’activer le provisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs Azure AD ont besoin d’accéder à Cisco Webex. Une fois que vous avez choisi, vous pouvez attribuer ces utilisateurs à Cisco Webex en suivant les instructions fournies ici :

* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Conseils importants pour l’attribution d’utilisateurs à Cisco Webex

* Il est recommandé de n’attribuer qu’un seul utilisateur Azure AD à Cisco Webex afin de tester la configuration du provisionnement automatique d’utilisateurs. Les autres utilisateurs peuvent être affectés ultérieurement.

* Quand vous attribuez un utilisateur à Cisco Webex, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’attribution. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Configuration du provisionnement automatique d’utilisateurs à Cisco Webex

Cette section vous guide tout au long des étapes de configuration du service de provisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs dans Cisco Webex en fonction des attributions d’utilisateurs dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Pour configurer le provisionnement automatique d’utilisateurs Cisco Webex dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com) et sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Cisco Webex**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Cisco Webex**.

    ![Lien Cisco Webex dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Provisionnement de Cisco Webex](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Provisionnement de Cisco Webex](common/provisioning-automatic.png)

5. Sous la section **Informations d’identification de l’administrateur**, entrez l’**URL du locataire** et le **jeton secret** de votre compte Cisco Webex.

    ![Provisionnement de Cisco Webex](./media/cisco-webex-provisioning-tutorial/secrettoken1.png)

6.  Dans le champ **URL du locataire** entrez une valeur sous la forme `https://api.ciscoweb.com/v1/scim/[OrgId]`. Pour obtenir `[OrgId]`, connectez-vous à votre [Hub de contrôle Cisco Webex](https://admin.webex.com/login). Cliquez sur le nom de votre organisation dans la partie inférieure gauche, puis copiez la valeur d’**ID d’organisation**. 

    * Pour obtenir la valeur de **Jeton secret**, accédez à cette [URL](https://idbroker.webex.com/idb/saml2/jsp/doSSO.jsp?type=login&goto=https%3A%2F%2Fidbroker.webex.com%2Fidb%2Foauth2%2Fv1%2Fauthorize%3Fresponse_type%3Dtoken%26client_id%3DC4ca14fe00b0e51efb414ebd45aa88c1858c3bfb949b2405dba10b0ca4bc37402%26redirect_uri%3Dhttp%253A%252F%252Flocalhost%253A3000%252Fauth%252Fcode%26scope%3Dspark%253Apeople_read%2520spark%253Apeople_write%2520Identity%253ASCIM%26state%3Dthis-should-be-a-random-string-for-security-purpose). Dans la page de connexion Webex qui s’affiche, connectez-vous avec le compte d’administrateur Cisco Webex complet de votre organisation. Une page d’erreur s’affiche, indiquant que le site ne peut pas être atteint, mais cela est normal.

        ![Provisionnement de Cisco Webex](./media/cisco-webex-provisioning-tutorial/test.png)
 
    * Copiez la valeur du jeton du porteur généré à partir de l’URL, comme indiqué ci-dessous. Ce jeton est valide pendant 365 jours.
        
        ![Provisionnement de Cisco Webex](./media/cisco-webex-provisioning-tutorial/test1.png)

7. Après avoir renseigné les champs indiqués à l’étape 5, cliquez sur **Tester la connexion** pour vous vérifier qu’Azure AD peut se connecter à Cisco Webex. Si la connexion échoue, vérifiez que votre compte Cisco Webex dispose des autorisations d’administrateur, puis réessayez.

    ![URL de locataire + Jeton](common/provisioning-testconnection-tenanturltoken.png)
   
8. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

9. Cliquez sur **Enregistrer**.

10. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Cisco Webex**.

    ![Provisionnement de Cisco Webex](./media/cisco-webex-provisioning-tutorial/usermapping.png)

11. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Cisco Webex. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés dans le but de faire correspondre les comptes d’utilisateur dans Cisco Webex pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Provisionnement de Cisco Webex](./media/cisco-webex-provisioning-tutorial/usermappingattributes.png)

12. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pour activer le service de provisionnement Azure AD pour Cisco Webex, affectez au paramètre **État du provisionnement** la valeur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

14. Définissez les utilisateurs et/ou groupes que vous souhaitez provisionner dans Cisco Webex en choisissant les valeurs souhaitées dans **Étendue**, dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

15. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour surveiller la progression et les liens vers les rapports d’activité du provisionnement, qui décrivent toutes les actions effectuées par le service de provisionnement Azure AD sur Cisco Webex.

Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitations du connecteur

* Cisco Webex se trouve actuellement dans la phase des premiers essais sur le terrain (EFT) de Cisco. Pour plus d’informations, veuillez contacter [l’équipe de support technique de Cisco](https://www.webex.co.in/support/support-overview.html). 
* Pour plus d’informations sur la configuration de Cisco Webex, reportez-vous à la documentation Cisco [ici](https://help.webex.com/en-us/aumpbz/Synchronize-Azure-Active-Directory-Users-into-cisco-webex-Control-Hub).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)