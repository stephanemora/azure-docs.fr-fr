---
title: Connecteur de gestion des services informatiques dans Azure Monitor
description: Cet article fournit des informations vous indiquant comment connecter vos produits/services ITSM à IT Service Management Connector (ITSMC) dans Azure Monitor pour surveiller et gérer les éléments de travail ITSM de manière centralisée.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: a75dd24769a4f1af018981ffcec279afc2a88b5a
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682416"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Connecter des produits/services ITSM à IT Service Management Connector
Cet article fournit des informations vous indiquant comment configurer la connexion entre votre produit/service ITSM au connecteur de gestion des services informatiques (ITSMC) dans Log Analytics pour gérer de manière centralisée vos éléments de travail. Pour plus d’informations sur le connecteur ITSM, consultez [Présentation](./itsmc-overview.md).

Les produits/services ITSM suivants sont pris en charge. Sélectionnez le produit pour afficher des informations détaillées sur la connexion du produit à ITSMC.

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]
> 
> À compter du 1er octobre 2020, les intégrations ITSM de Cherwell et Provance à Azure Alert ne seront plus disponibles pour les nouveaux clients. Les nouvelles connexions ITSM ne seront pas prises en charge. 
> Les connexions ITSM existantes continueront d’être prises en charge.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>Connecter System Center Service Manager au connecteur ITSM dans Azure

Les sections suivantes fournissent des détails sur la connexion de votre produit System Center Service Manager au connecteur ITSM dans Azure.

### <a name="prerequisites"></a>Prérequis

Vérifiez que les prérequis suivants sont remplis :

- Connecteur ITSM installé. Plus d’informations : [Ajout de la solution Connecteur de gestion des services informatiques](./itsmc-overview.md#add-it-service-management-connector).
- L’application web Service Manager (application web) est déployée et configurée. Pour plus d’informations sur l’application web, cliquez [ici](#create-and-deploy-service-manager-web-app-service).
- Connexion hybride créée et configurée. Plus d’informations : [Configurez la connexion hybride](#configure-the-hybrid-connection).
- Versions prises en charge de Service Manager :  2012 R2 ou 2016.
- Rôle utilisateur :  [Opérateur avancé](/previous-versions/system-center/service-manager-2010-sp1/ff461054(v=technet.10)).

> [!NOTE]
> 
> - Le connecteur ITSM peut uniquement se connecter aux instances de ServiceNow basées sur le cloud. Les instances de ServiceNow en local ne sont pas prises en charge actuellement.
> - Afin d’utiliser des [modèles](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#template-definitions) personnalisés dans le cadre des actions, le paramètre « ProjectionType » dans le modèle SCSM doit être mappé à « IncidentManagement!System.WorkItem.Incident.ProjectionType »

### <a name="connection-procedure"></a>Procédure de connexion

Utilisez la procédure suivante pour connecter votre instance System Center Service Manager au connecteur ITSM :

1. Dans le portail Azure, accédez à **Toutes les ressources** et recherchez **ServiceDesk(nom_de_votre_espace_de_travail)**

2.  Sous **SOURCES DE DONNÉES DE L’ESPACE DE TRAVAIL** dans le volet gauche, cliquez sur **Connexions ITSM**.

    ![Nouvelle connexion](media/itsmc-connections/add-new-itsm-connection.png)

3. En haut du panneau droit, cliquez sur **Ajouter**.

4. Indiquez les informations comme décrit dans le tableau suivant, puis cliquez sur **OK** pour créer la connexion.

> [!NOTE]
> 
> Tous ces paramètres sont obligatoires.

| **Champ** | **Description** |
| --- | --- |
| **Nom de connexion**   | Tapez le nom de l’instance System Center Service Manager que vous souhaitez connecter au connecteur ITSM.  Vous utiliserez ce nom ultérieurement lorsque vous configurerez des éléments de travail dans cette instance ou afficherez une analytique des journaux d’activité détaillée. |
| **Type de partenaire**   | Sélectionnez **System Center Service Manager**. |
| **URL du serveur**   | Tapez l’URL de l’application web Service Manager. Pour plus d’informations sur l’application web Service Manager, cliquez [ici](#create-and-deploy-service-manager-web-app-service).
| **ID client**   | Tapez l’ID client que vous avez généré (en utilisant le script automatique) pour authentifier l’application web. Pour plus d’informations sur le script automatisé, cliquez [ici](./itsmc-service-manager-script.md).|
| **Clé secrète client**   | Tapez la clé secrète client, générée pour cet ID.   |
| **Synchroniser les données**   | Sélectionnez les éléments de travail de Service Manager que vous souhaitez synchroniser via le connecteur ITSM.  Ces éléments de travail sont importés dans Log Analytics. **Options :**  Incidents, demandes de modification.|
| **Étendue de la synchronisation des données** | Tapez le nombre de jours passés dont vous souhaitez les données. **Limite maximale** : 120 jours. |
| **Create new configuration item in ITSM solution (Créer un élément de configuration dans la solution ITSM)** | Sélectionnez cette option si vous souhaitez créer les éléments de configuration dans le produit ITSM. Lorsque cette option est sélectionnée, Log Analytics crée les éléments de configuration affectés en tant qu’éléments de configuration (dans le cas d’éléments de configuration non existants) dans le système ITSM pris en charge. **Par défaut** : désactivée. |

![Connexion Service Manager](media/itsmc-connections/service-manager-connection.png)

**En cas de connexion et de synchronisation réussies** :

- Les éléments de travail sélectionnés dans Service Manager sont importés dans Azure **Log Analytics.** Vous pouvez afficher le résumé de ces éléments de travail sur la vignette d’IT Service Management Connector.

- Vous pouvez créer des incidents à partir d’alertes Log Analytics, d’enregistrements de journaux ou d’alertes Azure dans cette instance Service Manager.


En savoir plus : [Créer des éléments de travail ITSM à partir d’alertes Azure](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Créer et déployer l’application web Service Manager

Pour connecter l’instance Service Manager locale au connecteur ITSM dans Azure, Microsoft a créé une application web Service Manager sur GitHub.

Pour configurer l’application Web ITSM pour votre instance Service Manager, procédez comme suit :

- **Déployez l’application web** : déployez l’application web, définissez les propriétés et authentifiez-vous auprès d’Azure AD. Vous pouvez déployer l’application web à l’aide du [script automatisé](./itsmc-service-manager-script.md) que Microsoft vous a fourni.
- **Configurez la connexion hybride** - [Configurez cette connexion](#configure-the-hybrid-connection) manuellement.

#### <a name="deploy-the-web-app"></a>Déployer l’application web
Utilisez le [script](./itsmc-service-manager-script.md) automatisé pour déployer l’application web, définir les propriétés et vous authentifier auprès d’Azure AD.

Exécutez le script en fournissant les informations obligatoires suivantes :

- Détails de l’abonnement Azure
- Nom de groupe ressources
- Emplacement
- Détails du serveur Service Manager (nom du serveur, domaine, nom d’utilisateur et mot de passe)
- Préfixe de nom de site pour votre application Web
- Espace de noms ServiceBus.

Le script crée l’application web en utilisant le nom que vous avez spécifié (avec quelques chaînes supplémentaires pour le rendre unique). Il génère **l’URL de l’application web**, **l’ID client** et la **clé secrète client**.

Enregistrez les valeurs. Vous les utiliserez lorsque vous créerez une connexion avec le connecteur ITSM.

**Vérifier l’installation de l’application web**

1. Accédez au **portail Azure** > **Ressources**.
2. Sélectionnez l’application web, puis cliquez sur **Paramètres** > **Paramètres de l’application**.
3. Vérifiez les informations sur l’instance Service Manager que vous avez fournies au moment du déploiement de l’application via le script.

### <a name="configure-the-hybrid-connection"></a>Configurer la connexion hybride

Utilisez la procédure suivante pour configurer la connexion hybride qui connecte l’instance Service Manager au connecteur ITSM dans Azure.

1. Recherchez l’application web Service Manager, sous **Ressources Azure**.
2. Cliquez sur **Paramètres** > **Mise en réseau**.
3. Sous **Connexions hybrides**, cliquez sur **Configurer vos points de terminaison de connexion hybride**.

    ![Mise en réseau de connexions hybrides](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. Dans le panneau **Connexions hybrides**, cliquez sur **Ajouter une connexion hybride**.

    ![Ajout d’une connexion hybride](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. Dans le panneau **Ajouter des connexions hybrides**, cliquez sur **Créer une connexion hybride**.

    ![Nouvelle connexion hybride](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Tapez les valeurs suivantes :

   - **Nom du point de terminaison** : Spécifiez un nom pour la nouvelle connexion hybride.
   - **Hôte du point de terminaison** : Nom de domaine complet du serveur d’administration de Service Manager.
   - **Port du point de terminaison** : Tapez 5724
   - **Espace de noms Service Bus** : Utilisez un espace de noms Servicebus existant ou créez-en un.
   - **Emplacement** : sélectionnez l’emplacement.
   - **Name** : Spécifiez un nom pour le Servicebus si vous le créez.

     ![Valeurs de connexion hybride](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. Cliquez sur **OK** pour fermer le panneau **Créer une connexion hybride**, et créez la connexion hybride.

    Une fois la connexion hybride créée, elle s’affiche sous le panneau.

7. Une fois la connexion hybride créée, sélectionnez-la, puis cliquez sur **Ajouter la connexion hybride sélectionnée**.

    ![Nouvelle connexion hybride](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>Configurer l’écouteur

Utilisez la procédure suivante pour configurer l’écouteur pour la connexion hybride.

1. Dans le panneau **Connexions hybrides**, cliquez sur **Télécharger le gestionnaire de connexions** et installez-le sur l’ordinateur sur lequel l’instance System Center Service Manager s’exécute.

    Une fois l’installation terminée, l’option **Interface utilisateur du gestionnaire de connexions hybrides** est disponible dans le menu **Démarrer**.

2. Cliquez sur **Interface utilisateur du gestionnaire de connexions hybrides**. Vous êtes invité à entrer vos informations d’identification Azure.

3. Connectez-vous avec vos informations d’identification Azure et sélectionnez votre abonnement dans lequel la connexion hybride a été créée.

4. Cliquez sur **Enregistrer**.

Votre connexion hybride est connectée avec succès.

![Connexion hybride réussie](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> Une fois la connexion hybride créée, vérifiez et testez la connexion en visitant l’application web Service Manager déployée. Assurez-vous que la connexion est établie avant d’essayer de vous connecter au connecteur ITSM dans Azure.

L’image d’exemple suivante présente les détails d’une connexion réussie :

![Test de connexion hybride](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>Connecter ServiceNow au connecteur ITSM dans Azure

Les sections suivantes fournissent des détails sur la connexion de votre produit ServiceNow au connecteur ITSM dans Azure.

### <a name="prerequisites"></a>Prérequis
Vérifiez que les prérequis suivants sont remplis :
- Connecteur ITSM installé. Plus d’informations : [Ajout de la solution Connecteur de gestion des services informatiques](./itsmc-overview.md#add-it-service-management-connector).
- Versions prises en charge par ServiceNow : Orlando, New York, Madrid, Londres, Kingston, Jakarta, Istanbul, Helsinki, Genève.
- Actuellement, les alertes envoyées depuis Azure Monitor peuvent créer dans ServiceNow l’un des éléments suivants : Événements, incidents ou alertes.
> [!NOTE]
> Le connecteur ITSM prend en charge uniquement l’offre SaaS officielle de Service Now. Les déploiements privés de Service Now ne sont pas pris en charge. 

**Les administrateurs ServiceNow doivent procéder comme suit dans leur instance ServiceNow** :
- Générer l’ID client et la clé secrète client pour le produit ServiceNow. Pour plus d’informations sur la génération d’ID client et de secret, consultez les informations suivantes :

    - [Configurer OAuth pour Orlando](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurer OAuth pour New York](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurer OAuth pour Madrid](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurer OAuth pour London](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurer OAuth pour Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurer OAuth pour Jakarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurer OAuth pour Istanbul](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurer OAuth pour Helsinki](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Configurer OAuth pour Geneva](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)
> [!NOTE]
> Dans le cadre de la définition de la « configuration OAuth », nous vous recommandons de procéder comme suit :
>
> 1) **Mettez à jour la durée de vie du jeton d’actualisation à 90 jours (7 776 000 secondes) :** dans le cadre de la [configuration OAuth](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_SettingUpOAuth.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696739125&sdata=Q7mF6Ej8MCupKaEJpabTM56EDZ1T8vFVyihhoM594aA%3D&reserved=0) à la phase 2 : [Créer un point de terminaison pour que les clients accèdent à l’instance](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_CreateEndpointforExternalClients.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696749123&sdata=hoAJHJAFgUeszYCX1Q%2FXr4N%2FAKiFcm5WV7mwR2UqeWA%3D&reserved=0), après la définition du point de terminaison, dans le panneau ServiceNow, recherchez « Système OAuth », puis sélectionnez « Registre d’application ». Choisissez le nom du OAuth qui a été défini et mettez à jour le champ de durée de vie du jeton d’actualisation sur 7 776 000 (90 jours en secondes).
> À la fin, cliquez sur Mettre à jour.
> 2) **Nous vous recommandons d’établir une procédure interne pour garantir le maintien de la connexion :** en fonction de la durée de vie du jeton d’actualisation pour actualiser le jeton. Assurez-vous d’effectuer les opérations suivantes avant l’expiration prévue du jeton d’actualisation (nous recommandons de le faire quelques jours avant l’expiration de la durée de vie du jeton d’actualisation) :
>
> 1. [Effectuez une synchronisation manuelle pour la configuration du connecteur ITSM](./itsmc-resync-servicenow.md)
> 2. Révoquez l’ancien jeton d’actualisation, car il n’est pas recommandé, pour des raisons de sécurité, de conserver les anciennes clés. Dans le panneau ServiceNow, recherchez « Système OAuth », puis sélectionnez « Gérer les jetons ». Choisissez l’ancien jeton dans la liste en fonction du nom OAuth et de la date d’expiration.
> ![SNOW system OAuth definition](media/itsmc-connections/snow-system-oauth.png)
> 3. Cliquez sur Révoquer l’accès, puis sur Révoquer.

- Installez l’application utilisateur pour l’intégration de Microsoft Log Analytics (application ServiceNow). [Plus d’informations](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 )
> [!NOTE]
> ITSMC prend en charge uniquement l’application utilisateur officielle pour l’intégration de Microsoft Log Analytics qui est téléchargée à partir du magasin ServiceNow. ITSMC ne prend pas en charge l’ingestion de code côté ServiceNow ou l’application qui ne fait pas partie de la solution ServiceNow officielle. 
- Créer un rôle utilisateur de l’intégration pour l’application utilisateur installée. Pour plus d’informations sur la création du rôle d’utilisateur de l’intégration, cliquez [ici](#create-integration-user-role-in-servicenow-app).

### <a name="connection-procedure"></a>**Procédure de connexion**
Exécutez la procédure suivante pour créer une connexion ServiceNow :


1. Dans le portail Azure, accédez à **Toutes les ressources** et recherchez **ServiceDesk(nom_de_votre_espace_de_travail)**

2.  Sous **SOURCES DE DONNÉES DE L’ESPACE DE TRAVAIL** dans le volet gauche, cliquez sur **Connexions ITSM**.
    ![Nouvelle connexion](media/itsmc-connections/add-new-itsm-connection.png)

3. En haut du panneau droit, cliquez sur **Ajouter**.

4. Indiquez les informations comme décrit dans le tableau suivant, puis cliquez sur **OK** pour créer la connexion.


> [!NOTE]
> Tous ces paramètres sont obligatoires.

| **Champ** | **Description** |
| --- | --- |
| **Nom de connexion**   | Tapez le nom de l’instance ServiceNow que vous souhaitez connecter au connecteur ITSM.  Vous utiliserez ce nom ultérieurement dans Log Analytics lors de la configuration des éléments de travail dans cette instance ITSM ou de l’affichage d’une analytique des journaux d’activité détaillée. |
| **Type de partenaire**   | Sélectionnez **ServiceNow**. |
| **Nom d’utilisateur**   | Tapez le nom d’utilisateur de l’intégration que vous avez créé dans l’application ServiceNow pour prendre en charge la connexion au connecteur ITSM. Plus d’informations : [Create ServiceNow app user role (Créer un rôle utilisateur pour l’application ServiceNow)](#create-integration-user-role-in-servicenow-app).|
| **Mot de passe**   | Tapez le mot de passe associé à ce nom d’utilisateur. **Remarque** : Le nom d’utilisateur et le mot de passe sont utilisés uniquement pour générer des jetons d’authentification. Ils ne sont pas stockés dans le service ITSMC.  |
| **URL du serveur**   | Tapez l’URL de l’instance ServiceNow que vous souhaitez connecter au connecteur ITSM. L’URL doit pointer vers une version SaaS prise en charge avec le suffixe « .servicenow.com ».|
| **ID client**   | Tapez l’ID client généré précédemment que vous souhaitez utiliser pour l’authentification OAuth2.  Plus d’informations sur la génération de l’ID client et de la clé secrète :   [Paramètres OAuth](https://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Clé secrète client**   | Tapez la clé secrète client, générée pour cet ID.   |
| **Étendue de la synchronisation des données**   | Sélectionnez les éléments de travail de ServiceNow que vous souhaitez synchroniser avec Azure Log Analytics via le connecteur ITSMC.  Les valeurs sélectionnées sont importées dans Log Analytics.   **Options :**  Incidents et demandes de modification.|
| **Synchroniser les données** | Tapez le nombre de jours passés dont vous souhaitez les données. **Limite maximale** : 120 jours. |
| **Create new configuration item in ITSM solution (Créer un élément de configuration dans la solution ITSM)** | Sélectionnez cette option si vous souhaitez créer les éléments de configuration dans le produit ITSM. Lorsque cette option est sélectionnée, ITSMC crée les éléments de configuration affectés en tant qu’éléments de configuration (dans le cas d’éléments de configuration non existants) dans le système ITSM pris en charge. **Par défaut** : désactivée. |

![Connexion ServiceNow](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**En cas de connexion et de synchronisation réussies** :

- Les éléments de travail sélectionnés dans une instance ServiceNow sont importés dans Azure **Log Analytics.** Vous pouvez afficher le résumé de ces éléments de travail sur la vignette d’IT Service Management Connector.

- Vous pouvez créer des incidents à partir d’alertes Log Analytics, d’enregistrements de journaux ou d’alertes Azure dans cette instance ServiceNow.

En savoir plus : [Créer des éléments de travail ITSM à partir d’alertes Azure](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts).


> [!NOTE]
> Dans ServiceNow, il existe une limite horaire du débit des requêtes. Pour configurer la limite, utilisez cette valeur en définissant « Limitation du débit entrant de l’API REST » dans l’instance ServiceNow.

### <a name="create-integration-user-role-in-servicenow-app"></a>Créer un rôle utilisateur de l’intégration dans l’application ServiceNow

Procédez comme suit :

1. Visitez le [magasin ServiceNow](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) et installez **l’application utilisateur pour l’intégration de Microsoft OMS et de ServiceNow** dans votre instance ServiceNow.
   
   >[!NOTE]
   >Dans le cadre de la transition en cours depuis Microsoft Operations Management Suite (OMS) vers Azure Monitor, OMS est maintenant appelé Log Analytics.     
2. Après l’installation, consultez la barre de navigation gauche de l’instance ServiceNow, puis recherchez et sélectionnez l’intégrateur Microsoft OMS.  
3. Cliquez sur **Liste de vérifications d’installation**.

   L’état **Incomplet** est affiché si le rôle utilisateur doit encore être créé.

4. Dans les zones de texte situées en regard de **Create integration user** (Créer un utilisateur de l’intégration), entrez le nom de l’utilisateur qui peut se connecter au connecteur ITSM dans Azure.
5. Entrez le mot de passe de cet utilisateur, puis cliquez sur **OK**.  

> [!NOTE]
> 
> Vous utilisez ces informations d’identification pour établir la connexion ServiceNow dans Azure.

L’utilisateur nouvellement créé est affiché avec les rôles par défaut affectés.

**Rôles par défaut** :
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   itil
-   template_editor
-   view_changer

Une fois l’utilisateur créé, l’état de l’option **Liste de vérifications d’installation** est défini sur Terminé, et affiche les détails du rôle utilisateur créé pour l’application.

> [!NOTE]
> 
> Le connecteur ITSM peuvent envoyer des incidents à ServiceNow sans d’autres modules installés sur votre instance ServiceNow. Si vous utilisez le module EventManagement dans votre instance ServiceNow et si vous souhaitez créer des Événements ou des Alertes dans ServiceNow à l’aide du connecteur, ajoutez les rôles suivants à l’utilisateur d’intégration :
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>Connecter Provance au connecteur ITSM dans Azure

Les sections suivantes fournissent des détails sur la connexion de votre produit Provance au connecteur ITSM dans Azure.

> [!NOTE]
> 
> À compter du 1er octobre 2020, l’intégration ITSM de Provance à Azure Alert ne sera plus disponible pour les nouveaux clients. Les nouvelles connexions ITSM ne seront pas prises en charge. 
> Les connexions ITSM existantes continueront d’être prises en charge.

### <a name="prerequisites"></a>Prérequis

Vérifiez que les prérequis suivants sont remplis :


- Connecteur ITSM installé. Plus d’informations : [Ajout de la solution Connecteur de gestion des services informatiques](./itsmc-overview.md#add-it-service-management-connector).
- L’application Provance doit être inscrite auprès d’Azure AD, et l’ID client est mis à disposition. Pour plus d’informations, consultez [Comment configurer votre application pour utiliser la connexion Azure Active Directory](../../app-service/configure-authentication-provider-aad.md).

- Rôle utilisateur :  Administrateur.

### <a name="connection-procedure"></a>Procédure de connexion

Exécutez la procédure suivante pour créer une connexion Provance :

1. Dans le portail Azure, accédez à **Toutes les ressources** et recherchez **ServiceDesk(nom_de_votre_espace_de_travail)**

2.  Sous **SOURCES DE DONNÉES DE L’ESPACE DE TRAVAIL** dans le volet gauche, cliquez sur **Connexions ITSM**.
    ![Nouvelle connexion](media/itsmc-connections/add-new-itsm-connection.png)

3. En haut du panneau droit, cliquez sur **Ajouter**.

4. Indiquez les informations comme décrit dans le tableau suivant, puis cliquez sur **OK** pour créer la connexion.

> [!NOTE]
> 
> Tous ces paramètres sont obligatoires.

| **Champ** | **Description** |
| --- | --- |
| **Nom de connexion**   | Tapez le nom de l’instance Provance que vous souhaitez connecter au connecteur ITSM.  Vous utiliserez ce nom ultérieurement dans lorsque vous configurerez des éléments de travail dans cette instance ITSM ou afficherez une analytique des journaux d’activité détaillée. |
| **Type de partenaire**   | Sélectionnez **Provance**. |
| **Nom d’utilisateur**   | Tapez le nom d’utilisateur pouvant se connecter au connecteur ITSM.    |
| **Mot de passe**   | Tapez le mot de passe associé à ce nom d’utilisateur. **Remarque :** Le nom d’utilisateur et le mot de passe sont utilisés uniquement pour générer des jetons d’authentification. Ils ne sont pas stockés dans le service ITSMC._|
| **URL du serveur**   | Tapez l’URL de l’instance Provance que vous souhaitez connecter au connecteur ITSM. |
| **ID client**   | Tapez l’ID client que vous avez généré dans votre instance Provance pour authentifier cette connexion.  Pour plus d’informations sur l’ID client, consultez [Comment configurer votre application pour utiliser la connexion Azure Active Directory](../../app-service/configure-authentication-provider-aad.md). |
| **Étendue de la synchronisation des données**   | Sélectionnez les éléments de travail de Provance que vous souhaitez synchroniser avec Azure Log Analytics via le connecteur ITSM.  Ces éléments de travail sont importés dans Log Analytics.   **Options :**   Incidents, demandes de modification.|
| **Synchroniser les données** | Tapez le nombre de jours passés dont vous souhaitez les données. **Limite maximale** : 120 jours. |
| **Create new configuration item in ITSM solution (Créer un élément de configuration dans la solution ITSM)** | Sélectionnez cette option si vous souhaitez créer les éléments de configuration dans le produit ITSM. Lorsque cette option est sélectionnée, ITSMC crée les éléments de configuration affectés en tant qu’éléments de configuration (dans le cas d’éléments de configuration non existants) dans le système ITSM pris en charge. **Par défaut** : désactivée.|

![Capture d’écran mettant en évidence les listes Nom de connexion et Type de partenaire.](media/itsmc-connections/itsm-connections-provance-latest.png)

**En cas de connexion et de synchronisation réussies** :

- Les éléments de travail sélectionnés dans une instance Provance sont importés dans Azure **Log Analytics.** Vous pouvez afficher le résumé de ces éléments de travail sur la vignette d’IT Service Management Connector.

- Vous pouvez créer des incidents à partir d’alertes Log Analytics, d’enregistrements de journaux ou d’alertes Azure dans cette instance Provance.

En savoir plus : [Créer des éléments de travail ITSM à partir d’alertes Azure](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Connecter Cherwell au connecteur ITSM dans Azure

Les sections suivantes fournissent des détails sur la connexion de votre produit Cherwell au connecteur ITSM dans Azure.

> [!NOTE]
> 
> À compter du 1er octobre 2020, l’intégration ITSM de Cherwell à Azure Alert ne sera plus disponible pour les nouveaux clients. Les nouvelles connexions ITSM ne seront pas prises en charge. 
> Les connexions ITSM existantes continueront d’être prises en charge.

### <a name="prerequisites"></a>Prérequis

Vérifiez que les prérequis suivants sont remplis :

- Connecteur ITSM installé. Plus d’informations : [Ajout de la solution Connecteur de gestion des services informatiques](./itsmc-overview.md#add-it-service-management-connector).
- ID client généré. Plus d’informations : [Générer un ID client pour Cherwell](#generate-client-id-for-cherwell).
- Rôle utilisateur :  Administrateur.

### <a name="connection-procedure"></a>Procédure de connexion

Exécutez la procédure suivante pour créer une connexion Cherwell :

1. Dans le portail Azure, accédez à **Toutes les ressources** et recherchez **ServiceDesk(nom_de_votre_espace_de_travail)**

2.  Sous **SOURCES DE DONNÉES DE L’ESPACE DE TRAVAIL** dans le volet gauche, cliquez sur **Connexions ITSM**.
    ![Nouvelle connexion](media/itsmc-connections/add-new-itsm-connection.png)

3. En haut du panneau droit, cliquez sur **Ajouter**.

4. Indiquez les informations comme décrit dans le tableau suivant, puis cliquez sur **OK** pour créer la connexion.

> [!NOTE]
> 
> Tous ces paramètres sont obligatoires.

| **Champ** | **Description** |
| --- | --- |
| **Nom de connexion**   | Tapez le nom de l’instance Cherwell que vous souhaitez connecter au connecteur ITSM.  Vous utiliserez ce nom ultérieurement dans lorsque vous configurerez des éléments de travail dans cette instance ITSM ou afficherez une analytique des journaux d’activité détaillée. |
| **Type de partenaire**   | Sélectionnez **Cherwell**. |
| **Nom d’utilisateur**   | Tapez le nom d’utilisateur Cherwell pouvant se connecter au connecteur ITSM. |
| **Mot de passe**   | Tapez le mot de passe associé à ce nom d’utilisateur. **Remarque :** Le nom d’utilisateur et le mot de passe sont utilisés uniquement pour générer des jetons d’authentification. Ils ne sont pas stockés dans le service ITSMC.|
| **URL du serveur**   | Tapez l’URL de l’instance Cherwell que vous souhaitez connecter au connecteur ITSM. |
| **ID client**   | Tapez l’ID client que vous avez généré dans votre instance Cherwell pour authentifier cette connexion.   |
| **Étendue de la synchronisation des données**   | Sélectionnez les éléments de travail de Cherwell que vous souhaitez synchroniser via le connecteur ITSM.  Ces éléments de travail sont importés dans Log Analytics.   **Options :**  Incidents, demandes de modification. |
| **Synchroniser les données** | Tapez le nombre de jours passés dont vous souhaitez les données. **Limite maximale** : 120 jours. |
| **Create new configuration item in ITSM solution (Créer un élément de configuration dans la solution ITSM)** | Sélectionnez cette option si vous souhaitez créer les éléments de configuration dans le produit ITSM. Lorsque cette option est sélectionnée, ITSMC crée les éléments de configuration affectés en tant qu’éléments de configuration (dans le cas d’éléments de configuration non existants) dans le système ITSM pris en charge. **Par défaut** : désactivée. |


![Connexion Provance](media/itsmc-connections/itsm-connections-cherwell-latest.png)

**En cas de connexion et de synchronisation réussies** :

- Les éléments de travail sélectionnés dans une instance Cherwell sont importés dans Azure **Log Analytics.** Vous pouvez afficher le résumé de ces éléments de travail sur la vignette d’IT Service Management Connector.

- Vous pouvez créer des incidents à partir d’alertes Log Analytics, d’enregistrements de journaux ou d’alertes Azure dans cette instance Cherwell.

En savoir plus : [Créer des éléments de travail ITSM à partir d’alertes Azure](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Générer un ID client pour Cherwell

Pour générer l’ID client/la clé de Cherwell, procédez comme suit :

1. Connectez-vous à votre instance Cherwell en tant qu’administrateur.
2. Cliquez sur **Sécurité** > **Edit REST API client settings (Modifier les paramètres du client de l’API REST)** .
3. Sélectionnez **Créer un client** > **Clé secrète client**.

    ![Id utilisateur de Cherwell](media/itsmc-connections/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>Étapes suivantes
 - [Créer des éléments de travail ITSM à partir des alertes Azure](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts)

