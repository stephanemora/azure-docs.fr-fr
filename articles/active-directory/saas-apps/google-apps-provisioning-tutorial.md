---
title: 'Tutoriel : Configurer G Suite pour l’attribution automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment approvisionner et annuler automatiquement l’approvisionnement des comptes d’utilisateur d’Azure AD vers G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: beaa8561028a9e21d0623c0eb8e19592f3cad055
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74167865"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Tutoriel : Configurer G Suite pour l’attribution automatique d’utilisateurs

L’objectif de ce didacticiel est de présenter les étapes à effectuer dans G Suite et Azure Active Directory (Azure AD) afin de configurer Azure AD pour l’approvisionnement et l’annulation de l’approvisionnement automatiques d’utilisateurs et/ou de groupes sur G Suite.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md).

> [!NOTE]
> Le connecteur G Suite a été récemment mis à jour en octobre 2019. Les modifications apportées au connecteur G Suite sont les suivantes :
- Ajout de la prise en charge d’autres attributs d’utilisateur et de groupe G Suite. 
- Les noms d’attributs cibles G Suite mis à jour correspondent à ce qui est défini [ici](https://developers.google.com/admin-sdk/directory).
- Mappages d’attributs par défaut mis à jour.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à G Suite, vous avez besoin des éléments suivants :

- un locataire Azure AD ;
- [Un abonné G suite](https://gsuite.google.com/pricing.html)
- Un compte d’utilisateur dans G Suite avec des autorisations d’administrateur.

## <a name="assign-users-to-g-suite"></a>Affecter des utilisateurs à G Suite

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre d’une attribution automatique d’utilisateurs, seuls les utilisateurs ou les groupes auxquels une application a été attribuée dans Azure AD sont synchronisés.

Avant de configurer et d’activer l’approvisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à G Suite. Une fois que vous avez choisi, vous pouvez attribuer ces utilisateurs et/ou groupes à G Suite en suivant les instructions fournies ici :

* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-g-suite"></a>Conseils importants pour l’attribution d’utilisateurs à G Suite

* Il est recommandé de n’attribuer qu’un seul utilisateur Azure AD à G Suite afin de tester la configuration de l’approvisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Quand vous attribuez un utilisateur à G Suite, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’attribution. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="setup-g-suite-for-provisioning"></a>Configurer G suite pour l’approvisionnement

Avant de configurer G Suite pour l’approvisionnement automatique d’utilisateurs avec Azure AD, vous devez activer l’approvisionnement SCIM dans G Suite.

1. Connectez-vous à la [Console d’administration de G Suite](https://admin.google.com/) avec votre compte d’administrateur, puis sélectionnez **Sécurité**. Si le lien ne s’affiche pas, il est peut-être masqué par le menu **Autres contrôles** situé en bas de l’écran.

    ![Sélectionnez Sécurité.][10]

2. Dans la page **Sécurité**, sélectionnez **Informations de référence sur l’API**.

    ![Sélectionnez Informations de référence sur l’API.][15]

3. Sélectionnez **Activer l'accès à l'API**.

    ![Sélectionnez Informations de référence sur l’API.][16]

   > [!IMPORTANT]
   > Le nom d’utilisateur dans Azure AD de chaque utilisateur que vous souhaitez approvisionner dans G Suite **doit** être associé à un domaine personnalisé. Par exemple, les noms d’utilisateur qui ressemblent à bob@contoso.onmicrosoft.com ne sont pas acceptés par G Suite. En revanche, bob@contoso.com est accepté. Vous pouvez modifier le domaine d’un utilisateur existant en suivant les instructions [ici](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain).

4.  Une fois que vous avez ajouté et vérifié les domaines personnalisés souhaités avec Azure AD, vous devez les vérifier à nouveau avec G Suite. Pour vérifier les domaines dans G Suite, reportez-vous aux étapes suivantes :

    a. Dans la [Console d’administration de G Suite](https://admin.google.com/), sélectionnez **Domaines**.

    ![Sélectionner les domaines][20]

    b. Sélectionnez **Ajouter un domaine ou un alias de domaine**.

    ![Ajouter un nouveau domaine][21]

    c. Sélectionnez **Ajouter un autre domaine**, puis tapez le nom du domaine que vous souhaitez ajouter.

    ![Entrer votre nom de domaine][22]

    d. Sélectionnez **Continuer et vérifier la propriété du domaine**. Puis suivez les étapes pour vérifier que vous possédez le nom de domaine. Pour obtenir des instructions complètes sur la vérification de votre domaine avec Google, consultez [Vérification de votre propriété de site](https://support.google.com/webmasters/answer/35179).

    e. Répétez la procédure précédente pour tous les domaines supplémentaires que vous souhaitez ajouter à G Suite.

5. Déterminez ensuite le compte d’administrateur à utiliser pour gérer l’approvisionnement des utilisateurs dans G Suite. Accédez à **Rôles d’administrateur** .

    ![Sélectionner Google Apps][26]
    
6. Pour le **Rôle d’administrateur** de ce compte, modifiez les **Privilèges** pour ce rôle. Veillez à activer tous les **Privilèges d’administrateur d’API** pour que ce compte puisse être utilisé pour l’approvisionnement.

    ![Sélectionner Google Apps][27]

## <a name="add-g-suite-from-the-gallery"></a>Ajouter G Suite à partir de la galerie

Pour configurer G Suite pour l’approvisionnement automatique d’utilisateurs avec Azure AD, vous devez ajouter G Suite à votre liste d’applications SaaS managées à partir de la galerie d’applications Azure AD. 

1. Dans le panneau de navigation gauche du **[portail Azure](https://portal.azure.com)** , sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut du volet.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **G Suite**, sélectionnez **G Suite** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![G Suite dans la liste des résultats](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-g-suite"></a>Configuration de l’approvisionnement automatique d’utilisateurs sur G Suite 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans G Suite en fonction des attributions d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour G Suite en suivant les instructions fournies dans le [didacticiel sur l’authentification unique pour G Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

> [!NOTE]
> Pour en savoir plus sur le point de terminaison de l’API d’annuaire G Suite, reportez-vous à l’[API Directory](https://developers.google.com/admin-sdk/directory).

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Pour configurer l’approvisionnement automatique d’utilisateurs pour G Suite dans Azure AD :

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **G Suite**.

    ![Lien G Suite dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Approvisionnement](common/provisioning-automatic.png)

5. Sous la section **Informations d’identification de l’administrateur**, sélectionnez **Autoriser**. Une boîte de dialogue d’autorisation Google s’ouvre dans une nouvelle fenêtre de navigateur.

    ![Autorisation G Suite](media/google-apps-provisioning-tutorial/authorize.png)

6. Confirmez que vous souhaitez autoriser Azure AD à apporter des modifications à votre abonné G Suite. Sélectionnez **Accepter**.

    ![Confirmez les autorisations.][28]

7. Sur le Portail Azure, sélectionnez **Tester la connexion** pour vérifier qu’Azure AD parvient à se connecter à votre application. Si la connexion échoue, vérifiez que votre compte G Suite dispose des autorisations Administrateur d’équipe. Ensuite, réessayez d’effectuer l’étape **Autoriser**.

8. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

8. Cliquez sur **Enregistrer**.

9. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec G Suite**.

    ![Mappages d'utilisateurs G Suite](media/google-apps-provisioning-tutorial/usermappings.png)

10. Dans la section **Mappage des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et G Suite. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes utilisateur dans G Suite pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Attributs utilisateur G Suite](media/google-apps-provisioning-tutorial/userattributes.png)

11. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec G Suite**.

    ![Mappages de groupe G Suite](media/google-apps-provisioning-tutorial/groupmappings.png)

12. Dans la section **Mappage des attributs**, passez en revue les attributs de groupe qui sont synchronisés entre Azure AD et G Suite. Les attributs sélectionnés comme propriétés de **Correspondance** sont utilisés pour la mise en correspondre des groupes dans G Suite dans le cadre des opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Attributs du groupe G Suite](media/google-apps-provisioning-tutorial/groupattributes.png)

13. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Pour activer le service d’approvisionnement Azure AD pour G Suite, passez l’**État de l’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

15. Définissez les utilisateurs et/ou les groupes que vous souhaitez approvisionner sur G Suite en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

16. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour surveiller la progression et les liens vers les rapports d’activité d’approvisionnement, qui décrivent toutes les actions effectuées par le service d’approvisionnement Azure AD sur G Suite.

Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)


<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
