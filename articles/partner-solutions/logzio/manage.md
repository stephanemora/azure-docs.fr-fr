---
title: Gérer l’intégration d’Azure avec Logz.io - Solutions des partenaires Azure
description: Découvrez comment gérer l’intégration d’Azure avec Logz.io.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 08/17/2021
ms.author: tomfitz
ms.openlocfilehash: e86b42694276c23f4d38809c9b40a7f92e39fa64
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122527876"
---
# <a name="manage-the-logzio-integration-in-azure"></a>Gérer l’intégration Logz.io à Azure

Cet article explique comment gérer les paramètres de votre intégration Azure avec Logz.io.

## <a name="resource-overview"></a>Vue d’ensemble des ressources

L’écran **Vue d’ensemble** de Logz.io montre les détails de la ressource, avec les éléments suivants :

- **Groupe de ressources**
- **Lieu**
- **Abonnement**
- **Balises**
- **Authentification unique Logz.io**
- **Fréquence de facturation**

Selon que vous avez choisi ou non l’authentification unique, le lien d’**authentification unique Logz.io** varie :

- Si vous avez opté pour l’authentification unique :

    :::image type="content" source="./media/manage/sso-opt-in.png" alt-text="Abonnement à l’authentification unique.":::

- Si vous n’avez pas opté pour l’authentification unique :

    :::image type="content" source="./media/manage/sso-opt-out.png" alt-text="Désabonnement de l’authentification unique.":::

## <a name="reconfigure-rules-for-logs-and-metrics"></a>Reconfigurer les règles pour les journaux et les métriques

Pour changer les règles de configuration, sous **Configuration Logz**, sélectionnez **Journaux et métriques**. Pour plus d’informations, consultez [Journaux de configuration](./create.md#configure-logs).

:::image type="content" source="./media/manage/logs-metrics.png" alt-text="Configuration des journaux et des métriques.":::

## <a name="configure-diagnostic-settings"></a>Configurer les paramètres de diagnostic

Pour configurer les paramètres de diagnostic, sélectionnez la ressource. Dans le volet gauche, sélectionnez **Paramètres de diagnostic**.

Dans la section **Détails de la destination**, cochez l’option **Envoyer aux solutions partenaires pour** sélectionner Logz.io comme cible de destination. Cette option est disponible uniquement après la création d’une ressource Logz.

:::image type="content" source="./media/manage/configure-diagnostics.png" alt-text="Configurer les paramètres de diagnostic.":::

## <a name="view-monitored-resources"></a>Afficher les ressources supervisées

Pour voir la liste des ressources qui envoient des journaux à Logz.io (via les règles d’entreprise configurées), accédez à **Configuration de Logz** et sélectionnez **Ressources supervisées**.

:::image type="content" source="./media/manage/monitored-resources.png" alt-text="Configuration des Ressources supervisées.":::

Vous pouvez filtrer la liste par type de ressource, nom de groupe de ressources, emplacement et si la ressource envoie des journaux.

Voici les descriptions des colonnes affichées sur l’écran **Ressources supervisées** :

- **Nom de ressource** : nom de ressource Azure.
- **Type de ressource** : type de ressource Azure.
- **Groupe de ressources** : affiche le nom du groupe de ressources pour la ressource Azure.
- **Région** : affiche la région ou l’emplacement de la ressource Azure.
- **Journaux vers Elastic** : indique si la ressource envoie des journaux à Logz.io. Si la ressource n’envoie pas de journaux, ce champ indique pourquoi les journaux ne sont pas envoyés à Logz.io. Raisons possibles :
  - La ressource ne prend pas en charge l’envoi de journaux : seuls les journaux des ressources Azure pour tous les types de ressources et les [catégories de journaux définis ici](../../azure-monitor/essentials/resource-logs-categories.md) peuvent être configurés pour envoyer des journaux à Logz.io.
  - Limite de cinq paramètres de diagnostics atteints : chaque ressource Azure peut avoir un maximum de cinq paramètres de diagnostic. Pour plus d’informations, consultez [Créer des paramètres de diagnostic pour envoyer des journaux et des métriques de plateforme à différentes destinations](../../azure-monitor/essentials/diagnostic-settings.md).
  - Erreur : Une erreur empêche l’envoi des journaux à Logz.io.
  - Journaux non configurés : seules les ressources qui ont les étiquettes de ressources appropriées peuvent envoyer des journaux à Logz.io. Consultez [Configurer les journaux](./create.md#configure-logs).
  - Région non prise en charge : la ressource Azure se trouve dans une région qui n’envoie pas actuellement de journaux à Logz.io.

Pour modifier la liste des étiquettes de la ressource Azure, sélectionnez **Modifier les étiquettes**. Vous pouvez ajouter de nouvelles étiquettes ou supprimer des étiquettes existantes.

## <a name="monitor-vm-using-logzio-agent"></a>Surveiller une machine virtuelle à l’aide de l’agent Logz.io

Vous pouvez installer des agents Logz.io sur des machines virtuelles en tant qu’extension. Accédez à **Configuration Logz** et sélectionnez **Agent de machine virtuelle**. Cet écran affiche la liste de toutes les machines virtuelles dans l’abonnement.

Pour chaque machine virtuelle, les données suivantes s’affichent :

- **Nom de la ressource** : nom de la machine virtuelle.
- **Type de ressource** : type de ressource Azure.
- **Groupe de ressources** : affiche le nom du groupe de ressources pour la ressource Azure.
- **Région** : affiche la région ou l’emplacement de la ressource Azure.
- **Version de l’agent** : numéro de version de l’agent Logz.io.
- **Statut de l’extension** : indique si l’agent Logz.io est **installé** ou **non installé** sur la machine virtuelle.
- **Journaux vers Logz** : spécifie si l’agent Logz.io envoie les journaux à Logz.io.

:::image type="content" source="./media/manage/vm-agent.png" alt-text="Agent de machine virtuelle.":::

### <a name="install-vm-agent"></a>Installer l’agent de machine virtuelle

Pour installer l’agent de machine virtuelle, procédez comme suit.

1. Sélectionnez la machine virtuelle sur laquelle vous voulez installer l’agent Logz.io, puis sélectionnez **Installer l’agent**.

   Le portail vous demande de confirmer que vous souhaitez installer l’agent avec l’authentification par défaut.

1. Sélectionnez **OK** pour commencer l’installation.

   Azure affiche l’état **Installation** jusqu’à ce que l’agent soit installé et provisionné.

   Après l’installation de l’agent Logz.io, l’état passe à **Installé**.

1. Pour vérifier si l’agent Logz.io a été installé, sélectionnez la machine virtuelle et consultez la fenêtre **Extensions**.

> [!NOTE]
> Une fois l’agent de machine virtuelle installé, si la machine virtuelle est arrêtée, la colonne **Journaux vers Logz** affiche **Ne pas envoyer** pour cette machine virtuelle.

### <a name="uninstall-vm-agent"></a>Désinstaller l’agent de machine virtuelle

Vous pouvez désinstaller des agents Logz.io sur une machine virtuelle avec l’**agent de machine virtuelle**. Sélectionnez la machine virtuelle et **désinstallez l’agent**.

## <a name="create-logzio-sub-account"></a>Créer un sous-compte Logz.io

Seul un administrateur de compte Logz.io peut créer un sous-compte. Par défaut, le créateur du compte Logz.io est défini en tant qu’administrateur. Connectez-vous au compte Logz.io pour accorder à d’autres utilisateurs l’autorisation administrateur.

Pour plus d’informations, consultez [Sous-comptes Logz.io](https://logz.io/blog/diving-deeper-logz-io-sub-accounts/) sur le site web Logz.io.

Vous pouvez créer un sous-compte Logz.io avec le bouton **Vue d’ensemble** > **Ajouter un sous-compte**.

:::image type="content" source="./media/manage/create-sub-account-overview.png" alt-text="Créer un sous-compte Logz.io à partir de la vue d’ensemble.":::

Une autre méthode consiste à accéder à la **configuration de Logz** et à sélectionner **Ajouter un sous-compte**. Suivez les invites pour configurer le sous-compte et activer la livraison des journaux par le biais du sous-compte.

:::image type="content" source="./media/manage/create-sub-account-logz.png" alt-text="Créer un sous-compte Logz.io à partir de la configuration Logz.":::

## <a name="delete-logzio-sub-account"></a>Supprimer un sous-compte Logz.io

1. Pour la ressource Logz.io, sélectionnez **Configuration Logz** et **Sous-comptes Logz**.
1. Sélectionnez le sous-compte que vous souhaitez supprimer.
1. Sélectionnez **Supprimer**.

    :::image type="content" source="./media/manage/delete-sub-account-1.png" alt-text="Supprimer un sous-compte Logz.io à partir de la configuration Logz.":::

1. Tapez _Oui_ pour confirmer la suppression de la ressource Logz.io.
1. Sélectionnez **Supprimer**.

    :::image type="content" source="./media/manage/delete-sub-account-2.png" alt-text="Confirmer la suppression d’un sous-compte Logz.io.":::

Les journaux ne sont plus envoyés à Logz.io et l’ensemble de la facturation pour Logz.io s’arrête.

## <a name="delete-logzio-account"></a>Supprimer un compte Logz.io

1. À partir de la ressource Logz.io, sélectionnez **Vue d’ensemble** et **Supprimer**.
1. Confirmez la suppression de la ressource Logz.io.
1. Sélectionnez **Supprimer**.

    :::image type="content" source="./media/manage/delete-resource-1.png" alt-text="Supprimer une ressource Logz.io de la vue d’ensemble.":::

1. Tapez _Oui_ pour confirmer la suppression de la ressource Logz.io.
1. Sélectionnez **Supprimer**.

    :::image type="content" source="./media/manage/delete-resource-2.png" alt-text="Confirmer la suppression d’un sous-compte Logz.io.":::

> [!NOTE]
> L’option **Supprimer** du compte principal n’est activée que lorsque tous les sous-comptes mappés au compte principal sont déjà supprimés. Pour plus d’informations sur la suppression des sous-comptes, consultez [Supprimer des sous-comptes Logz.io](#delete-logzio-sub-account).

Une fois la ressource supprimée, les journaux ne sont plus envoyés à Logz.io et l’ensemble de la facturation s’arrête pour Logz.io via la place de marché Azure.

## <a name="get-support"></a>Obtenir de l’aide

Pour contacter le support à propos de l’intégration d’Azure Logz.io, sous **Support + résolution des problèmes** et **Nouvelle demande de support**. Cette option vous ouvre le [portail Logz.io](https://app.logz.io/). Utilisez le support intégré à la conversation ou envoyez un e-mail à [help@logz.io](mailto:help@logz.io).

## <a name="next-steps"></a>Étapes suivantes

- Pour résoudre les problèmes liés à l’intégration, consultez [Résolution des problèmes](troubleshoot.md).
