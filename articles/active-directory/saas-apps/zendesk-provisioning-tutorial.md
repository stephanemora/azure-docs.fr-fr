---
title: 'Didacticiel : Configurer Zendesk pour le provisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour provisionner et retirer automatiquement des comptes d’utilisateur sur Zendesk.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 01d5e4d5-d856-42c4-a504-96fa554baf66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf747fb75ea663d2c64038d73f48adb19d9fb804
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59056586"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Didacticiel : Configurer Zendesk pour le provisionnement automatique d’utilisateurs

L’objectif de ce tutoriel est de présenter les étapes à effectuer dans Zendesk et Azure Active Directory (Azure AD) afin de configurer Azure AD pour le provisionnement et le retrait automatiques d’utilisateurs et/ou de groupes sur Zendesk.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Conditions préalables

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* un locataire Azure AD ;
* Un locataire Zendesk avec le [plan Entreprise](https://www.zendesk.com/product/pricing/) ou mieux activé
* Un compte d’utilisateur dans Zendesk avec des autorisations d’administrateur

> [!NOTE]
> L’intégration du provisionnement Azure AD s’appuie sur l’[API Rest Zendesk](https://developer.zendesk.com/rest_api/docs/core/introduction), qui est disponible pour les équipes Zendesk disposant du forfait Entreprise ou mieux.

## <a name="adding-zendesk-from-the-gallery"></a>Ajout de Zendesk depuis la galerie

Avant de configurer Zendesk pour le provisionnement automatique d’utilisateurs avec Azure AD, vous devez ajouter Zendesk à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS managées.

**Pour ajouter Zendesk à partir de la galerie d’applications Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **Zendesk**, sélectionnez **Zendesk** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Zendesk dans la liste des résultats](common/search-new-app.png)

## <a name="assigning-users-to-zendesk"></a>Affectation d’utilisateurs à Zendesk

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique d’utilisateurs, seuls les utilisateurs et/ou les groupes qui ont été « assignés » à une application dans Azure AD sont synchronisés.

Avant de configurer et d’activer le provisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à Zendesk. Une fois que vous avez choisi, vous pouvez assigner ces utilisateurs et/ou groupes à votre application Zendesk en suivant les instructions fournies ici :

* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Conseils importants pour l’affectation d’utilisateurs à Zendesk

* Les rôles Zendesk sont désormais indiqués de manière dynamique et automatique dans l’interface utilisateur du Portail Azure. Avant d’assigner des rôles Zendesk aux utilisateurs, assurez-vous que la synchronisation initiale est effectuée par rapport à Zendesk afin de récupérer les rôles les plus récents dans votre tenant Zendesk.

* Il est recommandé d’assigner un seul utilisateur Azure AD à Zendesk afin de tester la configuration initiale du provisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être assignés ultérieurement, une fois les tests réussis.
  
* Il est recommandé de n’affecter qu’un seul utilisateur Azure AD à Zendesk afin de tester la configuration du provisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Quand vous assignez un utilisateur à Zendesk, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’assignation. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configuring-automatic-user-provisioning-to-zendesk"></a>Configuration du provisionnement automatique d’utilisateurs sur Zendesk 

Cette section vous guide tout au long des étapes de configuration du service de provisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Zendesk en fonction des assignations d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Zendesk en suivant les instructions fournies dans le [didacticiel sur l’authentification unique Zendesk](zendesk-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Pour configurer le provisionnement automatique d’utilisateurs pour Zendesk dans Azure AD :

1. Se connecter à la [Azure portal](https://portal.azure.com) et sélectionnez **Applications d’entreprise**, sélectionnez **toutes les applications**, puis sélectionnez **Zendesk**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Zendesk**.

    ![Lien Zendesk dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Provisionnement de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Provisionnement de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. Dans la section **Informations d’identification de l’administrateur**, entrez le **Nom d’utilisateur de l’administrateur**, le **Jeton secret** et le **Domaine** de votre compte Zendesk. Voici des exemples de valeurs :

   * Dans le champ **Nom d’utilisateur de l’administrateur**, indiquez le nom de l’utilisateur du compte administrateur sur votre locataire Zendesk. Exemple : admin@contoso.com.

   * Dans le champ **Jeton secret**, spécifiez le jeton secret comme décrit à l’étape 6.

   * Dans le champ **Domaine**, spécifiez le sous-domaine de votre locataire Zendesk.
     Exemple : pour un compte avec une URL de tenant `https://my-tenant.zendesk.com`, votre sous-domaine serait **my-tenant**.

6. Le **Jeton secret** pour votre compte se trouve dans **Admin > API > Paramètres**.
   Assurez-vous que **Jeton d'accès** est défini sur **Activé**.

    ![Provisionnement de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Provisionnement de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Après avoir renseigné les champs indiqués à l’étape 5, cliquez sur **Tester la connexion** pour vous assurer qu’Azure AD peut se connecter à Zendesk. Si la connexion échoue, vérifiez que votre compte Zendesk dispose des autorisations d’administrateur et réessayez.

    ![Provisionnement de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![Provisionnement de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Cliquez sur **Enregistrer**.

10. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Zendesk**.

    ![Provisionnement de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Zendesk. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Zendesk pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Provisionnement de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec Zendesk**.

    ![Provisionnement de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Dans la section **Mappages des attributs**, passez en revue les attributs groupe qui sont synchronisés entre Azure AD et Zendesk. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les groupes dans Zendesk pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Provisionnement de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Pour activer le service de provisionnement Azure AD pour Zendesk, modifiez le paramètre **État de mise en service** sur **Activé** dans la section **Paramètres**.

    ![Provisionnement de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Définissez les utilisateurs et/ou groupes que vous souhaitez provisionner sur Zendesk en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Provisionnement de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Provisionnement de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour surveiller la progression et les liens vers les rapports d’activité de provisionnement, qui décrivent toutes les actions effectuées par le service de provisionnement Azure AD sur Zendesk.

Pour plus d’informations sur la lecture des journaux d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitations du connecteur

* Zendesk prend en charge l’utilisation de groupes pour les utilisateurs avec les rôles Agent uniquement. Pour plus d’informations, consultez la [documentation de Zendesk](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

* Quand un rôle personnalisé est affecté à un utilisateur et/ou groupe, le service de provisionnement automatique d’utilisateurs Azure AD affecte également le rôle par défaut **Agent**. Seuls les **Agents** peuvent recevoir un rôle personnalisé. Pour plus d’informations, reportez-vous à la [documentation sur les API Zendesk](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests).  

## <a name="additional-resources"></a>Ressources supplémentaires

* [La gestion de l’approvisionnement de comptes utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
