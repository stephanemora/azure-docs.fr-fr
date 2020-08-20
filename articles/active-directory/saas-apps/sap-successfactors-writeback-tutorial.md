---
title: 'Tutoriel : Configurer SAP SuccessFactors Writeback dans Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer l’écriture différée des attributs sur SAP SuccessFactors à partir d’Azure AD.
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: article
ms.workload: identity
ms.date: 08/05/2020
ms.author: chmutali
ms.openlocfilehash: 4b048053a553176f73b5bd199bcb6e28bc74cc6c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88533994"
---
# <a name="tutorial-configure-attribute-write-back-from-azure-ad-to-sap-successfactors"></a>Tutoriel : Configurer l’écriture différée des attributs d’Azure AD sur SAP SuccessFactors
L’objectif de ce tutoriel est d’illustrer les étapes de l’écriture différée des attributs d’Azure AD sur SAP SuccessFactors Employee Central. 

## <a name="overview"></a>Vue d’ensemble

Vous pouvez configurer l’application SAP SuccessFactors Writeback pour écrire des attributs spécifiques vers SAP SuccessFactors Employee Central à partir d’Azure Active Directory. L’application d’approvisionnement SuccessFactors Writeback permet d’attribuer des valeurs aux attributs Employee Central suivants :

* Adresse e-mail professionnelle
* Nom d’utilisateur
* Numéro de téléphone professionnel (y compris l’indicatif du pays, l’indicatif régional, le numéro et le poste)
* Indicateur principal du numéro de téléphone professionnel
* Numéro de téléphone portable (y compris l’indicatif du pays, l’indicatif régional et le numéro)
* Indicateur principal du numéro de téléphone portable 
* Attributs utilisateur custom01-custom15
* Attribut loginMethod

> [!NOTE]
> Cette application n’a aucune dépendance vis-à-vis des applications d’intégration d’approvisionnement des utilisateurs entrants de SuccessFactors. Vous pouvez la configurer indépendamment de l’application d’approvisionnement [SuccessFactors vers AD local](sap-successfactors-inbound-provisioning-tutorial.md) ou [SuccessFactors vers Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>À qui cette solution d’attribution d’utilisateurs convient-elle le mieux ?

Cette solution de provisionnement d’utilisateurs SuccessFactors Writeback est idéale pour :

* Les organisations qui utilisent Office 365 et qui souhaitent écrire en différé vers SuccessFactors Employee Central des attributs faisant autorité gérés par le service informatique (par exemple, l’adresse e-mail, le numéro de téléphone ou le nom d’utilisateur).

## <a name="configuring-successfactors-for-the-integration"></a>Configuration de SuccessFactors pour l’intégration

Tous les connecteurs d’approvisionnement SuccessFactors nécessitent les informations d’identification d’un compte SuccessFactors avec les autorisations appropriées pour appeler les API OData Employee Central. Cette section décrit les étapes permettant de créer le compte de service dans SuccessFactors et d’accorder les autorisations appropriées. 

* [Créer/identifier un compte d’utilisateur d’API dans SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Créer un rôle d’autorisations d’API](#create-an-api-permissions-role)
* [Créer un groupe d’autorisations pour l’utilisateur des API](#create-a-permission-group-for-the-api-user)
* [Accorder le rôle d’autorisation au groupe d’autorisations](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Créer/identifier un compte d’utilisateur d’API dans SuccessFactors
Collaborez avec votre équipe d’administration SuccessFactors ou votre partenaire d’implémentation pour créer ou identifier un compte d’utilisateur dans SuccessFactors qui sera utilisé pour appeler les API OData. Les informations d’identification de nom d’utilisateur et de mot de passe de ce compte seront requises lors de la configuration des applications de provisionnement dans Azure AD. 

### <a name="create-an-api-permissions-role"></a>Créer un rôle d’autorisations d’API

1. Connectez-vous à SAP SuccessFactors avec un compte d’utilisateur qui a accès au centre d’administration.
1. Recherchez *Manage Permission Roles* (Gérer les rôles d’autorisations), puis sélectionnez **Manage Permission Roles** dans les résultats de la recherche.

   ![Gérer les rôles d’autorisations](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)

1. Dans la liste Permission Role (Rôle d’autorisation), cliquez sur **Create New** (Créer).

   > [!div class="mx-imgBorder"]
   > ![Créer un rôle d’autorisation](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)

1. Ajoutez un **nom de rôle** et une **description** pour le nouveau rôle d’autorisation. Le nom et la description doivent indiquer que le rôle est destiné aux autorisations d’utilisation des API.

   > [!div class="mx-imgBorder"]
   > ![Détail du rôle d’autorisation](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)

1. Sous Permission settings (Paramètres d’autorisation), cliquez sur **Permission...** , faites défiler la liste des autorisations, puis cliquez sur **Manage Integration Tools** (Gérer les outils d’intégration). Cochez la case **Allow Admin to Access to OData API through Basic Authentication** (Autoriser l’administrateur à accéder à l’API OData via l’authentification de base).

   > [!div class="mx-imgBorder"]
   > ![Gérer les outils d’intégration](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)

1. Faites défiler la liste dans la même zone et sélectionnez **Employee Central API**. Ajoutez des autorisations comme indiqué ci-dessous pour lire à l’aide de l’API ODATA et modifier à l’aide de l’API ODATA. Sélectionnez l’option de modification si vous envisagez d’utiliser le même compte pour le scénario d’écriture différée vers SuccessFactors. 

   > [!div class="mx-imgBorder"]
   > ![Autorisations de lecture-écriture](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. Cliquez sur **Done** (Terminé). Cliquez sur **Enregistrer les modifications**.

### <a name="create-a-permission-group-for-the-api-user"></a>Créer un groupe d’autorisations pour l’utilisateur des API

1. Dans le centre d’administration SuccessFactors, recherchez *Manage Permission Groups* (Gérer les groupes d’autorisations), puis sélectionnez **Manage Permission Groups** dans les résultats de la recherche.

   > [!div class="mx-imgBorder"]
   > ![Gérer les groupes d’autorisations](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)

1. Dans la fenêtre Manage Permission Groups, cliquez sur **Create New**.

   > [!div class="mx-imgBorder"]
   > ![Ajouter un nouveau groupe](./media/sap-successfactors-inbound-provisioning/create-new-group.png)

1. Ajoutez un nom pour le nouveau groupe. Ce nom doit indiquer que le groupe est destiné aux utilisateurs des API.

   > [!div class="mx-imgBorder"]
   > ![Nom du groupe d’autorisations](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)

1. Ajoutez des membres au groupe. Par exemple, vous pouvez sélectionner **Username** dans le menu déroulant People Pool, puis entrer le nom d’utilisateur du compte d’API qui sera utilisé pour l’intégration. 

   > [!div class="mx-imgBorder"]
   > ![Add group members](./media/sap-successfactors-inbound-provisioning/add-group-members.png) (Ajouter des membres à un groupe)

1. Cliquez sur **Done** pour terminer la création du groupe d’autorisations.

### <a name="grant-permission-role-to-the-permission-group"></a>Accorder le rôle d’autorisation au groupe d’autorisations

1. Dans le centre d’administration SuccessFactors, recherchez *Manage Permission Roles*, puis sélectionnez **Manage Permission Roles** dans les résultats de la recherche.
1. Dans la liste **Permission Role List** (Liste des rôles d’autorisations), sélectionnez le rôle que vous avez créé pour les autorisations d’utilisation des API.
1. Sous **Grant this role to...** (Attribuer ce rôle à), cliquez sur le bouton **Add...** (Ajouter).
1. Sélectionnez **Permission Group...** (Groupe d’autorisations) dans le menu déroulant, puis cliquez sur **Select...** (Sélectionner) pour ouvrir la fenêtre Groups afin de rechercher et sélectionner le groupe créé ci-dessus. 

   > [!div class="mx-imgBorder"]
   > ![Ajouter un groupe d’autorisations](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)

1. Passez en revue l’octroi de rôle d’autorisation au groupe d’autorisations. 
   > [!div class="mx-imgBorder"]
   > ![Détails du rôle et du groupe d’autorisations](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)

1. Cliquez sur **Enregistrer les modifications**.

## <a name="preparing-for-successfactors-writeback"></a>Préparation de SuccessFactors Writeback

L’application d’approvisionnement SuccessFactors Writeback utilise certaines valeurs de *code* pour définir les adresses email et les numéros de téléphone dans Employee Central. Ces valeurs de *code* sont définies comme des valeurs constantes dans la table de mappage d’attributs et sont différentes pour chaque instance SuccessFactors. Cette section utilise [Postman](https://www.postman.com/downloads/) pour extraire les valeurs de code. Vous pouvez utiliser [cURL](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler) ou tout autre outil similaire pour envoyer des requêtes HTTP. 

### <a name="download-and-configure-postman-with-your-successfactors-tenant"></a>Télécharger et configurer Postman avec votre locataire SuccessFactors

1. Téléchargez [Postman](https://www.postman.com/downloads/).
1. Créez une « Nouvelle collection » dans l’application Postman. Nommez-la « SuccessFactors ». 

   > [!div class="mx-imgBorder"]
   > ![New Postman collection](./media/sap-successfactors-inbound-provisioning/new-postman-collection.png)

1. Dans l’onglet « Autorisation », entrez les informations d’identification de l’utilisateur de l’API configuré dans la section précédente. Configurez le type sur « Authentification de base ». 

   > [!div class="mx-imgBorder"]
   > ![Postman authorization](./media/sap-successfactors-inbound-provisioning/postman-authorization.png)

1. Enregistrez la configuration. 

### <a name="retrieve-constant-value-for-emailtype"></a>Récupérer la valeur constante pour emailType

1. Dans Postman, cliquez sur le bouton de sélection (…) associé à la collection SuccessFactors et ajoutez une « Nouvelle requête » appelée « Obtenir les types d’e-mails », comme indiqué ci-dessous. 

   > [!div class="mx-imgBorder"]
   > ![Postman email request ](./media/sap-successfactors-inbound-provisioning/postman-email-request.png)

1. Ouvrez le panneau de requête « Obtenir le type d’e-mail ». 
1. Dans l’URL GET, ajoutez l’URL suivante, en remplaçant `successFactorsAPITenantName` par le locataire de l’API de votre instance SuccessFactors. 
   `https://<successfactorsAPITenantName>/odata/v2/Picklist('ecEmailType')?$expand=picklistOptions&$select=picklistOptions/id,picklistOptions/externalCode&$format=json`

   > [!div class="mx-imgBorder"]
   > ![Postman get email type](./media/sap-successfactors-inbound-provisioning/postman-get-email-type.png)

1. L’onglet « Autorisation » hérite de l’authentification configurée pour la collection. 
1. Cliquez sur « Envoyer » pour invoquer l’appel d’API. 
1. Dans le corps de la réponse, affichez le jeu de résultats JSON et recherchez l’ID correspondant à `externalCode = B`. 

   > [!div class="mx-imgBorder"]
   > ![Postman email type response](./media/sap-successfactors-inbound-provisioning/postman-email-type-response.png)

1. Notez cette valeur comme constante à utiliser avec *emailType* dans la table de mappage d’attributs.

### <a name="retrieve-constant-value-for-phonetype"></a>Récupérer la valeur constante pour phoneType

1. Dans Postman, cliquez sur le bouton de sélection (…) associé à la collection SuccessFactors et ajoutez une « Nouvelle requête » appelée « Obtenir les types de téléphone », comme indiqué ci-dessous. 

   > [!div class="mx-imgBorder"]
   > ![Postman phone request](./media/sap-successfactors-inbound-provisioning/postman-phone-request.png)

1. Ouvrez le panneau de requête « Obtenir le type de téléphone ». 
1. Dans l’URL GET, ajoutez l’URL suivante, en remplaçant `successFactorsAPITenantName` par le locataire de l’API de votre instance SuccessFactors. 
   `https://<successfactorsAPITenantName>/odata/v2/Picklist('ecPhoneType')?$expand=picklistOptions&$select=picklistOptions/id,picklistOptions/externalCode&$format=json`

   > [!div class="mx-imgBorder"]
   > ![Postman get phone type](./media/sap-successfactors-inbound-provisioning/postman-get-phone-type.png)

1. L’onglet « Autorisation » hérite de l’authentification configurée pour la collection. 
1. Cliquez sur « Envoyer » pour invoquer l’appel d’API. 
1. Dans le corps de la réponse, affichez le jeu de résultats JSON et recherchez l’*ID* correspondant à `externalCode = B` et `externalCode = C`. 

   > [!div class="mx-imgBorder"]
   > ![Postman-Phone](./media/sap-successfactors-inbound-provisioning/postman-phone-type-response.png)

1. Notez ces valeurs comme les constantes à utiliser avec *businessPhoneType* et *cellPhoneType* dans la table de mappage d’attributs.

## <a name="configuring-successfactors-writeback-app"></a>Configuration de l’application SuccessFactors Writeback

Cette section décrit les étapes à suivre pour 

* [Ajouter l’application du connecteur de provisionnement et configurer la connectivité à SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors).
* [Configurer les mappages d'attributs](#part-2-configure-attribute-mappings)
* [Activer et lancer l'approvisionnement des utilisateurs](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Première partie : Ajouter l’application du connecteur de provisionnement et configurer la connectivité à SuccessFactors

**Pour configurer SuccessFactors Writeback**

1. Accédez à <https://portal.azure.com>

2. Dans la barre de navigation de gauche, sélectionnez **Azure Active Directory**

3. Cliquez sur **Applications d’entreprise**, puis sur **Toutes les applications**.

4. Sélectionnez **Ajouter une application**, puis sélectionnez la catégorie **Tous**.

5. Recherchez **SuccessFactors Writeback** et ajoutez cette application à partir de la galerie.

6. Une fois l’application ajoutée et l’écran de détails de l’application affiché, sélectionnez **Approvisionnement**

7. Définissez **Mode** **d'approvisionnement** sur **Automatique**

8. Fermez la section **Informations d’identification de l’administrateur**, comme suit :

   * **Nom de l’utilisateur administrateur** : entrez le nom d’utilisateur du compte d’utilisateur de l’API SuccessFactors, accompagné de l’ID d’entreprise. Son format est le suivant : **nom_utilisateur\@ID_société**

   * **Mot de passe d’administrateur** : entrez le mot de passe du compte d’utilisateur de l’API SuccessFactors. 

   * **URL du locataire** : entrez le nom du point de terminaison des services de l’API OData SuccessFactors. Entrez uniquement le nom d’hôte du serveur, sans http ou https. Cette valeur doit ressembler à ceci : `api4.successfactors.com`.

   * **E-mail de notification :** entrez votre adresse e-mail et activez la case à cocher « Envoyer un e-mail en cas de défaillance ».
    > [!NOTE]
    > Le service Azure AD Provisioning envoie la notification par e-mail si le travail de provisionnement passe à l’état [Mise en quarantaine](/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

   * Cliquez sur le bouton **Tester la connexion**. Si le test de connexion aboutit, cliquez sur le bouton **Enregistrer**, en haut de l'écran. En cas d’échec, vérifiez que les informations d’identification et l’URL SuccessFactors sont valides.
    >[!div class="mx-imgBorder"]
    >![Azure portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Une fois les informations d’identification enregistrées, la section **Mappages** affiche le mappage par défaut. Actualisez la page si les mappages d’attributs ne sont pas visibles.  

### <a name="part-2-configure-attribute-mappings"></a>Deuxième partie : Configuration des mappages d’attributs

Dans cette section, vous allez configurer le flux des données utilisateur de SuccessFactors vers Active Directory.

1. Dans l’onglet Approvisionnement, sous **Mappages**, cliquez sur **Approvisionner les utilisateurs d’Azure Active Directory**.

1. Dans le champ **Portée de l’objet source**, vous pouvez sélectionner les ensembles d’utilisateurs Azure AD qui doivent être pris en compte pour l’écriture différée, en définissant des filtres d’attributs. La portée par défaut est « tous les utilisateurs dans Azure AD ». 
   > [!TIP]
   > Lors de la configuration initiale de l'application d'approvisionnement, vous devez tester et vérifier vos mappages d'attributs et expressions pour être sûr d'obtenir le résultat souhaité. Microsoft vous recommande d’utiliser les filtres d’étendue disponibles sous **Portée de l’objet source** pour tester vos mappages avec quelques utilisateurs test d’Azure AD. Après avoir vérifié que les mappages fonctionnent, vous pouvez supprimer le filtre ou l'étendre progressivement pour inclure d'autres utilisateurs.

1. Le champ **Actions d’objet cible** prend uniquement en charge l’opération **Mise à jour**.

1. Dans la table mappage, sous la section **Mappages d’attributs**, vous pouvez mapper les attributs Azure Active Directory suivants à SuccessFactors. Le tableau ci-dessous fournit des conseils sur la façon de mapper les attributs d’écriture différée. 

   | \# | Attribut Azure AD | Attribut SuccessFactors | Notes |
   |--|--|--|--|
   | 1 | employeeId | personIdExternal | Par défaut, cet attribut est l’identificateur correspondant. Au lieu d’employeeId, vous pouvez utiliser tout autre attribut Azure AD capable de stocker la valeur égale à personIdExternal dans SuccessFactors.    |
   | 2 | mail | email | Mappez la source de l’attribut « email ». À des fins de test, vous pouvez mapper userPrincipalName à email. |
   | 3 | 8448 | emailType | Cette valeur constante est la valeur d’ID SuccessFactors associée à la messagerie professionnelle. Mettez à jour cette valeur pour qu’elle corresponde à votre environnement SuccessFactors. Consultez la section [Récupérer la valeur constante pour emailType](#retrieve-constant-value-for-emailtype) afin de connaître les étapes permettant de définir cette valeur. |
   | 4 | true | emailIsPrimary | Utilisez cet attribut pour définir la messagerie professionnelle comme messagerie principale dans SuccessFactors. Si la messagerie professionnelle n’est pas la messagerie principale, définissez cet indicateur sur « false ». |
   | 5 | userPrincipalName | [custom01 – custom15] | En utilisant **Ajouter un nouveau mappage**, vous pouvez éventuellement écrire userPrincipalName ou tout autre attribut Azure AD dans un attribut personnalisé disponible dans l’objet Utilisateur SuccessFactors.  |
   | 6 | on-prem-samAccountName | username | En utilisant **Ajouter un nouveau mappage**, vous pouvez éventuellement mapper l’attribut samAccountName local à l’attribut username de SuccessFactors. |
   | 7 | SSO | loginMethod | Si le locataire SuccessFactors est configuré pour une [SSO partielle](https://apps.support.sap.com/sap/support/knowledge/en/2320766), en utilisant Ajouter un nouveau mappage, vous pouvez éventuellement définir loginMethod sur une valeur constante « SSO » ou « PWD ». |
   | 8 | telephoneNumber | businessPhoneNumber | Utilisez ce mappage pour transmettre l’attribut *telephoneNumber* d’Azure AD au numéro de téléphone professionnel SuccessFactors. |
   | 9 | 10605 | businessPhoneType | Cette valeur constante est la valeur d’ID SuccessFactors associée au téléphone professionnel. Mettez à jour cette valeur pour qu’elle corresponde à votre environnement SuccessFactors. Consultez la section [Récupérer la valeur constante pour phoneType](#retrieve-constant-value-for-phonetype) afin de connaître les étapes permettant de définir cette valeur. |
   | 10 | true | businessPhoneIsPrimary | Utilisez cet attribut pour définir le numéro de téléphone professionnel comme numéro principal. Les valeurs valides sont « true » ou « false ». |
   | 11 | mobile | cellPhoneNumber | Utilisez ce mappage pour transmettre l’attribut *telephoneNumber* d’Azure AD au numéro de téléphone professionnel SuccessFactors. |
   | 12 | 10606 | cellPhoneType | Cette valeur constante est la valeur d’ID SuccessFactors associée au téléphone portable. Mettez à jour cette valeur pour qu’elle corresponde à votre environnement SuccessFactors. Consultez la section [Récupérer la valeur constante pour phoneType](#retrieve-constant-value-for-phonetype) afin de connaître les étapes permettant de définir cette valeur. |
   | 13 | false | cellPhoneIsPrimary | Utilisez cet attribut pour définir le numéro de téléphone portable comme numéro principal. Les valeurs valides sont « true » ou « false ». |
 
1. Validez et vérifiez vos mappages d’attributs. 
 
    >[!div class="mx-imgBorder"]
    >![Writeback attribute mapping](./media/sap-successfactors-inbound-provisioning/writeback-attribute-mapping.png)

1. Cliquez sur **Enregistrer** pour enregistrer les mappages. Ensuite, nous allons mettre à jour les expressions d’API du chemin JSON pour utiliser les codes phoneType dans votre instance SuccessFactors. 
1. Sélectionnez **Afficher les options avancées**. 

    >[!div class="mx-imgBorder"]
    >![Show advanced options](./media/sap-successfactors-inbound-provisioning/show-advanced-options.png)

1. Cliquez sur **Modifier la liste des attributs de SuccessFactors**. 

   > [!NOTE] 
   > Si l’option **Modifier la liste des attributs de SuccessFactors** ne s’affiche pas dans le portail Azure, utilisez l’URL *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* pour accéder à la page. 

1. La colonne **Expression d’API** de cette vue affiche les expressions de chemin JSON utilisées par le connecteur. 
1. Mettez à jour les expressions de chemin JSON pour le téléphone professionnel et le téléphone portable afin d’utiliser la valeur d’ID (*businessPhoneType* et *cellPhoneType*) correspondant à votre environnement. 

    >[!div class="mx-imgBorder"]
    >![Phone JSON Path change](./media/sap-successfactors-inbound-provisioning/phone-json-path-change.png)

1. Cliquez sur **Enregistrer** pour enregistrer les mappages.

## <a name="enable-and-launch-user-provisioning"></a>Activer et lancer l'approvisionnement des utilisateurs

Une fois les configurations d’application de provisionnement SuccessFactors effectuées, vous pouvez activer le service de provisionnement dans le portail Azure.

> [!TIP]
> Par défaut, lorsque vous activez le service d'approvisionnement, il lance les opérations d'approvisionnement pour tous les utilisateurs concernés. En cas d’erreur de mappage ou de problème lié aux données, le travail d’approvisionnement peut échouer et être mis en quarantaine. Pour éviter ce genre de problème, nous vous recommandons de configurer le filtre **Portée de l'objet source** et de tester vos mappages d'attributs sur quelques utilisateurs test avant de lancer la synchronisation complète de tous les utilisateurs. Après avoir vérifié que les mappages fonctionnent et qu'ils vous donnent les résultats souhaités, vous pouvez supprimer le filtre ou l'étendre progressivement pour inclure d'autres utilisateurs.

1. Dans l’onglet **Approvisionnement**, définissez **État d’approvisionnement** sur **Activé**.

2. Cliquez sur **Enregistrer**.

3. Cette opération permet de lancer la synchronisation initiale, dont la durée dépendra du nombre d’utilisateurs du locataire SuccessFactors. Vous pouvez consulter la barre de progression pour suivre la progression du cycle de synchronisation. 

4. À tout moment, consultez l’onglet **Journaux d’audit** dans le portail Azure pour connaître les actions effectuées par le service d’approvisionnement. Les journaux d’audit listent tous les événements de synchronisation individuels effectués par le service d’approvisionnement, tels que les utilisateurs lus à partir d’Employee Central, puis ajoutés ou mis à jour dans Active Directory. 

5. Au terme de la synchronisation initiale, un rapport de synthèse d'audit est créé dans l'onglet **Approvisionnement**, comme illustré ci-dessous.

   > [!div class="mx-imgBorder"]
   > ![Barre de progression de provisionnement](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="supported-scenarios-known-issues-and-limitations"></a>Scénarios pris en charge, problèmes connus et limitations

Reportez-vous à la [section Scénarios d’écriture différée](../app-provisioning/sap-successfactors-integration-reference.md#writeback-scenarios) du guide de référence sur l’intégration de SAP SuccessFactors. 

## <a name="next-steps"></a>Étapes suivantes

* [Approfondissez vos connaissances sur l’intégration d’Azure AD et de SAP SuccessFactors](../app-provisioning/sap-successfactors-integration-reference.md)
* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../app-provisioning/check-status-user-account-provisioning.md)
* [Découvrez comment configurer l’authentification unique entre SuccessFactors et Azure Active Directory](successfactors-tutorial.md)
* [Découvrir comment intégrer d’autres applications SaaS à Azure Active Directory](tutorial-list.md)
* [Découvrez comment exporter et importer vos configurations de provisionnement](../app-provisioning/export-import-provisioning-configuration.md)

