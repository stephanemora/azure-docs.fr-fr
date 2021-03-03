---
title: Connecter Cherwell à IT Service Management Connector
description: Cet article fournit des informations vous indiquant comment connecter Cherwell à IT Service Management Connector (ITSMC) dans Azure Monitor pour surveiller et gérer les éléments de travail ITSM de manière centralisée.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 6d9d8136ef0936927af238726e30c7feed7d0b83
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100599341"
---
# <a name="connect-cherwell-with-it-service-management-connector"></a>Connecter Cherwell à IT Service Management Connector

Cet article fournit des informations vous indiquant comment configurer la connexion entre votre instance Cherwell au connecteur de gestion des services informatiques (ITSMC) dans Log Analytics pour gérer de manière centralisée vos éléments de travail.

> [!NOTE]
> À compter du 1er octobre 2020, l’intégration ITSM de Cherwell à Azure Alert ne sera plus disponible pour les nouveaux clients. Les nouvelles connexions ITSM ne seront pas prises en charge.
> Les connexions ITSM existantes continueront d’être prises en charge.

Les sections suivantes fournissent des détails sur la connexion de votre produit Cherwell au connecteur ITSM dans Azure.

## <a name="prerequisites"></a>Prérequis

Vérifiez que les prérequis suivants sont remplis :

- Connecteur ITSM installé. Plus d’informations : [Ajout de la solution Connecteur de gestion des services informatiques](./itsmc-definition.md#add-it-service-management-connector).
- ID client généré. Plus d’informations : [Générer un ID client pour Cherwell](#generate-client-id-for-cherwell).
- Rôle utilisateur :  Administrateur.

## <a name="connection-procedure"></a>Procédure de connexion

Exécutez la procédure suivante pour créer une connexion Cherwell :

1. Dans le portail Azure, accédez à **Toutes les ressources** et recherchez **ServiceDesk(nom_de_votre_espace_de_travail)**

2. Sous **SOURCES DE DONNÉES DE L’ESPACE DE TRAVAIL** dans le volet gauche, cliquez sur **Connexions ITSM**.
    ![Nouvelle connexion](/media/itsmc-overview/add-new-itsm-connection.png)

3. En haut du panneau droit, cliquez sur **Ajouter**.

4. Indiquez les informations comme décrit dans le tableau suivant, puis cliquez sur **OK** pour créer la connexion.

> [!NOTE]
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

![Connexion Cherwell](media/itsmc-connections-cherwell/itsm-connections-cherwell-latest.png)

**En cas de connexion et de synchronisation réussies** :

- Les éléments de travail sélectionnés dans une instance Cherwell sont importés dans Azure **Log Analytics.** Vous pouvez afficher le résumé de ces éléments de travail sur la vignette d’IT Service Management Connector.

- Vous pouvez créer des incidents à partir d’alertes Log Analytics, d’enregistrements de journaux ou d’alertes Azure dans cette instance Cherwell.

En savoir plus : [Créer des éléments de travail ITSM à partir d’alertes Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Générer un ID client pour Cherwell

Pour générer l’ID client/la clé de Cherwell, procédez comme suit :

1. Connectez-vous à votre instance Cherwell en tant qu’administrateur.
2. Cliquez sur **Sécurité** > **Edit REST API client settings (Modifier les paramètres du client de l’API REST)** .
3. Sélectionnez **Créer un client** > **Clé secrète client**.

    ![Id utilisateur de Cherwell](media/itsmc-connections-cherwell/itsmc-cherwell-client-id.png)

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble du connecteur ITSM](itsmc-overview.md)
* [Créer des éléments de travail ITSM à partir des alertes Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Résolution des problèmes liés au connecteur ITSM](./itsmc-resync-servicenow.md)