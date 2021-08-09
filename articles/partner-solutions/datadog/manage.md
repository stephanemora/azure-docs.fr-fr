---
title: Gestion d’une ressource Datadog – Solutions partenaires Azure
description: Cet article décrit la gestion d’une ressource Datadog sur le portail Azure. Comment configurer l’authentification unique, supprimer une organisation Confluent et obtenir de l’assistance.
ms.service: partner-services
ms.topic: conceptual
ms.date: 05/28/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: e964fd436795e19cc77a25efa95de5f6fcac87ca
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110652433"
---
# <a name="manage-the-datadog-resource"></a>Gestion d’une ressource Datadog

Cet article explique comment gérer les paramètres de votre intégration Azure avec Datadog.

## <a name="resource-overview"></a>Vue d’ensemble des ressources

Pour afficher les détails de votre ressource Datadog, sélectionnez **Vue d’ensemble** dans le volet gauche.

:::image type="content" source="media/manage/resource-overview.png" alt-text="Vue d’ensemble de la ressource Datadog" border="true" lightbox="media/manage/resource-overview.png":::

Les détails sont les suivants :

- Nom de groupe ressources
- Emplacement/région
- Abonnement
- Balises
- Lien d’authentification unique vers l’organisation Datadog
- Offre/plan Datadog
- Fréquence de facturation

Fournit également des liens vers les tableaux de bord, les journaux et les mappages d’hôtes Datadog.

L’écran Vue d’ensemble fournit un résumé des ressources qui envoient des journaux et des métriques à Datadog.

- Type de ressource : type de ressource Azure.
- Ressources totales : nombre total de ressources pour le type de ressource.
- Ressources envoyant des journaux : nombre de ressources qui envoient des journaux à Datadog via l’intégration.
- Ressources envoyant des métriques : nombre de ressources qui envoient des métriques à Datadog via l’intégration.

## <a name="reconfigure-rules-for-metrics-and-logs"></a>Reconfigurer les règles pour les métriques et les journaux

Pour modifier les règles de configuration des métriques et des journaux, sélectionnez **Métriques et journaux** dans le volet gauche.

:::image type="content" source="media/manage/reconfigure-metrics-and-logs.png" alt-text="Modifiez la configuration des journaux et des métriques pour la ressource Datadog." border="true":::

Pour plus d’informations, consultez [Configurer les métriques et les journaux](create.md#configure-metrics-and-logs).

## <a name="view-monitored-resources"></a>Afficher les ressources supervisées

Pour afficher la liste des ressources émettant des journaux vers Datadog, sélectionnez **Ressources supervisées** dans le volet gauche.

:::image type="content" source="media/manage/view-monitored-resources.png" alt-text="Afficher les ressources supervisées par Datadog" border="true":::

Vous pouvez filtrer la liste des ressources par type de ressource, nom de groupe de ressources, emplacement et selon si la ressource envoie des journaux et des métriques.

La colonne **Journaux vers Datadog** indique si la ressource envoie des journaux à Datadog. Si la ressource n’envoie pas de journaux, ce champ indique pourquoi les journaux ne sont pas envoyés à Datadog. Raisons possibles :

- La ressource ne prend pas en charge l’envoi de journaux. Seuls les types de ressources avec des catégories de journaux de supervision peuvent être configurés pour envoyer des journaux à Datadog.
- Limite de cinq paramètres de diagnostics atteints. Chaque ressource Azure peut avoir un maximum de cinq paramètres de diagnostic. Pour plus d’informations, consultez [Paramètres de diagnostic](../../azure-monitor/essentials/diagnostic-settings.md).
- Erreur. La ressource est configurée pour envoyer des journaux à Datadog, mais elle est bloquée par une erreur.
- Les journaux ne sont pas configurés. Seules les ressources Azure qui possèdent les étiquettes de ressources appropriées sont configurées pour envoyer des journaux à Datadog.
- Région non prise en charge. La ressource Azure se trouve dans une région qui ne prend actuellement pas en charge l’envoi de journaux à Datadog.
- L’agent Datadog n’est pas configuré. Les machines virtuelles sur lesquelles l’agent Datadog n’est pas installé n’envoient pas de journaux vers Datadog.

## <a name="api-keys"></a>API Keys

Pour afficher la liste des clés API pour votre ressource Datadog, sélectionnez les **clés** dans le volet gauche. Vous pouvez voir des informations sur les clés.

:::image type="content" source="media/manage/keys.png" alt-text="Clés API pour l’organisation Datadog." border="true":::

Le portail Azure fournit une vue en lecture seule des clés API. Pour gérer les clés, sélectionnez le lien vers le portail Datadog. Une fois que vous avez effectué des modifications dans le portail Datadog, actualisez la vue du portail Azure.

L’intégration de Datadog dans Azure vous permet d’installer l’agent Datadog sur une machine virtuelle ou une instance App Service. Si une clé par défaut n’est pas sélectionnée, l’installation de l’agent Datadog échoue.

## <a name="monitor-virtual-machines-using-the-datadog-agent"></a>Superviser des machines virtuelles à l’aide de l’agent Datadog

Vous pouvez installer des agents Datadog sur des machines virtuelles en tant qu’extension. Accédez à **l’Agent de machine virtuelle** dans les configurations de l’organisation Datadog dans le volet gauche. Cet écran montre la liste de toutes les machines virtuelles de l’abonnement.

Pour chaque machine virtuelle, les données suivantes s’affichent :

- Nom de la ressource : nom de la machine virtuelle
- État de la ressource : indique si la machine virtuelle est arrêtée ou en cours d’exécution. L’agent Datadog ne peut être installé que sur les machines virtuelles en cours d’exécution. Si la machine virtuelle est arrêtée, l’installation de l’agent Datadog est désactivée.
- Version de l’agent : numéro de version de l’agent Datadog.
- État de l’agent : indique si l’agent Datadog est en cours d’exécution sur la machine virtuelle.
- Intégrations activées : métriques clés collectées par l’agent Datadog.
- Méthode d’installation : outil spécifique utilisé pour installer l’agent Datadog. Par exemple, Chef ou Script.
- Envoi de journaux : indique si l’agent Datadog envoie des journaux à Datadog.

Sélectionnez la machine virtuelle sur laquelle installer l’agent Datadog. Sélectionnez **Installer l’agent**.

Le portail vous demande de confirmer que vous souhaitez installer l’agent avec la clé par défaut. Sélectionnez **OK** pour commencer l’installation. Azure affiche l’état **Installation** jusqu’à ce que l’agent soit installé et provisionné.

Après l’installation de l’agent Datadog, l’état passe à Installé.

Pour vérifier que l’agent Datadog a été installé, sélectionnez la machine virtuelle et accédez à la fenêtre Extensions.

Vous pouvez désinstaller des agents Datadog sur une machine virtuelle en accédant à **l’Agent de machine virtuelle**. Sélectionnez la machine virtuelle, puis sélectionnez **Désinstaller l’agent**.

## <a name="monitor-app-services-using-the-datadog-agent-as-an-extension"></a>Superviser App Services à l’aide de l’agent Datadog en tant qu’extension

Vous pouvez installer des agents Datadog sur des instances App Services en tant qu’extension. Accédez à **Extension App Service** dans le volet gauche. Cet écran affiche la liste des instances App Services dans l’abonnement.

Pour chaque instance App Service, les éléments de données suivants sont affichés :

- Nom de la ressource : nom de la machine virtuelle.
- État de la ressource : indique si l’instance App Service est arrêtée ou en cours d’exécution. L’agent Datadog ne peut être installé que sur des instances App Services en cours d’exécution. Si l’instance App Service est arrêtée, l’installation de l’agent Datadog est désactivée.
- Plan App service : plan spécifique configuré pour l’instance App Service.
- Version de l’agent : numéro de version de l’agent Datadog.

Pour installer l’agent Datadog, sélectionnez l’instance App Service et l’option **Installer l’extension**. L’agent Datadog le plus récent est installé sur l’instance App Service en tant qu’extension.

Le portail confirme que vous souhaitez installer l’agent Datadog. En outre, les paramètres d’application pour l’instance App Service spécifique sont mis à jour avec la clé par défaut. L’instance App Service est redémarrée après l’installation de l’agent Datadog.

Sélectionnez **OK** pour commencer le processus d’installation de l’agent Datadog. Azure affiche l’état **Installation** jusqu’à ce que l’agent soit installé. Après l’installation de l’agent Datadog, l’état passe à Installé.

Pour désinstaller les agents Datadog sur l’instance App Service, accédez à **Extension App Service**. Sélectionner l’instance App Service, puis sélectionnez **Désinstaller l’extension**

## <a name="reconfigure-single-sign-on"></a>Reconfigurer l’authentification unique

Si vous souhaitez reconfigurer l’authentification unique, sélectionnez **Authentification unique** dans le volet gauche.

Pour établir l’authentification unique via Azure Active Directory, sélectionnez **Activer l’authentification unique via Azure Active Directory**.

Le portail récupère l’application Datadog appropriée à partir d’Azure Active Directory. L’application provient du nom de l’application d’entreprise que vous avez sélectionné lors de la configuration de l’intégration. Sélectionnez le nom de l’application Datadog comme indiqué ci-dessous :
 
:::image type="content" source="media/manage/reconfigure-single-sign-on.png" alt-text="Reconfigurez l’application d’authentification unique." border="true":::
 
## <a name="change-plan"></a>Modifier le plan

Pour modifier le plan de facturation Datadog, accédez à **Vue d’ensemble**, puis sélectionnez **Modifier le plan**.

:::image type="content" source="media/manage/datadog-select-change-plan.png" alt-text="Sélectionnez Modifier le plan de facturation Datadog." border="true":::

Le portail récupère tous les plans Datadog disponibles pour votre locataire. Sélectionnez le plan approprié, puis cliquez sur **Modifier le plan**.

:::image type="content" source="media/manage/datadog-change-plan.png" alt-text="Sélectionnez le plan de facturation Datadog à modifier." border="true":::
  
## <a name="disable-or-enable-integration"></a>Désactiver ou activer l’intégration

Vous pouvez arrêter d’envoyer des journaux et des métriques d’Azure vers Datadog. Vous continuerez à être facturé pour les autres services Datadog qui ne sont pas liés à la supervision des métriques et des journaux.

Pour désactiver l’intégration d’Azure avec Datadog, consultez la page **Vue d’ensemble**. Sélectionnez **Désactiver** et **OK**.
 
:::image type="content" source="media/manage/disable.png" alt-text="Désactivez la ressource Datadog." border="true":::

Pour désactiver l’intégration d’Azure avec Datadog, accédez à la **Vue d’ensemble**. Sélectionnez **Activer** et **OK**. L’option **Activer** permet de récupérer toutes les configurations précédentes pour les métriques et les journaux. La configuration détermine les ressources Azure qui émettent des métriques et des journaux vers Datadog. Une fois cette étape effectuée, les métriques et les journaux sont envoyés à Datadog.
 
:::image type="content" source="media/manage/enable.png" alt-text="Activez la ressource Datadog." border="true":::

## <a name="delete-datadog-resource"></a>Supprimer la ressource Datadog

Accédez à la **Vue d’ensemble** dans le volet gauche et sélectionnez **Supprimer**. Confirmez que vous souhaitez supprimer la ressource Datadog. Sélectionnez **Supprimer**.

:::image type="content" source="media/manage/delete.png" alt-text="Supprimer la ressource Datadog" border="true":::

Si une seule ressource Datadog est mappée à une organisation Datadog, les journaux et les métriques ne sont plus envoyés à Datadog. L’ensemble de la facturation s’arrête pour Datadog via la Place de marché Azure.

Si plusieurs ressources Datadog sont mappées à l’organisation Datadog, la suppression de la ressource Datadog entraîne uniquement l’arrêt de l’envoi des journaux et des métriques pour cette ressource Datadog. Étant donné que l’organisation Datadog est liée à d’autres ressources Azure, la facturation continue via la Place de marché Azure.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir de l’aide pour la résolution des problèmes, consultez [Résolution des problèmes pour les solutions Datadog](troubleshoot.md).
