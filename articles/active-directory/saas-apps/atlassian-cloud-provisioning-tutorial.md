---
title: 'Tutoriel : Configurer Atlassian Cloud pour le provisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour provisionner et déprovisionner automatiquement des comptes d’utilisateur sur Atlassian Cloud.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: 604dca2861b7a7126d2e37b5a01bcb85c530546e
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75561461"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Tutoriel : Configurer Atlassian Cloud pour le provisionnement automatique d’utilisateurs

L’objectif de ce tutoriel est de présenter les étapes à effectuer dans Atlassian Cloud et Azure Active Directory (Azure AD) afin de configurer Azure AD pour provisionner et déprovisionner automatiquement des utilisateurs et/ou des groupes sur Atlassian Cloud.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Conditions préalables requises

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* un locataire Azure AD ;
* [Un locataire Atlassian Cloud](https://www.atlassian.com/licensing/cloud)
* Un compte d’utilisateur dans Atlassian Cloud avec des autorisations d’administrateur.

> [!NOTE]
> L’intégration du provisionnement Azure AD repose sur l’**API SCIM Atlassian Cloud**, qui est disponible pour les équipes Atlassian Cloud.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Ajouter Atlassian Cloud à partir de la galerie

Avant de configurer Atlassian Cloud pour le provisionnement automatique d’utilisateurs avec Azure AD, vous devez ajouter Atlassian Cloud à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS managées.

**Pour ajouter Atlassian Cloud à partir de la galerie d’applications Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **[portail Azure](https://portal.azure.com)** , sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut du volet.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Atlassian Cloud**, sélectionnez **Atlassian Cloud** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Atlassian Cloud dans la liste des résultats](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Affectation d’utilisateurs à Atlassian Cloud

Azure Active Directory utilise un concept appelé *affectations* pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre d’une attribution automatique d’utilisateurs, seuls les utilisateurs ou les groupes auxquels une application a été attribuée dans Azure AD sont synchronisés.

Avant de configurer et d’activer le provisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à Atlassian Cloud. Une fois que vous avez choisi, vous pouvez affecter ces utilisateurs et/ou groupes à Atlassian Cloud en suivant les instructions fournies ici :

* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Conseils importants pour l’affectation d’utilisateurs à Atlassian Cloud

* Il est recommandé de n’affecter qu’un seul utilisateur Azure AD à Atlassian Cloud afin de tester la configuration du provisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Quand vous affectez un utilisateur à Atlassian Cloud, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’affectation. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Configuration du provisionnement automatique d’utilisateurs sur Atlassian Cloud 

Cette section vous guide tout au long des étapes de configuration du service de provisionnement Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Atlassian Cloud en fonction des affectations d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Atlassian Cloud en suivant les instructions fournies dans le [tutoriel sur l’authentification unique Atlassian Cloud](atlassian-cloud-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Pour configurer le provisionnement automatique d’utilisateurs pour Atlassian Cloud dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com) et sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Atlassian Cloud**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Atlassian Cloud**.

    ![Lien Atlassian Cloud dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Provisionnement Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Provisionnement Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. Accédez à [Atlassian Organization Manager](https://admin.atlassian.com), puis **sélectionnez Organization (Organisation) et Directory (Annuaire)** .

    ![Provisionnement Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

6. Cliquez sur **User Provisioning** (Attribution d’utilisateurs), puis sur **Create a directory** (Créer un annuaire). Copiez l’**URL de base de répertoire** et le **jeton du porteur** dans les champs **URL de locataire** et **Jeton secret** respectivement.

    ![Atlassian Cloud - Provisionnement](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Atlassian Cloud - Provisionnement](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png) ![Atlassian Cloud - Provisionnement](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. Sous la section **Informations d’identification de l’administrateur**, entrez l’**URL du locataire** et le **jeton secret** de votre compte Atlassian Cloud. Voici des exemples de valeurs :

   * Dans le champ **URL de locataire**, indiquez le point de terminaison de locataire spécifique reçu d’Atlassian, comme décrit à l’étape 6. Par exemple : `https://api.atlassian.com/scim/directory/{directoryId}`.

   * Dans le champ **Jeton secret**, spécifiez le jeton secret comme décrit à l’étape 6.

8. Après avoir renseigné les champs indiqués à l’étape 7, cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Atlassian Cloud. Si la connexion échoue, vérifiez que votre compte Atlassian Cloud dispose d’autorisations d’administrateur et réessayez.

    ![Provisionnement Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

9. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![Provisionnement Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/notification.png)

10. Cliquez sur **Enregistrer**.

11. Sous la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory sur Atlassian Cloud**.

    ![Provisionnement Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

12. Passez en revue les attributs utilisateur qui sont synchronisés d’Azure AD vers Atlassian Cloud dans la section **Mappages d’attributs**. Les attributs sélectionnés comme propriétés de **Correspondance** sont utilisés pour la mise en correspondance des comptes d’utilisateur dans Atlassian Cloud dans le cadre des opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Provisionnement Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

13. Sous la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory sur Atlassian Cloud**.

    ![Provisionnement Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

14. Passez en revue les attributs de groupe qui sont synchronisés d’Azure AD vers Atlassian Cloud dans la section **Mappages d’attributs**. Les attributs sélectionnés comme propriétés de **Correspondance** sont utilisés pour la mise en correspondance des groupes dans Atlassian Cloud dans le cadre des opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Provisionnement Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

15. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

16. Pour activer le service de provisionnement Azure AD pour Atlassian Cloud, définissez le paramètre **État du provisionnement** sur **Activé** dans la section **Paramètres**.

    ![Provisionnement Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

17. Définissez les utilisateurs et/ou groupes que vous aimeriez provisionner sur Atlassian Cloud en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Provisionnement Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

18. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Provisionnement Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/save.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour superviser la progression et suivre les liens vers le rapport d’activité de provisionnement, qui décrit toutes les actions effectuées par le service de provisionnement Azure AD sur Atlassian Cloud.

Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitations du connecteur

* Atlassian Cloud permet le provisionnement d’utilisateurs uniquement à partir de [domaines vérifiés](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* Atlassian Cloud ne prend pas en charge le renommage des groupes pour l’instant. Cela signifie que toute modification apportée à la propriété displayName d’un groupe dans Azure AD ne sera pas mise à jour et reflétée dans Atlassian Cloud.
* La valeur de l’attribut utilisateur **mail** dans Azure AD est renseignée uniquement si l’utilisateur possède une boîte aux lettres Microsoft Exchange. Si l’utilisateur n’en a pas, il est recommandé de mapper un autre attribut souhaité à l’attribut **e-mails** dans Atlassian Cloud.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png