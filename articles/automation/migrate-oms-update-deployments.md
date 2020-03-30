---
title: Migrer vos déploiements de mises à jour OMS vers Azure
description: Cet article décrit comment migrer vos déploiements de mise à jour OMS existants vers Azure
services: automation
ms.subservice: update-management
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 2660e4a348d2ffd71f912ff80c36a5a9a3c9fe88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75417779"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>Migrer vos déploiements de mises à jour OMS vers Azure

Le portail Operations Management Suite (OMS) est en cours de [dépréciation](../azure-monitor/platform/oms-portal-transition.md). Toutes les fonctionnalités qui étaient disponibles dans le portail OMS pour Update Management sont disponibles dans le portail Azure. Cet article fournit les informations nécessaires pour migrer vers le portail Azure.

## <a name="key-information"></a>Informations essentielles

* Les déploiements existants continueront de fonctionner. Une fois que vous avez recréé le déploiement dans Azure, vous pouvez supprimer l’ancien déploiement dans OMS.
* Toutes les fonctionnalités existantes dont vous disposiez dans OMS sont disponibles dans Azure. Pour plus d’informations sur Update Management, consultez [Vue d’ensemble d’Update Management](automation-update-management.md).

## <a name="access-the-azure-portal"></a>Accéder au portail Azure

À partir de votre espace de travail OMS, cliquez sur **Ouvrir dans Azure**. Vous accédez alors à l’espace de travail Log Analytics qui était utilisé par OMS.

![Ouvrir dans Azure - Portail OMS](media/migrate-oms-update-deployments/link-to-azure-portal.png)

Dans le portail Azure, cliquez sur **Compte Automation**.

![Journaux d’activité Azure Monitor](media/migrate-oms-update-deployments/log-analytics.png)

Dans votre compte Automation, cliquez sur **Update Management** pour ouvrir Update Management.

![Update Management](media/migrate-oms-update-deployments/azure-automation.png)

À l’avenir, vous pouvez accédez directement au portail Azure. Sous **Tous les services**, sélectionnez **Comptes Automation** sous **Outils de gestion**, sélectionnez le compte Automation approprié, puis cliquez sur **Update Management**.

## <a name="recreate-existing-deployments"></a>Recréer des déploiements existants

Tous les déploiements de mises à jour créés dans le portail OMS ont une [recherche enregistrée](../azure-monitor/platform/computer-groups.md), également appelée « groupe d’ordinateurs », avec le même nom que le déploiement de mises à jour existant. La recherche enregistrée contient la liste des ordinateurs pour lesquels le déploiement de mises à jour a été planifié.

![Update Management](media/migrate-oms-update-deployments/oms-deployment.png)

Pour utiliser cette recherche enregistrée existante, suivez ces étapes :

Pour créer un déploiement de mises à jour, accédez au portail Azure, sélectionnez le compte Automation qui est utilisé, puis cliquez sur **Update Management**. Cliquez sur **Planifier le déploiement de la mise à jour**.

![Planifier le déploiement de la mise à jour](media/migrate-oms-update-deployments/schedule-update-deployment.png)

Le volet **Nouveau déploiement de mises à jour** s’ouvre. Entrez les valeurs des propriétés décrites dans le tableau suivant, puis cliquez sur **Créer** :

Pour les ordinateurs à mettre à jour, sélectionnez la recherche enregistrée utilisée par le déploiement OMS existant.

| Propriété | Description |
| --- | --- |
|Name |Nom unique identifiant le déploiement de mises à jour. |
|Système d’exploitation| Sélectionnez **Linux** ou **Windows**.|
|Ordinateurs à mettre à jour |Sélectionnez une recherche enregistrée, un groupe importé ou choisissez un ordinateur dans la liste déroulante, puis sélectionnez des ordinateurs individuels. Si vous choisissez **Machines**, l’état de préparation de la machine est indiqué dans la colonne **PRÉPARATION À LA MISE À JOUR DE L’AGENT**.</br> Pour en savoir plus sur les différentes méthodes de création de groupes d’ordinateurs dans les journaux Azure Monitor, consultez [Groupes d’ordinateurs dans les journaux Azure Monitor](../azure-monitor/platform/computer-groups.md). |
|Classifications des mises à jour|Sélectionnez toutes les classifications des mises à jour dont vous avez besoin. CentOS ne prend pas directement en charge cette fonction.|
|Mises à jour à exclure|Entrez les mises à jour à exclure. Pour Windows, entrez la version KB sans le préfixe **KB**. Pour Linux, entrez le nom du package ou utilisez un caractère générique.  |
|Paramètres de planification|Sélectionnez l’heure de début, puis la périodicité (**Une fois** ou **Récurrent**). | 
| Fenêtre de maintenance |Nombre de minutes défini pour les mises à jour. La valeur ne peut pas être inférieure à 30 minutes ni supérieure à 6 heures. |
| Contrôle du redémarrage| Détermine la façon dont doivent être gérés les redémarrages.</br>Options disponibles :</br>Redémarrer si nécessaire (par défaut)</br>Toujours redémarrer</br>Ne jamais redémarrer</br>Redémarrer uniquement : les mises à jour ne sont pas installées|

Cliquez sur **Déploiements de mise à jour planifiés** pour voir l’état du déploiement de mises à jour nouvellement créé.

![Nouveau déploiement de mises à jour](media/migrate-oms-update-deployments/new-update-deployment.png)

Comme mentionné précédemment, une fois que vos nouveaux déploiements sont configurés via le portail Azure, les déploiements existants peuvent être supprimés du portail OMS.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Update Management dans Azure, consultez [Update Management](automation-update-management.md).
