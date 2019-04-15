---
title: 'Didacticiel : Configurer Atlassian Cloud pour l’approvisionnement automatique avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et retirer automatiquement des comptes d’utilisateur dans Atlassian Cloud.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.openlocfilehash: f4e76121f7815702270d6601413ff7a4c2c25839
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270300"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Didacticiel : Configurer Atlassian Cloud pour l’approvisionnement automatique

L’objectif de ce didacticiel est de présenter les étapes à effectuer dans Atlassian Cloud et Azure Active Directory (Azure AD) pour configurer Azure AD pour approvisionner et retirer automatiquement les utilisateurs et/ou groupes à Atlassian Cloud.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ce connecteur est actuellement en version préliminaire publique. Pour plus d’informations sur les conditions d’utilisation Microsoft Azure générales pour les fonctionnalités en version préliminaire, consultez [conditions d’utilisation supplémentaires pour les versions préliminaires de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Conditions préalables

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* un locataire Azure AD ;
* [Un locataire Atlassian Cloud](https://www.atlassian.com/licensing/cloud)
* Un compte d’utilisateur dans Atlassian Cloud avec des autorisations d’administrateur.

> [!NOTE]
> Intégration de l’approvisionnement Azure AD s’appuie sur le **Atlassian Cloud SCIM API**, qui est disponible pour les équipes d’Atlassian Cloud.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Ajouter Atlassian Cloud à partir de la galerie

Avant de configurer Atlassian Cloud pour l’approvisionnement avec Azure AD automatique d’utilisateurs, vous devez ajouter Atlassian Cloud à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS gérées.

**Pour ajouter Atlassian Cloud à partir de la galerie d’applications Azure AD, procédez comme suit :**

1. Dans le  **[Azure portal](https://portal.azure.com)**, dans le volet de navigation de gauche, sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, sélectionnez le **nouvelle application** bouton en haut du volet.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Atlassian Cloud**, sélectionnez **Atlassian Cloud** dans le volet de résultats, puis cliquez sur le **ajouter** pour ajouter l’application.

    ![Atlassian Cloud dans la liste des résultats](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Affectation d’utilisateurs à Atlassian Cloud

Azure Active Directory utilise un concept appelé *affectations* pour déterminer quels utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le contexte de l’approvisionnement automatique, seuls les utilisateurs et/ou les groupes qui ont été assignés à une application dans Azure AD sont synchronisés.

Avant de configurer et activer l’approvisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à Atlassian Cloud. Une fois choisi, vous pouvez affecter ces utilisateurs et/ou groupes à Atlassian Cloud en suivant les instructions fournies ici :

* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Conseils importants pour l’affectation d’utilisateurs à Atlassian Cloud

* Il est recommandé qu’un seul utilisateur Azure AD est affecté à Atlassian Cloud pour tester la configuration du provisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Quand vous assignez un utilisateur à Atlassian Cloud, vous devez sélectionner un rôle spécifique à l’application valide (si disponible) dans la boîte de dialogue d’attribution. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Configuration de l’approvisionnement automatique d’utilisateurs à Atlassian Cloud 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement de AD Azure pour créer, mettre à jour et désactiver des utilisateurs et/ou groupes dans Atlassian Cloud en fonction des utilisateurs et/ou des affectations de groupe dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer basée sur SAML SSO pour Atlassian Cloud en suivant les instructions fournies dans le [didacticiel l’authentification unique de l’Atlassian Cloud](atlassian-cloud-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Pour configurer l’approvisionnement automatique pour Atlassian Cloud dans Azure AD :

1. Se connecter à la [Azure portal](https://portal.azure.com) et sélectionnez **Applications d’entreprise**, sélectionnez **toutes les applications**, puis sélectionnez **Atlassian Cloud**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Atlassian Cloud**.

    ![Lien Atlassian Cloud dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Approvisionnement de Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Approvisionnement de Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. Sous le **informations d’identification administrateur** section, entrée le **URL de locataire** et **jeton Secret** du compte de votre Atlassian Cloud. Voici des exemples de valeurs :

   * Dans le **URL de locataire** champ, de remplir le point de terminaison client spécifique que vous recevez à partir de la Atlassian, comme décrit à l’étape 6. Par exemple : `https://api.atlassian.com/scim/directory/{directoryId}`.

   * Dans le champ **Jeton secret**, spécifiez le jeton secret comme décrit à l’étape 6.

6. Accédez à [Atlassian organisation Manager](https://admin.atlassian.com) **> approvisionnement** , puis cliquez sur **créer un jeton**. Copie le **URL de base de répertoire** et **le jeton du porteur** à la **URL de locataire** et **jeton Secret** champs respectivement.

    ![Approvisionnement du Cloud Atlassian](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Atlassian Cloud d’approvisionnement](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)

    ![Approvisionnement de Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. Après avoir renseigné les champs indiqués à l’étape 5, cliquez sur **tester la connexion** pour vérifier qu’Azure AD peut se connecter à Atlassian Cloud. Si la connexion échoue, vérifiez que votre compte de Atlassian Cloud dispose des autorisations d’administrateur et réessayez.

    ![Approvisionnement de Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

8. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![Approvisionnement de Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/notification.png)

9. Cliquez sur **Enregistrer**.

10. Sous le **mappages** section, sélectionnez **synchroniser les utilisateurs Azure Active Directory à Atlassian Cloud**.

    ![Approvisionnement de Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

11. Passez en revue les attributs utilisateur qui sont synchronisés à partir d’Azure AD à Atlassian Cloud dans le **mappage d’attributs** section. Les attributs sélectionnés en tant que **correspondance** propriétés sont utilisées pour faire correspondre les comptes d’utilisateur dans Atlassian Cloud pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Approvisionnement de Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

12. Sous le **mappages** section, sélectionnez **synchroniser les groupes Azure Active Directory à Atlassian Cloud**.

    ![Approvisionnement de Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

13. Passez en revue les attributs groupe qui sont synchronisés à partir d’Azure AD à Atlassian Cloud dans le **mappage d’attributs** section. Les attributs sélectionnés en tant que **correspondance** propriétés sont utilisées pour faire correspondre les groupes dans Atlassian Cloud pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Approvisionnement de Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

14. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Pour activer l’approvisionnement de service pour Atlassian Cloud Azure AD, modifiez le **état d’approvisionnement** à **sur** dans le **paramètres** section.

    ![Approvisionnement de Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

16. Définissez les utilisateurs et/ou groupes que vous aimeriez approvisionner sur Atlassian Cloud en choisissant les valeurs souhaitées dans **étendue** dans le **paramètres** section.

    ![Approvisionnement de Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

17. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Approvisionnement de Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/save.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la **détails de la synchronisation** section pour surveiller la progression et suivre les liens vers les rapports d’activité, qui décrit toutes les actions effectuées par le service sur Atlassian Cloud de provisionnement Azure AD d’approvisionnement.

Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitations du connecteur

* Atlassian Cloud permet l’approvisionnement d’utilisateurs uniquement à partir de [domaines vérifiés](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* Atlassian Cloud ne prend pas en charge les affectations de groupe dès aujourd'hui. Cela signifie que toute modification apportée à la propriété displayName d’un groupe dans Azure AD ne sera pas mis à jour et reflétées dans Atlassian Cloud.
* La valeur de la **mail** attribut utilisateur dans Azure AD est rempli uniquement si l’utilisateur possède une boîte aux lettres Exchange de Microsoft. Si l’utilisateur n’a pas un, il est recommandé pour mapper un autre attribut souhaité pour le **e-mails** attribut dans Atlassian Cloud.

## <a name="additional-resources"></a>Ressources supplémentaires

* [La gestion de l’approvisionnement de comptes utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
