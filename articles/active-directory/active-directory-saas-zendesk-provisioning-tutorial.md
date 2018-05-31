---
title: 'Tutoriel : Configurer Zendesk pour le provisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour provisionner et retirer automatiquement des comptes d’utilisateur sur Zendesk.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: v-ant
ms.openlocfilehash: 87ed987b53b45ef9dacb771d8dd057432712fb16
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34354113"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Tutoriel : Configurer Zendesk pour le provisionnement automatique d’utilisateurs

L’objectif de ce tutoriel est de présenter les étapes à effectuer dans Zendesk et Azure Active Directory (Azure AD) afin de configurer Azure AD pour le provisionnement et le retrait automatiques d’utilisateurs et/ou de groupes sur Zendesk. 

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Prérequis


Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

*   un locataire Azure AD ;
*   Un locataire Zendesk avec le [plan Entreprise](https://www.zendesk.com/product/pricing/) ou mieux activé 
*   Un compte d’utilisateur dans Zendesk avec des autorisations d’administrateur 

> [!NOTE]
> L’intégration du provisionnement Azure AD s’appuie sur l’[API Rest Zendesk](https://developer.zendesk.com/rest_api/docs/core/introduction), qui est disponible pour les équipes Zendesk disposant du forfait Entreprise ou mieux.

## <a name="adding-zendesk-from-the-gallery"></a>Ajout de Zendesk depuis la galerie
Avant de configurer Zendesk pour le provisionnement automatique d’utilisateurs avec Azure AD, vous devez ajouter Zendesk à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS managées.

**Pour ajouter Zendesk à partir de la galerie d’applications Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise** > **Toutes les applications**.

    ![Section Applications d’entreprise][2]
    
3. Pour ajouter Zendesk, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Zendesk**.

    ![Provisionnement de Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk6.png)

5. Dans le volet de résultats, sélectionnez **Zendesk**, puis cliquez sur le bouton **Ajouter** pour ajouter Zendesk à votre liste d’applications SaaS.

    ![Provisionnement de Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk7.png)

    ![Provisionnement de Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk20.png)

## <a name="assigning-users-to-zendesk"></a>Affectation d’utilisateurs à Zendesk

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique d’utilisateurs, seuls les utilisateurs et/ou les groupes qui ont été « assignés » à une application dans Azure AD sont synchronisés. 

Avant de configurer et d’activer le provisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à Zendesk. Une fois que vous avez choisi, vous pouvez assigner ces utilisateurs et/ou groupes à votre application Zendesk en suivant les instructions fournies ici :

*   [Affecter un utilisateur ou un groupe à une application d’entreprise](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Conseils importants pour l’affectation d’utilisateurs à Zendesk

*   Il est recommandé de n’affecter qu’un seul utilisateur Azure AD à Zendesk afin de tester la configuration du provisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

*   Quand vous assignez un utilisateur à Zendesk, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’assignation. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configuring-automatic-user-provisioning-to-zendesk"></a>Configuration du provisionnement automatique d’utilisateurs sur Zendesk 

Cette section vous guide tout au long des étapes de configuration du service de provisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Zendesk en fonction des assignations d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Zendesk en suivant les instructions fournies dans le [didacticiel sur l’authentification unique Zendesk](active-directory-saas-zendesk-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Pour configurer le provisionnement automatique d’utilisateurs pour Zendesk dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis accédez à la section **Azure Active Directory > Applications d’entreprise > Toutes les applications**.

2. Sélectionnez Zendesk dans votre liste d’applications SaaS.
 
    ![Provisionnement de Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk3.png)

3. Sélectionnez l’onglet **Approvisionnement**.
    
    ![Provisionnement de Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk16.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Provisionnement de Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk1.png)

5. Dans la section **Informations d’identification de l’administrateur**, entrez le **Nom d’utilisateur de l’administrateur**, le **Jeton secret** et le **Domaine** de votre compte Zendesk. Voici des exemples de valeurs :

    *   Dans le champ **Nom d’utilisateur de l’administrateur**, indiquez le nom de l’utilisateur du compte administrateur sur votre locataire Zendesk. Exemple : admin@contoso.com.

    *   Dans le champ **Jeton secret**, spécifiez le jeton secret comme décrit à l’étape 6.

    *   Dans le champ **Domaine**, spécifiez le sous-domaine de votre locataire Zendesk.
    Exemple : pour un compte avec une URL de locataire https://my-tenant.zendesk.com, votre sous-domaine serait **my-tenant**.

6. Le **Jeton secret** pour votre compte se trouve dans **Admin > API > Paramètres**. 

    ![Provisionnement de Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk4.png) ![Provisionnement de Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk2.png)

7. Après avoir renseigné les champs indiqués à l’étape 5, cliquez sur **Tester la connexion** pour vous assurer qu’Azure AD peut se connecter à Zendesk. Si la connexion échoue, vérifiez que votre compte Zendesk dispose des autorisations d’administrateur et réessayez.

    ![Provisionnement de Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk19.png)
    
8. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![Provisionnement de Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk9.png)

9. Cliquez sur **Enregistrer**.

10. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Zendesk**.

    ![Provisionnement de Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk10.png)

11. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Zendesk. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Zendesk pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Provisionnement de Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk11.png)

12. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec Zendesk**.

    ![Provisionnement de Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk12.png)

13. Dans la section **Mappages des attributs**, passez en revue les attributs groupe qui sont synchronisés entre Azure AD et Zendesk. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les groupes dans Zendesk pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Provisionnement de Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk13.png)

14. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](./active-directory-saas-scoping-filters.md).

15. Pour activer le service de provisionnement Azure AD pour Zendesk, modifiez le paramètre **État de mise en service** sur **Activé** dans la section **Paramètres**.

    ![Provisionnement de Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk14.png)

16. Définissez les utilisateurs et/ou groupes que vous souhaitez provisionner sur Zendesk en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Provisionnement de Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk15.png)

17. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Provisionnement de Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk18.png)


Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour surveiller la progression et les liens vers les rapports d’activité de provisionnement, qui décrivent toutes les actions effectuées par le service de provisionnement Azure AD sur Zendesk.

Pour plus d’informations sur la lecture des journaux d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](./active-directory-saas-provisioning-reporting.md).

## <a name="connector-limitations"></a>Limitations du connecteur
* Zendesk prend en charge l’utilisation de groupes pour les utilisateurs avec les rôles Agent uniquement. Pour plus d’informations, consultez la [documentation de Zendesk](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](active-directory-enterprise-apps-manage-provisioning.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux et obtenir des rapports sur l’activité d’approvisionnement](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_03.png
