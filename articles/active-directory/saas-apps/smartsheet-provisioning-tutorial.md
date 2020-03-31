---
title: 'Tutoriel : Configurer Smartsheet pour l’attribution automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour provisionner et déprovisionner des comptes d’utilisateur dans Smartsheet.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: 9fbdf8a1c4b1881fc6dfd9d7b95a4103761e9ce7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063184"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Tutoriel : Configurer Smartsheet pour l’attribution automatique d’utilisateurs

L’objectif de ce didacticiel est de présenter les étapes à suivre dans Smartsheet et Azure Active Directory (Azure AD) afin de configurer Azure AD pour provisionner et déprovisionner automatiquement des utilisateurs et/ou des groupes dans Smartsheet.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ce connecteur est actuellement en préversion publique. Pour plus d’informations sur les conditions d’utilisation Microsoft Azure générales relatives aux fonctionnalités d’évaluation, consultez [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* un locataire Azure AD ;
* [Un locataire Smartsheet](https://www.smartsheet.com/pricing)
* Un compte d’utilisateur sur un plan Smartsheet Enterprise ou Enterprise Premier avec des autorisations d’administrateur système.

## <a name="assign-users-to-smartsheet"></a>Affecter des utilisateurs à Smartsheet

Azure Active Directory utilise un concept appelé *affectations* pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre d’une attribution automatique d’utilisateurs, seuls les utilisateurs ou les groupes auxquels une application a été attribuée dans Azure AD sont synchronisés.

Avant de configurer et d’activer l’attribution automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à Smartsheet. Une fois que vous avez choisi, vous pouvez assigner ces utilisateurs et/ou groupes à Smartsheet en suivant les instructions fournies ici :

* [Affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Conseils importants pour l’affectation d’utilisateurs à Smartsheet

* Il est recommandé de n’affecter qu’un seul utilisateur Azure AD à Smartsheet afin de tester la configuration de l’attribution automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

* Quand vous assignez un utilisateur à Smartsheet, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’attribution. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

* Pour garantir la parité dans les attributions de rôles d’utilisateur entre Smartsheet et Azure AD, il est recommandé d’utiliser les attributions de rôles renseignées dans la liste complète des utilisateurs Smartsheet. Pour récupérer cette liste d’utilisateurs à partir de Smartsheet, accédez à **Account Admin (Administrateur de compte) > User Management (Gestion des utilisateurs) > More Actions (Autres Actions) > Download User List (csv) (Télécharger la liste des utilisateurs (csv))** .

* Pour accéder à certaines fonctionnalités de l’application, Smartsheet exige d’un utilisateur qu’il possède plusieurs rôles. Pour en savoir plus sur les types d’utilisateurs et les autorisations dans Smartsheet, accédez à [User Types and Permissions (Types d’utilisateurs et autorisations)](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Si un utilisateur a plusieurs rôles dans Smartsheet, vous **DEVEZ** garantir que ces attributions de rôles sont répliquées dans Azure AD afin d’éviter un scénario où les utilisateurs pourraient perdre définitivement l’accès aux objets Smartsheet. Chaque rôle unique dans Smartsheet **DOIT** être attribué à un groupe différent dans Azure AD. L’utilisateur **DOIT** ensuite être ajouté à chacun des groupes correspondant aux rôles que vous souhaitez. 

## <a name="set-up-smartsheet-for-provisioning"></a>Configurer Smartsheet pour le provisionnement

Avant de configurer Smartsheet pour l’attribution automatique d’utilisateurs avec Azure AD, vous devez activer le provisionnement SCIM sur Smartsheet.

1. Connectez-vous en tant que **SysAdmin** dans le **[portail Smartsheet](https://app.smartsheet.com/b/home)** et accédez à l’**administrateur de compte**.

    ![Administrateur de compte Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Accédez à **Security Controls (Contrôles de sécurité) > User Auto Provisioning (Attribution automatique d’utilisateurs) > Edit (Modifier)** .

    ![Contrôles de sécurité Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Ajoutez et validez les domaines de courrier pour les utilisateurs que vous envisagez d’approvisionner à partir d’Azure AD dans Smartsheet. Choisissez **Not Enabled (Non activé)** pour vous assurer que toutes les actions de provisionnement proviennent uniquement d’Azure AD et également pour vous assurer que votre liste d’utilisateurs Smartsheet est synchronisée avec les attributions d’Azure AD.

    ![Attribution d’utilisateurs Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Une fois la validation terminée, vous devez activer le domaine. 

    ![Activation du domaine Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Générez le **jeton secret** requis pour configurer l’attribution automatique d’utilisateurs avec Azure AD en accédant à **Apps and Integrations (Applications et intégrations)** .

    ![Installation de Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Choisissez **Accès d’API**. Cliquez sur **Generate new access token (Générer un nouveau jeton d'accès)** .

    ![Installation de Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Définissez le nom du jeton d’accès d’API. Cliquez sur **OK**.

    ![Installation de Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Copiez le jeton d’accès d’API et enregistrez-le car il s’agit de la seule fois où vous pouvez le consulter. Il est requis dans le champ **Jeton secret**, dans Azure AD.

    ![Jeton Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>Ajouter Smartsheet à partir de la galerie

Avant de configurer Smartsheet pour l’attribution automatique d’utilisateurs avec Azure AD, vous devez ajouter Smartsheet à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS managées.

1. Dans le panneau de navigation gauche du **[portail Azure](https://portal.azure.com)** , sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut du volet.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Smartsheet** et sélectionnez **Smartsheet** dans le volet de résultats. 

    ![Smartsheet dans la liste des résultats](common/search-new-app.png)

5. Sélectionnez le bouton **S’inscrire à Smartsheet** qui vous redirigera vers la page de connexion de Smartsheet. 

    ![Ajout de l’application OIDC Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Comme Smartsheet est une application OpenIDConnect, choisissez de vous connecter à Smartsheet à l’aide de votre compte professionnel Microsoft.

    ![Connexion à l’application OIDC Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. Une fois l’authentification réussie, acceptez l’invite de consentement pour la page de consentement. L’application est alors automatiquement ajoutée à votre client et vous serez redirigé vers votre compte Smartsheet.

    ![Consentement de l’application OIDC Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Configurer l’attribution automatique d’utilisateurs dans Smartsheet 

Cette section vous guide tout au long des étapes de configuration du service de provisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Smartsheet en fonction des attributions d’utilisateurs et/ou de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Pour configurer l’attribution automatique d’utilisateurs pour Smartsheet dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Smartsheet**.

    ![Lien Smartsheet dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Approvisionnement](common/provisioning-automatic.png)

5. Dans la section **Informations d’identification de l’administrateur**, entrez `https://scim.smartsheet.com/v2/` dans **URL de locataire**. Entrez la valeur que vous avez récupérée et enregistrée précédemment à partir de Smartsheet dans **Jeton secret**. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Smartsheet. Si la connexion échoue, vérifiez que votre compte Smartsheet dispose d’autorisations d’administrateur système et réessayez.

    ![par jeton](common/provisioning-testconnection-tenanturltoken.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Cliquez sur **Enregistrer**.

8. Sous la section **Mappages**, sélectionnez **Synchronize Azure Active Directory Users to Smartsheet (Synchroniser les utilisateurs Azure Active Directory dans Smartsheet)** .

    ![Mappages des utilisateurs Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Passez en revue les attributs utilisateur qui sont synchronisés d’Azure AD vers Smartsheet dans la section **Mappage d’attributs**. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour mettre en correspondance les comptes d’utilisateur dans Smartsheet pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Attributs utilisateur Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pour activer le service de provisionnement Azure AD pour Smartsheet, modifiez le paramètre **État de l’approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

12. Définissez les utilisateurs et/ou groupes que vous aimeriez provisionner dans Smartsheet en choisissant les valeurs souhaitées dans **Étendue**, dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

13. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour surveiller la progression et suivre les liens vers le rapport d’activité de provisionnement, qui décrit toutes les actions effectuées par le service de provisionnement Azure AD sur Smartsheet.

Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitations du connecteur

* Smartsheet ne prend pas en charge les suppressions réversibles. Lorsque l’attribut **actif** d’un utilisateur est défini sur False, Smartsheet supprime l’utilisateur de façon permanente.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)
