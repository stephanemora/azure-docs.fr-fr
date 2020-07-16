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
ms.openlocfilehash: 95455d389669b89075ca0ea8583cc7858bb532f0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85317622"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Tutoriel : Configurer Atlassian Cloud pour le provisionnement automatique d’utilisateurs

L’objectif de ce tutoriel est de présenter les étapes à effectuer dans Atlassian Cloud et Azure Active Directory (Azure AD) afin de configurer Azure AD pour provisionner et déprovisionner automatiquement des utilisateurs et/ou des groupes sur [Atlassian Cloud](https://www.atlassian.com/licensing/cloud). Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Fonctionnalités prises en charge
> [!div class="checklist"]
> * Créer des utilisateurs dans Atlassian Cloud
> * Supprimer les utilisateurs dans Atlassian Cloud quand ils n’ont plus besoin d’accès
> * Conserver les attributs utilisateur synchronisés entre Azure AD et Atlassian Cloud
> * Provisionner des groupes et des appartenances aux groupes dans Atlassian Cloud
> * Utiliser l’[authentification unique](https://docs.microsoft.com/azure/active-directory/saas-apps/atlassian-cloud-tutorial) dans Atlassian Cloud (recommandé)

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

* [Un locataire Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Un compte d’utilisateur dans Azure AD avec l’[autorisation](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) de configurer l’approvisionnement (par exemple, Administrateur d’application, Administrateur d’application cloud, Propriétaire d’application ou Administrateur général).
* [Un locataire Atlassian Cloud](https://www.atlassian.com/licensing/cloud)
* Un compte d’utilisateur dans Atlassian Cloud avec des autorisations d’administrateur.

## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement
1. En savoir plus sur le [fonctionnement du service d’approvisionnement](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Déterminez qui sera dans l’[étendue pour l’approvisionnement](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Déterminez les données à [mapper entre Azure AD et Atlassian Cloud](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-atlassian-cloud-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer Atlassian Cloud pour prendre en charge le provisionnement avec Azure AD

1. Accédez à [Atlassian Organization Manager](https://admin.atlassian.com), puis **sélectionnez Organization (Organisation) et Directory (Annuaire)** .

    ![Provisionnement Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

2. Cliquez sur **User Provisioning** (Attribution d’utilisateurs), puis sur **Create a directory** (Créer un annuaire). Copiez les valeurs **Directory base URL** (URL de base de l’annuaire) et **Bearer Token** (Jeton du porteur) qui seront entrées respectivement dans les champs **Tenant URL** (URL du locataire) et **Secret Token** (Jeton secret) sous l’onglet Provisioning (Provisionnement) de votre application Cloud Atlassian dans le portail Azure AD.

    ![Atlassian Cloud - Provisionnement](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Atlassian Cloud - Provisionnement](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png) ![Atlassian Cloud - Provisionnement](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)


## <a name="step-3-add-atlassian-cloud-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter Atlassian Cloud à partir de la galerie d’applications Azure AD

Ajoutez Atlassian Cloud à partir de la galerie d’applications Azure AD pour commencer à gérer le provisionnement dans Atlassian Cloud. Si vous avez déjà configuré Atlassian Cloud pour l’authentification unique, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement 

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Quand vous provisionnez des utilisateurs et des groupes dans Atlassian Cloud, vous devez sélectionner un autre rôle que le rôle **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible dans l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) pour ajouter des rôles supplémentaires. 

* Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Étape 5. Configuration du provisionnement automatique d’utilisateurs sur Atlassian Cloud 

Cette section vous guide tout au long des étapes de configuration du service de provisionnement Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Atlassian Cloud en fonction des affectations d’utilisateurs et/ou de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Pour configurer le provisionnement automatique d’utilisateurs pour Atlassian Cloud dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com) et sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **Atlassian Cloud**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Atlassian Cloud**.

    ![Lien Atlassian Cloud dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Onglet Approvisionnement](common/provisioning-automatic.png)

5. Sous la section **Informations d’identification de l’administrateur**, entrez l’**URL du locataire** et le **jeton secret** que vous avez récupérés précédemment de votre compte Atlassian Cloud. Cliquez sur **Tester la connexion** pour vous assurer qu’Azure AD peut se connecter à Atlassian Cloud. Si la connexion échoue, vérifiez que votre compte Atlassian Cloud dispose d’autorisations d’administrateur et réessayez.

    ![URL de locataire + Jeton](common/provisioning-testconnection-tenanturltoken.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification](common/provisioning-notification-email.png)

7. Cliquez sur **Enregistrer**.

8. Sous la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory sur Atlassian Cloud**.

9. Passez en revue les attributs utilisateur qui sont synchronisés d’Azure AD vers Atlassian Cloud dans la section **Mappages d’attributs**. Les attributs sélectionnés comme propriétés de **Correspondance** sont utilisés pour la mise en correspondance des comptes d’utilisateur dans Atlassian Cloud dans le cadre des opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

   |Attribut|Type|
   |---|---|
   |userName|String|
   |active|Boolean|
   |name.familyName|String|
   |name.givenName|String|
   |emails[type eq "work"].value|String|   

10. Sous la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory sur Atlassian Cloud**.

11. Passez en revue les attributs de groupe qui sont synchronisés d’Azure AD vers Atlassian Cloud dans la section **Mappages d’attributs**. Les attributs sélectionnés comme propriétés de **Correspondance** sont utilisés pour la mise en correspondance des groupes dans Atlassian Cloud dans le cadre des opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

      |Attribut|Type|
      |---|---|
      |displayName|String|
      |externalId|String|
      |membres|Informations de référence|

12. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pour activer le service de provisionnement Azure AD pour Atlassian Cloud, définissez le paramètre **État du provisionnement** sur **Activé** dans la section **Paramètres**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

14. Définissez les utilisateurs et/ou groupes que vous aimeriez provisionner sur Atlassian Cloud en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

16. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution.

## <a name="step-6-monitor-your-deployment"></a>Étape 6. Surveiller votre déploiement
Une fois que vous avez configuré l’approvisionnement, utilisez les ressources suivantes pour surveiller votre déploiement :

1. Utilisez les [journaux d’approvisionnement](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) pour déterminer quels utilisateurs ont été configurés avec succès ou échoué.
2. Consultez la [barre de progression](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) pour afficher l’état du cycle d’approvisionnement et quand il se termine
3. Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour en savoir plus sur les états de quarantaine, cliquez [ici](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="connector-limitations"></a>Limitations du connecteur

* Atlassian Cloud permet le provisionnement d’utilisateurs uniquement à partir de [domaines vérifiés](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* Atlassian Cloud ne prend pas en charge le renommage des groupes pour l’instant. Cela signifie que toute modification apportée à la propriété displayName d’un groupe dans Azure AD ne sera pas mise à jour et reflétée dans Atlassian Cloud.
* La valeur de l’attribut utilisateur **mail** dans Azure AD est renseignée uniquement si l’utilisateur possède une boîte aux lettres Microsoft Exchange. Si l’utilisateur n’en a pas, il est recommandé de mapper un autre attribut souhaité à l’attribut **e-mails** dans Atlassian Cloud.

## <a name="change-log"></a>Journal des modifications

* 15/06/2020 - Ajout de la prise en charge du batch PATCH pour les groupes.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png