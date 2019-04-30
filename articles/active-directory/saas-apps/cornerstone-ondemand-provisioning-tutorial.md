---
title: 'Didacticiel : Configurer Cornerstone OnDemand pour le provisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et annuler l’approvisionnement de comptes d’utilisateurs à Cornerstone OnDemand automatiquement.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85ddcf3aff7d15c946230cedb0da190bca6aeab7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127495"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Didacticiel : Configurer Cornerstone OnDemand pour le provisionnement automatique d’utilisateurs

Ce didacticiel présente les étapes à effectuer dans Cornerstone OnDemand et Azure Active Directory (Azure AD) pour configurer Azure AD pour approvisionner et annuler le déploiement d’utilisateurs ou des groupes à Cornerstone OnDemand automatiquement.

> [!NOTE]
> Ce didacticiel décrit un connecteur qui repose sur l’utilisateur Azure AD service d’approvisionnement. Pour plus d’informations sur ce service, son fonctionnement et les questions fréquemment posées, consultez [automatiser l’approvisionnement et annulation d’approvisionnement pour les applications de software-as-a-service (SaaS) avec Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Conditions préalables

Le scénario décrit dans ce didacticiel suppose que vous avez :

* Un locataire Azure AD.
* Un locataire Cornerstone OnDemand.
* Un compte d’utilisateur dans Cornerstone OnDemand avec des autorisations d’administrateur.

> [!NOTE]
> Intégration de l’approvisionnement Azure AD s’appuie sur le [service web de Cornerstone OnDemand](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf). Ce service est disponible pour les équipes de Cornerstone OnDemand.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Ajouter Cornerstone OnDemand à partir de la place de marché Azure

Avant de configurer Cornerstone OnDemand pour l’approvisionnement avec Azure AD automatique d’utilisateurs, ajouter Cornerstone OnDemand à partir de la place de marché à votre liste d’applications SaaS gérées.

Pour ajouter Cornerstone OnDemand à partir de la place de marché, procédez comme suit.

1. Dans le [Azure portal](https://portal.azure.com), dans le volet de navigation de gauche, sélectionnez **Azure Active Directory**.

    ![L’icône Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une application, sélectionnez **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Cornerstone OnDemand** et sélectionnez **Cornerstone OnDemand** le volet de résultats. Pour ajouter l’application, sélectionnez **ajouter**.

    ![Cornerstone OnDemand dans la liste des résultats](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Affecter des utilisateurs à Cornerstone OnDemand

Azure Active Directory utilise un concept appelé *affectations* pour déterminer quels utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le contexte de l’approvisionnement automatique, uniquement les utilisateurs ou les groupes qui ont été affectés à une application dans Azure AD sont synchronisés.

Avant de configurer et activer l’approvisionnement automatique d’utilisateurs, déterminer quels utilisateurs ou groupes dans Azure AD ont besoin d’accéder à Cornerstone OnDemand. Pour affecter ces utilisateurs ou groupes à Cornerstone OnDemand, suivez les instructions de [affecter un utilisateur ou un groupe à une application d’entreprise](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Conseils importants pour l’assignation d’utilisateurs à Cornerstone OnDemand

* Nous vous recommandons d’affecter un seul utilisateur Azure AD à Cornerstone OnDemand pour tester la configuration du provisionnement automatique d’utilisateurs. Vous pouvez affecter des utilisateurs ou des groupes plus tard.

* Lorsque vous affectez un utilisateur à Cornerstone OnDemand, sélectionnez un rôle spécifique à l’application valide, s’il est disponible, dans la boîte de dialogue d’attribution. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Configurer l’approvisionnement automatique d’utilisateurs à Cornerstone OnDemand

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD. Utilisez-le pour créer, mettre à jour et désactiver des utilisateurs ou groupes dans Cornerstone OnDemand basé sur les affectations de groupe ou utilisateur dans Azure AD.

Pour configurer l’approvisionnement automatique pour Cornerstone OnDemand dans Azure AD, procédez comme suit.

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Sélectionnez **applications d’entreprise** > **toutes les applications** > **Cornerstone OnDemand**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Cornerstone OnDemand**.

    ![Le lien de Cornerstone OnDemand dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Approvisionnement Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Mode d’approvisionnement cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Sous le **informations d’identification administrateur** section, entrez le nom d’utilisateur administrateur, le mot de passe administrateur et le domaine de votre compte Cornerstone OnDemand :

    * Dans le **Admin Username** zone, indiquez le domaine ou le nom d’utilisateur du compte d’administrateur sur votre locataire Cornerstone OnDemand. Contoso\admin est un exemple.

    * Dans le **mot de passe administrateur** zone, renseignez le mot de passe qui correspond au nom d’utilisateur administrateur.

    * Dans le **domaine** zone, entrez l’URL de service web du locataire Cornerstone OnDemand. Par exemple, le service est situé à `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`, et pour Contoso est le domaine `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`. Pour plus d’informations sur la façon de récupérer l’URL du service web, consultez [ce fichier pdf](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. Une fois que vous remplissez les zones affichées à l’étape 5, sélectionnez **tester la connexion** pour vous assurer qu’Azure AD peut se connecter à Cornerstone OnDemand. Si la connexion échoue, assurez-vous que votre compte Cornerstone OnDemand dispose des autorisations d’administrateur et réessayez.

    ![Cornerstone OnDemand tester la connexion](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. Dans le **E-mail de Notification** zone, entrez l’adresse e-mail de la personne ou le groupe pour recevoir les notifications d’erreur d’approvisionnement. Sélectionnez le **envoyer une notification par e-mail lorsqu’une défaillance se produit** case à cocher.

    ![E-mail de Notification de cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Sélectionnez **Enregistrer**.

9. Dans la section **Mappages**, sélectionnez **Synchronize Azure Active Directory Users to Cornerstone OnDemand** (Synchroniser des utilisateurs Azure Active Directory avec Cornerstone OnDemand).

    ![Synchronisation de cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Passez en revue les attributs utilisateur qui sont synchronisés à partir d’Azure AD à Cornerstone OnDemand dans le **des mappages d’attributs** section. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Cornerstone OnDemand pour les opérations de mise à jour. Pour enregistrer les modifications, sélectionnez **enregistrer**.

    ![Pierre angulaire des mappages d’attributs de la demande](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Pour configurer des filtres d’étendue, suivez les instructions de la [didacticiel de filtre d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Pour activer l’approvisionnement de service pour Cornerstone OnDemand, dans Azure AD le **paramètres** section, remplacez **état d’approvisionnement** à **sur**.

    ![État d’approvisionnement cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Définissez les utilisateurs ou les groupes auxquels vous souhaitez approvisionner sur Cornerstone OnDemand. Dans le **paramètres** , sélectionnez les valeurs souhaitées dans **étendue**.

    ![Cornerstone OnDemand étendue](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Lorsque vous êtes prêt à déployer, sélectionnez **enregistrer**.

    ![Cornerstone OnDemand enregistrer](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs ou groupes définis dans **étendue** dans le **paramètres** section. La synchronisation initiale prend plus de temps que les synchronisations ultérieures. Elles se produisent toutes les 40 minutes environ tant que le service d’approvisionnement AD Azure s’exécute. 

Vous pouvez utiliser la **détails de la synchronisation** section pour surveiller la progression et suivre les liens vers le rapport d’activité d’approvisionnement. Le rapport décrit toutes les actions effectuées par le service sur Cornerstone OnDemand de provisionnement Azure AD.

Pour avoir des informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitations du connecteur

L’attribut **Position** Cornerstone OnDemand attend une valeur correspondant aux rôles du portail Cornerstone OnDemand. Pour obtenir une liste de valides **Position** valeurs, accédez à **modifier un enregistrement utilisateur > Structure de l’organisation > Position** dans le portail de Cornerstone OnDemand.

![Approvisionnement cornerstone OnDemand modifier l’enregistrement d’utilisateur](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Position de l’approvisionnement cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Liste de position cornerstone OnDemand approvisionnement](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion du provisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
