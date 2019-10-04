---
title: 'Didacticiel : Configurer Visitly pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour provisionner et déprovisionner automatiquement des comptes d’utilisateurs dans Visitly.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 381f9cb2cb9ef196149144d40332a1de3b90f188
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802624"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>Didacticiel : Configurer Visitly pour le provisionnement automatique d’utilisateurs

L’objectif de ce tutoriel est de présenter les étapes à effectuer dans Visitly et Azure Active Directory (Azure AD) pour configurer Azure AD en vue de provisionner et déprovisionner des utilisateurs et/ou des groupes dans Visitly.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Ce connecteur est actuellement en préversion publique. Pour plus d’informations sur les conditions d’utilisation Microsoft Azure générales relatives aux fonctionnalités d’évaluation, consultez [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* Un locataire Azure AD.
* [Un locataire Visitly](https://www.visitly.io/pricing/).
* Un compte d’utilisateur Visitly avec des autorisations d’administrateur.

## <a name="assigning-users-to-visitly"></a>Affectation d’utilisateurs à Visitly 

Azure Active Directory utilise un concept appelé *affectations* pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre d’une attribution automatique d’utilisateurs, seuls les utilisateurs ou les groupes auxquels une application a été attribuée dans Azure AD sont synchronisés.

Avant de configurer et d’activer le provisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à Visitly. Une fois que vous avez choisi, vous pouvez affecter ces utilisateurs et/ou groupes à Visitly en suivant les instructions fournies ici :
* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>Conseils importants pour l’affectation d’utilisateurs à Visitly 

* Dans un premier temps, il est recommandé d’affecter un seul utilisateur Azure AD à Visitly pour tester la configuration du provisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Quand vous affectez un utilisateur à Visitly, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’affectation. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="setup-visitly-for-provisioning"></a>Configurer Visitly pour le provisionnement

Avant de configurer Visitly pour le provisionnement automatique d’utilisateurs avec Azure AD, vous devez activer le provisionnement SCIM dans Visitly.

1. Connectez-vous à [Visitly](https://app.visitly.io/login). Cliquez sur **Integrations** > **Host Synchronization**.

    ![Visitly](media/Visitly-provisioning-tutorial/login.png)

2. Faites défiler et sélectionnez la section **Azure AD**.

    ![Visitly](media/Visitly-provisioning-tutorial/integration.png)

3. Copiez la **Clé d’API**. Vous devrez entrer ces valeurs dans le champ **Jeton secret** sous l’onglet Provisioning de votre application Visitly sur le portail Azure.

    ![Visitly](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>Ajouter Visitly à partir de la galerie

Pour configurer Visitly pour le provisionnement automatique d’utilisateurs avec Azure AD, vous devez ajouter Visitly à votre liste d’applications SaaS managées à partir de la galerie d’applications Azure AD.

**Pour ajouter Visitly à partir de la galerie d’applications Azure AD, effectuez les étapes suivantes :**

1. Dans le panneau de navigation gauche du **[portail Azure](https://portal.azure.com)** , sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut du volet.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Visitly**, sélectionnez **Visitly** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Visitly dans la liste de résultats](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-visitly"></a>Configuration du provisionnement automatique d’utilisateurs dans Visitly  

Cette section vous guide tout au long des étapes de configuration du service de provisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Visitly en fonction des affectations d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez aussi choisir d’activer l’authentification unique basée sur SAML pour Visitly en suivant les instructions fournies dans le [tutoriel sur l’authentification unique Visitly](Visitly-tutorial.md). L’authentification unique peut être configurée indépendamment de l’attribution automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent

### <a name="to-configure-automatic-user-provisioning-for-visitly--in-azure-ad"></a>Pour configurer le provisionnement automatique d’utilisateurs pour Visitly dans Azure AD :

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Visitly**.

    ![Lien Visitly dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Approvisionnement](common/provisioning-automatic.png)

5. Sous la section Informations d’identification de l’administrateur, entrez respectivement les valeurs de ` https://api.visitly.io/v1/usersync/SCIM` et du **Clé d’API** récupérées précédemment dans les champs **URL de locataire** et **Jeton secret**. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Visitly. Si la connexion échoue, vérifiez que votre compte Visitly dispose des autorisations d’administrateur, puis réessayez.

    ![URL de locataire + Jeton](common/provisioning-testconnection-tenanturltoken.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Cliquez sur **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Visitly**.

    ![Visitly – Mappages d’utilisateurs](media/visitly-provisioning-tutorial/usermapping.png)

9. Dans la section **Mappage d’attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Visitly. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Visitly pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Visitly – Attributs utilisateur](media/visitly-provisioning-tutorial/userattribute.png)



10. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pour activer le service de provisionnement d’Azure AD pour Visitly, définissez le paramètre **État de l’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

12. Définissez les utilisateurs et/ou groupes que vous souhaitez approvisionner dans Visitly en choisissant les valeurs souhaitées dans **Étendue**, dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

13. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations ultérieures. Pour plus d’informations sur la durée d’approvisionnement des utilisateurs et/ou des groupes, voir [Combien de temps faut-il pour approvisionner des utilisateurs ?](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Vous pouvez utiliser la section **État actuel** pour superviser la progression et suivre les liens vers les rapports d’activité de provisionnement, qui décrivent toutes les actions effectuées par le service de provisionnement d’Azure AD dans Visitly. Pour plus d’informations, consultez [Vérifier l’état de l’approvisionnement d’utilisateurs](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). Pour la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitations du connecteur

Visitly ne prend pas en charge les suppressions définitives, seulement les suppressions réversibles.  

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)
