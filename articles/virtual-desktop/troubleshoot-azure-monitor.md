---
title: Résolution des problèmes liés à Monitor pour Windows Virtual Desktop (préversion) – Azure
description: Guide pratique pour résoudre les problèmes liés à Azure Monitor pour Windows Virtual Desktop.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c335c1cf7e5319b812345714dbdc6b87ddc4e81b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709170"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop-preview"></a>Résolution des problèmes liés à Azure Monitor pour Windows Virtual Desktop (préversion)

>[!IMPORTANT]
>Azure Monitor pour Windows Virtual Desktop est en préversion publique. Cette préversion est fournie sans contrat de niveau de service, c’est pourquoi nous déconseillons son utilisation pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article présente les problèmes connus dans Azure Monitor pour Windows Virtual Desktop (préversion) et les solutions aux problèmes courants.

## <a name="issues-with-configuration-and-setup"></a>Problèmes de configuration

Si le classeur de configuration ne fonctionne pas correctement pour automatiser la configuration, vous pouvez utiliser les ressources suivantes pour configurer votre environnement manuellement :

- Pour activer manuellement les diagnostics ou accéder à l’espace de travail Log Analytics, consultez [Envoi de diagnostics Windows Virtual Desktop à Log Analytics](diagnostics-log-analytics.md).
- Pour installer manuellement l’extension Log Analytics sur un hôte, consultez [Extension de machine virtuelle Log Analytics pour Windows](../virtual-machines/extensions/oms-windows.md).
- Pour configurer un nouvel espace de travail Log Analytics, consultez [Création d’un espace de travail Log Analytics sur le Portail Azure](../azure-monitor/logs/quick-create-workspace.md).
- Pour ajouter ou supprimer des compteurs de performances, consultez [Configuration des compteurs de performances](../azure-monitor/agents/data-sources-performance-counters.md).
- Pour configurer des événements pour un espace de travail Log Analytics, consultez [Collecte de sources de données du journal des événements Windows avec l’agent Log Analytics](../azure-monitor/agents/data-sources-windows-events.md).

## <a name="my-data-isnt-displaying-properly"></a>Mes données ne s’affichent pas correctement

Si vos données ne s'affichent pas correctement, vérifiez votre configuration, vos autorisations et assurez-vous que les adresses IP requises sont débloquées. 

- Tout d’abord, vérifiez que vous avez rempli tous les champs du classeur de configuration (cf. [Monitoring d’un déploiement avec Azure Monitor pour Windows Virtual Desktop](azure-monitor.md)). S’il vous manque des compteurs ou des événements, les données associées n’apparaissent pas sur le Portail Azure.

- Vérifiez vos autorisations d'accès et contactez les propriétaires des ressources pour leur demander les autorisations manquantes ; toute personne surveillant Windows Virtual Desktop doit disposer des autorisations suivantes :

    - Accès en lecture aux abonnements Azure qui contiennent vos ressources Windows Virtual Desktop
    - Accès en lecture aux groupes de ressources de l'abonnement qui contiennent vos hôtes de session Windows Virtual Desktop 
    - Accès en lecture à l’espace de travail Log Analytics

- Vous devrez peut-être ouvrir les ports sortants dans le pare-feu de votre serveur pour autoriser Azure Monitor à envoyer des données au portail ; voir [Ports sortants](../azure-monitor/app/ip-addresses.md). 

- Vous ne voyez pas les données relatives aux activités récentes ? Patientez 15 minutes et actualisez le flux. Azure Monitor présente une période de latence de 15 minutes pour le remplissage des données de journal. Pour plus d’informations, consultez [Durée d’ingestion des données de journal dans Azure Monitor](../azure-monitor/logs/data-ingestion-time.md).

S’il ne manque aucune information, mais que les données ne s’affichent toujours pas correctement, il peut y avoir un problème dans la requête ou les sources de données. Passez en revue nos problèmes connus et limitations. 

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Je veux personnaliser Azure Monitor pour Windows Virtual Desktop

Azure Monitor pour Windows Virtual Desktop utilise des classeurs Azure Monitor. Les classeurs vous permettent d’enregistrer une copie du modèle de classeur Windows Virtual Desktop et d’effectuer vos propres personnalisations.

Par défaut, les modèles de classeur personnalisés n'adoptent pas automatiquement les mises à jour du groupe de produits. Pour plus d’informations, consultez [Résolution des problèmes liés aux insights issus des classeurs](../azure-monitor/insights/troubleshoot-workbooks.md) et [Vue d’ensemble des classeurs](../azure-monitor/visualize/workbooks-overview.md).

## <a name="i-cant-interpret-the-data"></a>Je ne parviens pas à interpréter les données

Pour plus d’informations sur les termes liés aux données, consultez le [glossaire Azure Monitor pour Windows Virtual Desktop](azure-monitor-glossary.md).

## <a name="the-data-i-need-isnt-available"></a>Les données dont j’ai besoin ne sont pas disponibles

Si vous souhaitez surveiller d'autres compteurs de performances ou événements, vous pouvez les envoyer à votre espace de travail Log Analytics et les surveiller dans Diagnostics de l'ordinateur hôte : navigateur hôte. 

- Pour ajouter des compteurs de performances, consultez [Configuration des compteurs de performances](../azure-monitor/agents/data-sources-performance-counters.md#configuring-performance-counters).
- Pour ajouter des événements Windows, consultez [Configuration des journaux des événements Windows](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs).

Il vous manque un point de données pour diagnostiquer un problème ? Envoyez-nous vos commentaires.

- Pour savoir comment laisser des commentaires, consultez [Vue d’ensemble de la résolution des problèmes, des commentaires et du support pour Windows Virtual Desktop](troubleshoot-set-up-overview.md).
- Vous pouvez également laisser vos commentaires sur Windows Virtual Desktop sur le [Hub de commentaires Windows Virtual Desktop](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) ou notre [forum UserVoice](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

## <a name="known-issues-and-limitations"></a>Problèmes connus et limitations

Voici les problèmes et limitations dont nous avons actuellement connaissance et que nous nous efforçons de résoudre :

- Vous ne pouvez surveiller qu'un seul pool d'hôtes à la fois. 

- Pour enregistrer vos paramètres favoris, vous devez enregistrer un modèle personnalisé du classeur. Les modèles personnalisés n'adoptent pas automatiquement les mises à jour du groupe de produits.

- Certains messages d’erreur ne sont pas formulés de manière conviviale. Par ailleurs, tous ne sont pas décrits dans la documentation.

- Le compteur de performances du nombre total de sessions peut légèrement dépasser le nombre de sessions réel, et le nombre total de vos sessions peut sembler dépasser votre limite maximale de sessions.

- Le nombre de sessions disponibles ne reflète pas les stratégies de mise à l'échelle sur le pool d'hôtes. 
    
- Bien que cela soit rare, l'événement d'achèvement d'une connexion peut disparaître, ce qui peut avoir un impact sur certains objets visuels comme les connexions au fil du temps et l'état de la connexion de l'utilisateur.  
    
- Le classeur de configuration prend uniquement en charge la configuration d'hôtes situés dans la même région que leur groupe de ressources. 

- Le « temps pour se connecter » englobe le temps nécessaire aux utilisateurs pour entrer leurs informations d'identification ; ce temps est corrélé à l'expérience mais peut, dans certains cas, afficher de faux pics. 
    

## <a name="next-steps"></a>Étapes suivantes

Si vous ne savez pas comment interpréter les données ou souhaitez en savoir plus sur les termes courants, consultez le [glossaire Azure Monitor pour Windows Virtual Desktop](azure-monitor-glossary.md). Si vous voulez apprendre à configurer et à utiliser Azure Monitor pour Windows Virtual Desktop, consultez [Monitoring d’un déploiement avec Azure Monitor pour Windows Virtual Desktop](azure-monitor.md).