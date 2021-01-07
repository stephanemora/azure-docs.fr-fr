---
title: Connecter Provance au connecteur de gestion des services informatiques
description: Cet article fournit des informations vous indiquant comment connecter Provance au connecteur de gestion des services informatiques (ITSMC) dans Azure Monitor pour surveiller et gérer les éléments de travail ITSM de manière centralisée.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 19d2ef1d4d711738d60938aac502dfd490e021a1
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97729533"
---
# <a name="connect-provance-with-it-service-management-connector"></a>Connecter Provance au connecteur de gestion des services informatiques

Cet article fournit des informations vous indiquant comment configurer la connexion entre votre instance Provance au connecteur de gestion des services informatiques (ITSMC) dans Log Analytics pour gérer de manière centralisée vos éléments de travail.

> [!NOTE]
> Nous proposons à nos clients Cherwell et Provance d’utiliser une [action webhook](./action-groups.md#webhook) pour point de terminaison Cherwell et Provance en guise d’autre solution pour l’intégration.

Les sections suivantes fournissent des détails sur la connexion de votre produit Provance au connecteur ITSM dans Azure.

## <a name="prerequisites"></a>Prérequis

Vérifiez que les prérequis suivants sont remplis :

- Connecteur ITSM installé. Plus d’informations : [Ajout de la solution Connecteur de gestion des services informatiques](./itsmc-definition.md#add-it-service-management-connector).
- L’application Provance doit être inscrite auprès d’Azure AD, et l’ID client est mis à disposition. Pour plus d’informations, consultez [Comment configurer votre application pour utiliser la connexion Azure Active Directory](../../app-service/configure-authentication-provider-aad.md).

- Rôle utilisateur :  Administrateur.

## <a name="connection-procedure"></a>Procédure de connexion

Exécutez la procédure suivante pour créer une connexion Provance :

1. Dans le portail Azure, accédez à **Toutes les ressources** et recherchez **ServiceDesk(nom_de_votre_espace_de_travail)**

2. Sous **SOURCES DE DONNÉES DE L’ESPACE DE TRAVAIL** dans le volet gauche, cliquez sur **Connexions ITSM**.
    ![Nouvelle connexion](media/itsmc-connections/add-new-itsm-connection.png)

3. En haut du panneau droit, cliquez sur **Ajouter**.

4. Indiquez les informations comme décrit dans le tableau suivant, puis cliquez sur **OK** pour créer la connexion.

> [!NOTE]
> Tous ces paramètres sont obligatoires.

| **Champ** | **Description** |
| --- | --- |
| **Nom de connexion**   | Tapez le nom de l’instance Provance que vous souhaitez connecter au connecteur ITSM.  Vous utiliserez ce nom ultérieurement dans lorsque vous configurerez des éléments de travail dans cette instance ITSM ou afficherez une analytique des journaux d’activité détaillée. |
| **Type de partenaire**   | Sélectionnez **Provance**. |
| **Nom d’utilisateur**   | Tapez le nom d’utilisateur pouvant se connecter au connecteur ITSM.    |
| **Mot de passe**   | Tapez le mot de passe associé à ce nom d’utilisateur. **Remarque :** Le nom d’utilisateur et le mot de passe sont utilisés uniquement pour générer des jetons d’authentification. Ils ne sont pas stockés dans le service ITSMC.|
| **URL du serveur**   | Tapez l’URL de l’instance Provance que vous souhaitez connecter au connecteur ITSM. |
| **ID client**   | Tapez l’ID client que vous avez généré dans votre instance Provance pour authentifier cette connexion.  Pour plus d’informations sur l’ID client, consultez [Comment configurer votre application pour utiliser la connexion Azure Active Directory](../../app-service/configure-authentication-provider-aad.md). |
| **Étendue de la synchronisation des données**   | Sélectionnez les éléments de travail de Provance que vous souhaitez synchroniser avec Azure Log Analytics via le connecteur ITSM.  Ces éléments de travail sont importés dans Log Analytics.   **Options :**   Incidents, demandes de modification.|
| **Synchroniser les données** | Tapez le nombre de jours passés dont vous souhaitez les données. **Limite maximale** : 120 jours. |
| **Create new configuration item in ITSM solution (Créer un élément de configuration dans la solution ITSM)** | Sélectionnez cette option si vous souhaitez créer les éléments de configuration dans le produit ITSM. Lorsque cette option est sélectionnée, ITSMC crée les éléments de configuration affectés en tant qu’éléments de configuration (dans le cas d’éléments de configuration non existants) dans le système ITSM pris en charge. **Par défaut** : désactivée.|

![Capture d’écran mettant en évidence les listes Nom de connexion et Type de partenaire.](media/itsmc-connections/itsm-connections-provance-latest.png)

**En cas de connexion et de synchronisation réussies** :

- Les éléments de travail sélectionnés dans une instance Provance sont importés dans Azure **Log Analytics.** Vous pouvez afficher le résumé de ces éléments de travail sur la vignette d’IT Service Management Connector.

- Vous pouvez créer des incidents à partir d’alertes Log Analytics, d’enregistrements de journaux ou d’alertes Azure dans cette instance Provance.

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble du connecteur ITSM](itsmc-overview.md)
* [Créer des éléments de travail ITSM à partir des alertes Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Résolution des problèmes liés au connecteur ITSM](./itsmc-resync-servicenow.md)