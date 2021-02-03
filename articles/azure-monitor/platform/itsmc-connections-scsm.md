---
title: Connecter SCSM avec le connecteur de gestion des services informatiques
description: Cet article fournit des informations vous indiquant comment connecter SCSM au connecteur de gestion des services informatiques dans Azure Monitor pour surveiller et gérer les éléments de travail ITSM de manière centralisée.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 907a78b15cca4718308f79bc6be2e6258bc04d19
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492669"
---
# <a name="connect-system-center-service-manager-with-it-service-management-connector"></a>Connecter System Center Service Manager au connecteur de gestion des services informatiques

Cet article fournit des informations vous indiquant comment configurer la connexion entre votre instance System Center Service Manager et le connecteur de gestion des services informatiques dans Log Analytics pour gérer de manière centralisée vos éléments de travail.

Les sections suivantes fournissent des détails sur la connexion de votre produit System Center Service Manager au connecteur ITSM dans Azure.

## <a name="prerequisites"></a>Prérequis

Vérifiez que les prérequis suivants sont remplis :

- Connecteur ITSM installé. Plus d’informations : [Ajout de la solution Connecteur de gestion des services informatiques](./itsmc-definition.md).
- L’application web Service Manager (application web) est déployée et configurée. Pour plus d’informations sur l’application web, cliquez [ici](#create-and-deploy-service-manager-web-app-service).
- Connexion hybride créée et configurée. Plus d’informations : [Configurez la connexion hybride](#configure-the-hybrid-connection).
- Versions prises en charge de Service Manager :  2012 R2 ou 2016.
- Rôle utilisateur :  [Opérateur avancé](/previous-versions/system-center/service-manager-2010-sp1/ff461054(v=technet.10)).
- Actuellement, les alertes envoyées à partir d'Azure Monitor peuvent créer des incidents dans System Center Service Manager.

> [!NOTE]
> - Le connecteur ITSM peut uniquement se connecter aux instances de ServiceNow basées sur le cloud. Les instances de ServiceNow en local ne sont pas prises en charge actuellement.
> - Afin d’utiliser des [modèles](./itsmc-definition.md#define-a-template) personnalisés dans le cadre des actions, le paramètre « ProjectionType » dans le modèle SCSM doit être mappé à « IncidentManagement!System.WorkItem.Incident.ProjectionType »

## <a name="connection-procedure"></a>Procédure de connexion

Utilisez la procédure suivante pour connecter votre instance System Center Service Manager au connecteur ITSM :

1. Dans le portail Azure, accédez à **Toutes les ressources** et recherchez **ServiceDesk(nom_de_votre_espace_de_travail)**

2. Sous **SOURCES DE DONNÉES DE L’ESPACE DE TRAVAIL** dans le volet gauche, cliquez sur **Connexions ITSM**.

    ![Nouvelle connexion](media/itsmc-connections/add-new-itsm-connection.png)

3. En haut du panneau droit, cliquez sur **Ajouter**.

4. Indiquez les informations comme décrit dans le tableau suivant, puis cliquez sur **OK** pour créer la connexion.

> [!NOTE]
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

En savoir plus : [Créer des éléments de travail ITSM à partir d’alertes Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="create-and-deploy-service-manager-web-app-service"></a>Créer et déployer l’application web Service Manager

Pour connecter l’instance Service Manager locale au connecteur ITSM dans Azure, Microsoft a créé une application web Service Manager sur GitHub.

Pour configurer l’application Web ITSM pour votre instance Service Manager, procédez comme suit :

- **Déployez l’application web** : déployez l’application web, définissez les propriétés et authentifiez-vous auprès d’Azure AD. Vous pouvez déployer l’application web à l’aide du [script automatisé](./itsmc-service-manager-script.md) que Microsoft vous a fourni.
- **Configurez la connexion hybride** - [Configurez cette connexion](#configure-the-hybrid-connection) manuellement.

### <a name="deploy-the-web-app"></a>Déployer l’application web
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

## <a name="configure-the-hybrid-connection"></a>Configurer la connexion hybride

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

### <a name="configure-the-listener-setup"></a>Configurer l’écouteur

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

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble du connecteur ITSM](itsmc-overview.md)
* [Créer des éléments de travail ITSM à partir des alertes Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Résolution des problèmes liés au connecteur ITSM](./itsmc-resync-servicenow.md)