---
title: 'Tutoriel : Configurer TravelPerk pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment approvisionner et déprovisionner automatiquement des comptes d’utilisateur d’Azure AD vers TravelPerk.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 3e40f87d-8624-4b14-b098-80ff916103c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2021
ms.author: Zhchia
ms.openlocfilehash: 7e3e119c81f6417110d34b7b9b97af9fa1738d35
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054122"
---
# <a name="tutorial-configure-travelperk-for-automatic-user-provisioning"></a>Tutoriel : Configurer TravelPerk pour le provisionnement automatique d’utilisateurs

Ce tutoriel décrit les étapes à suivre dans TravelPerk et Azure Active Directory (Azure AD) pour configurer l’approvisionnement automatique d’utilisateurs. Une fois configuré, Azure AD approvisionne et déprovisionne automatiquement des utilisateurs et des groupes sur [TravelPerk](https://www.travelperk.com/) à l’aide du service d’approvisionnement d’Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="capabilities-supported"></a>Fonctionnalités prises en charge

> [!div class="checklist"]
>
> - Créer des utilisateurs dans TravelPerk
> - Supprimer des utilisateurs dans TravelPerk quand ils n’ont plus besoin d’accéder
> - Maintenir la synchronisation des attributs utilisateur entre Azure AD et TravelPerk
> - [Authentification unique](https://docs.microsoft.com/azure/active-directory/saas-apps/travelperk-tutorial) auprès de TravelPerk (recommandé)

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce tutoriel part du principe que vous disposez des prérequis suivants :

- [Un locataire Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
- Un compte d’utilisateur dans Azure AD avec l’[autorisation](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) de configurer l’approvisionnement (par exemple, administrateur d’application, administrateur d’application Cloud, propriétaire d’application ou administrateur général).
- Un compte d’administrateur [TravelPerk](https://app.travelperk.com/signup) actif.
- Un [plan](https://www.travelperk.com/pricing/)Premium/Pro.


## <a name="step-1-plan-your-provisioning-deployment"></a>Étape 1. Planifier votre déploiement de l’approvisionnement

1. En savoir plus sur le [fonctionnement du service d’approvisionnement](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Déterminez qui sera dans l’[étendue pour l’approvisionnement](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Déterminez les données à [mapper entre Azure AD et TravelPerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-travelperk-to-support-provisioning-with-azure-ad"></a>Étape 2. Configurer TravelPerk pour prendre en charge l’approvisionnement avec Azure AD

1. Connectez-vous à l’application [TravelPerk](https://app.travelperk.com/company/integrations/scim) avec votre compte d’administrateur.

2. Accédez à **Paramètres de la société** > **Intégrations** > **SCIM**.

3. Cliquez sur **Activer l’API SCIM**.

   ![Activer](./media/travelperk-provisioning-tutorial/configuration.png)

4. Vous pouvez également activer les approbations via SCIM. Les approbations vous aident à définir une gouvernance supplémentaire en veillant à ce que les voyages soient approuvés en premier par les approbateurs spécifiés. Vous pouvez en apprendre davantage à ce propos [ici](https://support.travelperk.com/hc/en-us/articles/360044168971-How-do-approval-processes-work-).

5. Vous pouvez spécifier si vous souhaitez que le responsable de chaque personne devienne automatiquement l’utilisateur responsable de l’approbation des voyages. Par conséquent, un approbateur sera affecté dans le processus d’approbation automatique correspondant. TravelPerk mappera la valeur **gestionnaire** d’Azure à l’approbateur souhaité de l’utilisateur. L’utilisateur doit exister sur la plateforme avant de devenir l’approbateur d’utilisateur approvisionné.
Des approbateurs ne seront pas créés s’ils ne sont pas configurés correctement sur TravelPerk.

6. La création d’un processus d’approbation automatique est disponible dans les **Paramètres SCIM** après l’activation de SCIM à partir de la page Intégrations. Pour l’activer, sélectionnez **Via un fournisseur d’identité** et basculez le commutateur pour **permettre la création d’un processus d’approbation automatique**.

7. Une fois le processus d’approbation nécessaire configuré, cliquez sur **Enregistrer les modifications**.

   ![Automatisation](./media/travelperk-provisioning-tutorial/approval.png)

## <a name="step-3-add-travelperk-from-the-azure-ad-application-gallery"></a>Étape 3. Ajouter TravelPerk à partir de la galerie d’applications Azure AD

Ajoutez TravelPerk à partir de la galerie d’applications Azure AD pour commencer à gérer l’approvisionnement pour TravelPerk. Si vous avez déjà configuré TravelPerk pour l’authentification unique, vous pouvez utiliser la même application. Toutefois, il est recommandé de créer une application distincte lors du test initial de l’intégration. En savoir plus sur l’ajout d’une application à partir de la galerie [ici](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Étape 4. Définir qui sera dans l’étendue pour l’approvisionnement

Le service d’approvisionnement Azure AD vous permet de définir l’étendue des utilisateurs approvisionnés en fonction de l’affectation à l’application et/ou en fonction des attributs de l’utilisateur/groupe. Si vous choisissez de définir l’étendue de l’approvisionnement pour votre application en fonction de l’attribution, vous pouvez utiliser les étapes de [suivantes](../manage-apps/assign-user-or-group-access-portal.md) pour affecter des utilisateurs et des groupes à l’application. Si vous choisissez de définir l’étendue de l’approvisionnement en fonction uniquement des attributs de l’utilisateur ou du groupe, vous pouvez utiliser un filtre d’étendue comme décrit [ici](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

- Quand vous attribuez des utilisateurs à TravelPerk, vous devez sélectionner un autre rôle que le rôle **Accès par défaut**. Les utilisateurs disposant du rôle Accès par défaut sont exclus de l’approvisionnement et sont marqués comme non autorisés dans les journaux de configuration. Si le seul rôle disponible dans l’application est le rôle d’accès par défaut, vous pouvez [mettre à jour le manifeste de l’application](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) pour ajouter des rôles supplémentaires.

- Commencez progressivement. Testez avec un petit ensemble d’utilisateurs et de groupes avant d’effectuer un déploiement général. Lorsque l’étendue de l’approvisionnement est définie sur les utilisateurs et les groupes attribués, vous pouvez contrôler cela en affectant un ou deux utilisateurs ou groupes à l’application. Lorsque l’étendue est définie sur tous les utilisateurs et groupes, vous pouvez spécifier un [filtre d’étendue basé sur l’attribut](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

## <a name="step-5-configure-automatic-user-provisioning-to-travelperk"></a>Étape 5. Configurer l’approvisionnement automatique d’utilisateurs pour TravelPerk

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans TestApp en fonction des assignations d’utilisateurs et/ou de groupes dans Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-travelperk-in-azure-ad"></a>Pour configurer l’approvisionnement automatique d’utilisateurs pour TravelPerk dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.

   ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **TravelPerk**.

   ![Lien TravelPerk dans la liste Applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

   ![Onglet Approvisionnement](common/provisioning.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

   ![Onglet Provisionnement automatique](common/provisioning-automatic.png)

5. Sous la section **Informations d’identification de l’administrateur**, cliquez sur **Autoriser**. Vous serez redirigé vers la page de connexion de **TravelPerk**. Entrez vos **Nom d’utilisateur** et **Mot de passe**, puis cliquez sur le bouton **Se connecter**. Cliquez sur **Autoriser l’application** dans la page Autorisation. Cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à TravelPerk. Si la connexion échoue, vérifiez que votre compte SecureLogin dispose d’autorisations d’administrateur, puis réessayez.

   ![Admin Credentials (Informations d’identification de l’administrateur)](./media/travelperk-provisioning-tutorial/authorize.png)

   ![Bienvenue](./media/travelperk-provisioning-tutorial/login.png)

   ![Accès](./media/travelperk-provisioning-tutorial/authorization.png)

6. Dans le champ **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur de provisionnement et sélectionnez la case à cocher **Envoyer une notification par e-mail en cas de défaillance**.

   ![E-mail de notification](common/provisioning-notification-email.png)

7. Sélectionnez **Enregistrer**.

8. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory sur TravelPerk**.

9. Dans la section **Mappages des attributs**, vérifiez les attributs d’utilisateurs qui sont synchronisés d’Azure AD sur TravelPerk. Les attributs sélectionnés en tant que propriétés de **Correspondantes** sont utilisés dans le but de faire correspondre les comptes d’utilisateur dans TravelPerk pour les opérations de mise à jour. Si vous choisissez de modifier l’[attribut cible correspondant](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), vérifiez que l’API TravelPerk prend en charge le filtrage des utilisateurs en fonction de cet attribut. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

   | Attribut                                                                         | Type      | Pris en charge pour le filtrage |
   | --------------------------------------------------------------------------------- | --------- | ----------------------- |
   | userName                                                                          | String    | &check;                 |
   | externalId                                                                        | String    |
   | active                                                                            | Boolean   |
   | name.honorificPrefix                                                              | String    |
   | name.familyName                                                                   | String    |
   | name.givenName                                                                    | String    |
   | name.middleName                                                                   | String    |
   | preferredLanguage                                                                 | String    |
   | locale                                                                            | String    |
   | phoneNumbers[type eq "work"].value                                                | String    |
   | externalId                                                                        | String    |
   | title                                                                             | String    |
   | urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter             | String    |
   | urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager                | Informations de référence |
   | urn:ietf:params:scim:schemas:extension:travelperk:2.0:User:gender                 | String    |
   | urn:ietf:params:scim:schemas:extension:travelperk:2.0:User:dateOfBirth            | String    |
   | urn:ietf:params:scim:schemas:extension:travelperk:2.0:User:invoiceProfiles        | Array     |
   | urn:ietf:params:scim:schemas:extension:travelperk:2.0:User:emergencyContact.name  | String    |
   | urn:ietf:params:scim:schemas:extension:travelperk:2.0:User:emergencyContact.phone | String    |
   | urn:ietf:params:scim:schemas:extension:travelperk:2.0:User:travelPolicy           | String    |

10. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Pour activer le service d’approvisionnement Azure AD pour TravelPerk, dans la section **Paramètres**, attribuez au paramètre **État de l’approvisionnement** la valeur **Activé**.

    ![État d’approvisionnement activé](common/provisioning-toggle-on.png)

12. Définissez les utilisateurs et/ou les groupes que vous souhaitez approvisionner dans TravelPerk en choisissant les valeurs souhaitées dans **Étendue**, dans la section **Paramètres**.

    ![Étendue de l’approvisionnement](common/provisioning-scope.png)

13. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Enregistrement de la configuration de l’approvisionnement](common/provisioning-configuration-save.png)

Cette opération démarre le cycle de synchronisation initiale de tous les utilisateurs et groupes définis dans **Étendue** dans la section **Paramètres**. Le cycle de synchronisation initiale prend plus de temps que les cycles de synchronisation suivants, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution.

## <a name="step-6-monitor-your-deployment"></a>Étape 6. Surveiller votre déploiement

Une fois que vous avez configuré l’approvisionnement, utilisez les ressources suivantes pour surveiller votre déploiement :

1. Utilisez les [journaux d’approvisionnement](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) pour déterminer quels utilisateurs ont été configurés avec succès ou échoué.
2. Consultez la [barre de progression](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) pour afficher l’état du cycle d’approvisionnement et quand il se termine
3. Si la configuration de l’approvisionnement semble se trouver dans un état non sain, l’application passe en quarantaine. Pour en savoir plus sur les états de quarantaine, cliquez [ici](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

- [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../manage-apps/check-status-user-account-provisioning.md)
