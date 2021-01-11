---
title: Connecter ServiceNow au connecteur ITSM
description: Cet article fournit des informations vous indiquant comment connecter ServiceNow au connecteur de gestion des services informatiques (ITSMC) dans Azure Monitor pour surveiller et gérer les éléments de travail ITSM de manière centralisée.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 662c36e4f0082c376a6e250e9a0885f0cd225964
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97729530"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>Connecter ServiceNow au connecteur ITSM

Cet article fournit des informations vous indiquant comment configurer la connexion entre votre instance ServiceNow au connecteur de gestion des services informatiques (ITSMC) dans Log Analytics pour gérer de manière centralisée vos éléments de travail.

Les sections suivantes fournissent des détails sur la connexion de votre produit ServiceNow au connecteur ITSM dans Azure.

## <a name="prerequisites"></a>Prérequis
Vérifiez que les prérequis suivants sont remplis :
- Connecteur ITSM installé. Plus d’informations : [Ajout de la solution Connecteur de gestion des services informatiques](./itsmc-definition.md#add-it-service-management-connector).
- Versions prises en charge par ServiceNow : Orlando, New York, Madrid, Londres, Kingston, Jakarta, Istanbul, Helsinki, Genève.
- Actuellement, les alertes envoyées depuis Azure Monitor peuvent créer l'un des éléments suivants dans ServiceNow : Événements, incidents ou alertes.
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
>     1. [Effectuez une synchronisation manuelle pour la configuration du connecteur ITSM](./itsmc-resync-servicenow.md)
>     2. Révoquez l’ancien jeton d’actualisation, car il n’est pas recommandé, pour des raisons de sécurité, de conserver les anciennes clés. Dans le panneau ServiceNow, recherchez « Système OAuth », puis sélectionnez « Gérer les jetons ». Choisissez l’ancien jeton dans la liste en fonction du nom OAuth et de la date d’expiration.
> ![SNOW system OAuth definition](media/itsmc-connections/snow-system-oauth.png)
>     3. Cliquez sur Révoquer l’accès, puis sur Révoquer.

- Installez l’application utilisateur pour l’intégration de Microsoft Log Analytics (application ServiceNow). [Plus d’informations](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 )
> [!NOTE]
> ITSMC prend en charge uniquement l’application utilisateur officielle pour l’intégration de Microsoft Log Analytics qui est téléchargée à partir du magasin ServiceNow. ITSMC ne prend pas en charge l’ingestion de code côté ServiceNow ou l’application qui ne fait pas partie de la solution ServiceNow officielle. 
- Créer un rôle utilisateur de l’intégration pour l’application utilisateur installée. Pour plus d’informations sur la création du rôle d’utilisateur de l’intégration, cliquez [ici](#create-integration-user-role-in-servicenow-app).

## <a name="connection-procedure"></a>**Procédure de connexion**
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

> [!NOTE]
> Dans ServiceNow, il existe une limite horaire du débit des requêtes. Pour configurer la limite, utilisez cette valeur en définissant « Limitation du débit entrant de l’API REST » dans l’instance ServiceNow.

## <a name="create-integration-user-role-in-servicenow-app"></a>Créer un rôle utilisateur de l’intégration dans l’application ServiceNow

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
> Le connecteur ITSM peuvent envoyer des incidents à ServiceNow sans d’autres modules installés sur votre instance ServiceNow. Si vous utilisez le module EventManagement dans votre instance ServiceNow et si vous souhaitez créer des Événements ou des Alertes dans ServiceNow à l’aide du connecteur, ajoutez les rôles suivants à l’utilisateur d’intégration :
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble du connecteur ITSM](itsmc-overview.md)
* [Créer des éléments de travail ITSM à partir des alertes Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Résolution des problèmes liés au connecteur ITSM](./itsmc-resync-servicenow.md)