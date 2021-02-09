---
title: Connecter ServiceNow au connecteur ITSM
description: Découvrez comment connecter ServiceNow au connecteur de gestion des services informatiques (ITSMC) dans Azure Monitor pour surveiller et gérer les éléments de travail ITSM de manière centralisée.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: cc4f961c399e9ac5e853085106924584eb3935d0
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99223387"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>Connecter ServiceNow au connecteur ITSM

Cet article vous montre comment configurer la connexion entre une instance ServiceNow au connecteur de gestion des services informatiques (ITSMC) dans Log Analytics, ce qui vous permet de gérer de manière centralisée vos éléments de travail de gestion des services informatiques (ITSMC).

## <a name="prerequisites"></a>Prérequis
Assurez-vous de satisfaire les prérequis suivants pour la connexion.

### <a name="itsmc-installation"></a>Installation d’ITSMC

Pour plus d’informations sur l’installation d’ITSMC, consultez [Ajouter la solution IT Service Management Connector](./itsmc-definition.md#add-it-service-management-connector).

> [!NOTE]
> ITSMC prend en charge uniquement l’offre SaaS (software as a service) officielle de ServiceNow. Les déploiements privés de ServiceNow ne sont pas pris en charge.

### <a name="oauth-setup"></a>Paramètres OAuth

Les versions prises en charge par ServiceNow incluent Paris, Orlando, New York, Madrid, London, Kingston, Jakarta, Istanbul, Helsinki et Geneva.

Les administrateurs ServiceNow doivent générer un ID client et une clé secrète client pour leur instance ServiceNow. Consultez les informations requises suivantes :

- [Configurer OAuth pour Paris](https://docs.servicenow.com/bundle/paris-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurer OAuth pour Orlando](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurer OAuth pour New York](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurer OAuth pour Madrid](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurer OAuth pour London](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurer OAuth pour Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurer OAuth pour Jakarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurer OAuth pour Istanbul](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurer OAuth pour Helsinki](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configurer OAuth pour Geneva](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)

Dans le cadre de la configuration d’OAuth, nous vous recommandons de procéder comme suit :

1. [Créez un point de terminaison pour que les clients accèdent à l’instance](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_CreateEndpointforExternalClients.html).

1. Mettez à jour la durée de vie du jeton d’actualisation :

   1. Dans le volet **ServiceNow**, recherchez **Système OAuth**, puis sélectionnez **Registre d’application**. 
   1. Sélectionnez le nom du OAuth qui a été défini, puis définissez le champ de **durée de vie du jeton d'actualisation** sur **7 776 000 secondes** (90 jours). 
   1. Sélectionnez **Update**. 

1. Établissez une procédure interne pour garantir le maintien de la connexion. Deux jours avant l’expiration attendue de la durée de vie du jeton d’actualisation, effectuez les opérations suivantes :

   1. [Effectuez une synchronisation manuelle pour la configuration du connecteur ITSM](./itsmc-resync-servicenow.md).

   1. Révoquez l’ancien jeton d’actualisation. Nous vous déconseillons de conserver les anciennes clés pour des raisons de sécurité. 
   
      1. Dans le volet **ServiceNow**, recherchez **Système OAuth**, puis sélectionnez **Gérer les jetons**. 
      
      1. Sélectionnez l’ancien jeton dans la liste en fonction du nom OAuth et de la date d’expiration.

         ![Capture d’écran montrant une liste de jetons pour OAuth.](media/itsmc-connections/snow-system-oauth.png)
      1. Sélectionnez **Révoquer l’accès** > **Révoquer**.

## <a name="install-the-user-app-and-create-the-user-role"></a>Installer l’application utilisateur et créer le rôle d’utilisateur

Utilisez la procédure suivante pour installer l’application utilisateur ServiceNow et créer le rôle d’utilisateur de l’intégration pour celle-ci. Vous utiliserez ces informations d’identification pour établir la connexion ServiceNow dans Azure.

> [!NOTE]
> ITSMC prend en charge uniquement l’application utilisateur officielle pour l’intégration de Microsoft Log Analytics qui est téléchargée à partir du magasin ServiceNow. ITSMC ne prend pas en charge l’ingestion de code côté ServiceNow ou une application qui ne fait pas partie de la solution ServiceNow officielle. 

1. Visitez le [magasin ServiceNow](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) et installez **l’application utilisateur pour l’intégration de Microsoft OMS et de ServiceNow** dans votre instance ServiceNow.
   
   >[!NOTE]
   >Dans le cadre de la transition en cours depuis Microsoft Operations Management Suite (OMS) vers Azure Monitor, OMS est maintenant appelé Log Analytics.     
2. Après l’installation, accédez à la barre de navigation gauche de l’instance ServiceNow, puis recherchez et sélectionnez l’**intégrateur Microsoft OMS**.  
3. Sélectionnez **Liste de vérifications d’installation**.

   L’état **Incomplet** est affiché car le rôle utilisateur n’a pas encore été créé.

4. Dans la zone de texte située en regard de **Create integration user** (Créer un utilisateur de l’intégration), entrez le nom de l’utilisateur qui peut se connecter à ITSMC dans Azure.
5. Entrez le mot de passe de cet utilisateur, puis sélectionnez **OK**.  

L’utilisateur nouvellement créé est affiché avec les rôles par défaut affectés :

- personalize_choices
- import_transformer
- x_mioms_microsoft.user
- itil
- template_editor
- view_changer

Une fois l’utilisateur créé, l’état de l’option **Liste de vérifications d’installation** est défini sur **Terminé**, et affiche les détails du rôle utilisateur créé pour l’application.

> [!NOTE]
> ITSMC peut envoyer des incidents à ServiceNow sans d’autres modules installés sur votre instance ServiceNow. Si vous utilisez le module EventManagement dans votre instance ServiceNow et si vous souhaitez créer des événements ou des alertes dans ServiceNow à l’aide du connecteur, ajoutez les rôles suivants à l’utilisateur d’intégration :
> 
> - evt_mgmt_integration
> - evt_mgmt_operator  

## <a name="create-a-connection"></a>Créer une connexion
Exécutez la procédure suivante pour créer une connexion ServiceNow.

> [!NOTE]
> Les alertes envoyées depuis Azure Monitor peuvent créer l'un des éléments suivants dans ServiceNow : des événements, des incidents ou des alertes. 

1. Dans le portail Azure, accédez à **Toutes les ressources** et recherchez **ServiceDesk(nom_de_votre_espace_de_travail)** .

2. Sous **Source de données de l’espace de travail**, sélectionnez **Connexions ITSM**.

   ![Capture d’écran présentant la sélection d’une source de données.](media/itsmc-connections/add-new-itsm-connection.png)

3. En haut du panneau droit, sélectionnez **Ajouter**.

4. Indiquez les informations, comme décrit dans le tableau suivant, puis sélectionnez **OK**.

   | **Champ** | **Description** |
   | --- | --- |
   | **Nom de connexion**   | Entrez le nom de l’instance ServiceNow que vous souhaitez connecter à ITSMC. Vous utiliserez ce nom ultérieurement dans Log Analytics lors de la configuration des éléments de travail ITSM ou de l’affichage d’une analytique détaillée. |
   | **Type de partenaire**   | Sélectionnez **ServiceNow**. |
   | **URL du serveur**   | Entrez l’URL de l’instance ServiceNow que vous souhaitez connecter à ITSMC. L’URL doit pointer vers une version SaaS prise en charge avec le suffixe *.servicenow.com* (par exemple : https://XXXXX.service-now.com/).|
   | **Nom d’utilisateur**   | Entrez le nom d’utilisateur de l’intégration que vous avez créé dans l’application ServiceNow pour prendre en charge la connexion à ITSMC.|
   | **Mot de passe**   | Entrez le mot de passe associé à ce nom d’utilisateur. **Remarque** : Le nom d’utilisateur et le mot de passe servent uniquement à générer des jetons d’authentification. Ils ne sont stockés nulle part dans le service ITSMC.  |
   | **ID de client**   | Entrez l’ID client généré précédemment que vous souhaitez utiliser pour l’authentification OAuth2. Pour plus d’informations sur la génération d’un ID client et d’un secret, consultez [Configurer OAuth](https://old.wiki/index.php/OAuth_Setup). |
   | **Clé secrète client**   | Entrez la clé secrète client, générée pour cet ID.   |
   | **Étendue de la synchronisation des données (en jours)** | Entrez le nombre de jours passés dont vous souhaitez les données. La limite est de 120 jours. |
   | **Éléments de travail à synchroniser**   | Sélectionnez les éléments de travail de ServiceNow que vous souhaitez synchroniser avec Azure Log Analytics via ITSMC. Les valeurs sélectionnées sont importées dans Log Analytics. Les options sont des demandes de modification.|
   | **Créer un élément de configuration dans le produit ITSM** | Sélectionnez cette option si vous souhaitez créer les éléments de configuration dans le produit ITSM. Lorsqu’il est sélectionné, ITSMC crée des éléments de configuration (s’il n’en existe aucun) dans le système ITSM pris en charge. Elle est désactivée par défaut. |

![Capture d’écran des zones et options permettant d’ajouter une connexion ServiceNow.](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

En cas de connexion et de synchronisation réussies :

- Les éléments de travail sélectionnés dans l’instance ServiceNow sont importés dans Log Analytics. Vous pouvez afficher le résumé de ces éléments de travail sur la vignette d’**IT Service Management Connector**.

- Vous pouvez créer des incidents à partir d’alertes Log Analytics, d’enregistrements de journaux ou d’alertes Azure dans cette instance ServiceNow.

> [!NOTE]
> ServiceNow comporte une limite horaire du débit des requêtes. Pour configurer la limite, définissez **Limitation du débit entrant de l’API REST** dans l’instance ServiceNow.

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble du connecteur ITSM](itsmc-overview.md)
* [Créer des éléments de travail ITSM à partir des alertes Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Résolution des problèmes dans le connecteur ITSM](./itsmc-resync-servicenow.md)
