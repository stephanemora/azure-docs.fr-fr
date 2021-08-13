---
title: Gérer une intégration d’Elastic à Azure – Solutions de partenaires Azure
description: Cet article décrit la gestion d’Elastic sur le portail Azure. Comment configurer les paramètres de diagnostic et supprimer la ressource.
ms.service: partner-services
ms.topic: conceptual
ms.date: 05/20/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 9feb4c5eb143ced9079bfe9e63b451aecd437212
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952588"
---
# <a name="manage-the-elastic-integration-with-azure"></a>Gérer l’intégration d’Elastic à Azure

Cet article explique comment gérer votre intégration d’Elastic à Azure. Il montre comment configurer les paramètres de diagnostic et supprimer la ressource Elastic.

## <a name="reconfigure-rules-for-metrics-and-logs"></a>Reconfigurer les règles pour les métriques et les journaux

Lorsque vous avez créé la ressource Elastic, vous avez configuré les journaux qui sont envoyés à Elastic. Si vous avez besoin de modifier ces paramètres, sélectionnez **Métriques et journaux d’activité** dans le volet gauche. Apportez les modifications nécessaires à la façon dont les journaux sont envoyés à Elastic.

Pour plus d’informations sur les deux types de journaux, consultez [Démarrage rapide : Prise en main d’Elastic](create.md).

:::image type="content" source="media/manage/reconfigure-logs.png" alt-text="Modifier les paramètres du journal":::

## <a name="view-monitored-resources"></a>Afficher les ressources supervisées

Pour afficher la liste des ressources qui envoient des journaux à Elastic, sélectionnez **Ressources supervisées** dans le volet gauche.

:::image type="content" source="media/manage/monitored-resources.png" alt-text="Afficher les ressources supervisées":::

Vous pouvez filtrer la liste par type de ressource, nom de groupe de ressources, emplacement et si la ressource envoie des journaux.

La colonne **Journaux vers Elastic** indique si la ressource envoie des journaux à Elastic. Si la ressource n’envoie pas de journaux, ce champ précise pourquoi les journaux ne sont pas envoyés. Raisons possibles :

* La ressource ne prend pas en charge l’envoi de journaux. Seuls les journaux des ressources Azure pour tous les types de ressources et toutes les catégories de journaux définis ici peuvent être configurés pour envoyer des journaux à Elastic
* Limite de cinq paramètres de diagnostics atteints. Chaque ressource Azure peut avoir un maximum de cinq [paramètres de diagnostic](../../azure-monitor/essentials/diagnostic-settings.md).
* Une erreur empêche l’envoi des journaux à Elastic.
* Les journaux ne sont pas configurés pour la ressource. Seules les ressources disposant des balises de ressources appropriées sont envoyées à Elastic. Vous avez spécifié les règles de balises dans la configuration des journaux. 
* La région n’est pas prise en charge. La ressource Azure se trouve dans une région qui n’envoie pas actuellement de journaux à Elastic. 

## <a name="monitor-virtual-machines-using-elastic-agent"></a>Superviser des machines virtuelles à l’aide de l’agent Elastic

Vous pouvez installer des agents Elastic sur des machines virtuelles en tant qu’extension. Pour afficher les machines virtuelles disponibles dans votre abonnement, sélectionnez **Machines virtuelles** dans le volet gauche de votre ressource Elastic.

:::image type="content" source="media/manage/vm-agents.png" alt-text="Afficher les machines virtuelles":::

Pour chaque machine virtuelle, les données suivantes s’affichent :

* Nom de la ressource : nom de la machine virtuelle.
* État de la ressource : indique si la machine virtuelle est arrêtée ou en cours d’exécution. L’agent Elastic ne peut être installé que sur les machines virtuelles en cours d’exécution. Si la machine virtuelle est arrêtée, l’installation de l’agent Elastic est désactivée.
* Version de l’agent : numéro de version de l’agent Elastic.
* État de l’agent : indique si l’agent Elastic est en cours d’exécution sur la machine virtuelle.
* Intégrations activées : métriques clés collectées par l’agent Elastic.
* Envoi de journaux : indique si l’agent Elastic envoie des journaux à Elastic.

Pour installer l’agent Elastic, sélectionnez une machine virtuelle, puis **Installer l’extension**.

Le portail vous demande de confirmer que vous souhaitez installer l’agent avec l’authentification par défaut. Sélectionnez **OK** pour commencer l’installation. Le portail affiche l’état **Installation** jusqu’à ce que l’agent soit installé et approvisionné.

Après l’installation de l’agent Elastic, l’état passe à **Installé**.

Pour vérifier que l’agent Elastic a été installé, sélectionnez la machine virtuelle et accédez à **Extensions**.

Pour désinstaller l’agent Elastic sur une machine virtuelle, sélectionnez la machine virtuelle et **Désinstaller l’extension**.

## <a name="configure-diagnostic-settings"></a>Configurer les paramètres de diagnostic

Pour configurer les paramètres de diagnostic d’une ressource, sélectionnez cette ressource. Dans le volet gauche, sélectionnez **Paramètres de diagnostic**. 

Dans la section des détails de la destination, cochez l’option d’envoi aux solutions de partenaires pour sélectionner Elastic comme cible de destination. Cette option est disponible uniquement après la création d’une ressource Elastic.

:::image type="content" source="media/manage/diagnostic-settings.png" alt-text="Configurer les paramètres de diagnostic":::

## <a name="delete-elastic-resource"></a>Supprimer une ressource Elastic

Lorsque vous n’avez plus besoin de votre ressource Elastic, supprimez-la dans le portail Azure.

Pour supprimer la ressource dans Azure, sélectionnez votre ressource Elastic. Dans **Vue d’ensemble**, sélectionnez **Supprimer**. Confirmez que vous souhaitez supprimer la ressource Elastic.

:::image type="content" source="media/manage/delete-elastic.png" alt-text="Supprimer une ressource Elastic":::

Lorsque la ressource Elastic est supprimée, les journaux ne sont plus envoyés à Elastic. L’ensemble de la facturation s’arrête pour Elastic via Place de marché Azure.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir de l’aide sur le dépannage, consultez [Résolution des problèmes d’intégration d’Elastic à Azure](troubleshoot.md).