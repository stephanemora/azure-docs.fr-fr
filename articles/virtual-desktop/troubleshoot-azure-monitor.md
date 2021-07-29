---
title: Résolution des problèmes liés à Monitor pour Azure Virtual Desktop – Azure
description: Guide pratique pour résoudre les problèmes liés à Azure Monitor pour Azure Virtual Desktop.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 27234cab8ebd6beefdd3766b3dca234ba5b41515
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111755354"
---
# <a name="troubleshoot-azure-monitor-for-azure-virtual-desktop"></a>Résolution des problèmes liés à Azure Monitor pour Azure Virtual Desktop

Cet article présente des problèmes connus dans Azure Monitor pour Azure Virtual Desktop, ainsi que des solutions à des problèmes courants.

## <a name="issues-with-configuration-and-setup"></a>Problèmes de configuration

Si le classeur de configuration ne fonctionne pas correctement pour automatiser la configuration, vous pouvez utiliser les ressources suivantes pour configurer votre environnement manuellement :

- Pour activer manuellement les diagnostics ou accéder à l’espace de travail Log Analytics, consultez [Envoi de diagnostics Azure Virtual Desktop à Log Analytics](diagnostics-log-analytics.md).
- Pour installer manuellement l’extension Log Analytics sur un hôte de session, consultez [Extension de machine virtuelle Log Analytics pour Windows](../virtual-machines/extensions/oms-windows.md).
- Pour configurer un nouvel espace de travail Log Analytics, consultez [Création d’un espace de travail Log Analytics sur le Portail Azure](../azure-monitor/logs/quick-create-workspace.md).
- Pour ajouter, supprimer ou modifier des compteurs de performances, consultez [Configuration des compteurs de performances](../azure-monitor/agents/data-sources-performance-counters.md).
- Pour configurer les journaux des événements Windows pour un espace de travail Log Analytics, consultez [Collecter les sources de données du journal des événements Windows avec l’agent Log Analytics](../azure-monitor/agents/data-sources-windows-events.md).

## <a name="my-data-isnt-displaying-properly"></a>Mes données ne s’affichent pas correctement

Si vos données ne s’affichent pas correctement, vérifiez les solutions courantes suivantes :

- Tout d’abord, vérifiez que vous avez correctement préparé le classeur de configuration décrit dans [Monitoring d’un déploiement avec Azure Monitor pour Azure Virtual Desktop](azure-monitor.md). S’il vous manque des compteurs ou des événements, les données associées n’apparaissent pas sur le Portail Azure.
- Vérifiez vos autorisations d'accès et contactez les propriétaires des ressources pour leur demander les autorisations manquantes ; toute personne surveillant Azure Virtual Desktop doit disposer des autorisations suivantes :
    - Accès en lecture aux abonnements Azure qui contiennent vos ressources Azure Virtual Desktop
    - Accès en lecture aux groupes de ressources de l'abonnement qui contiennent vos hôtes de session Azure Virtual Desktop 
    - Accès en lecture à tout espace de travail Log Analytics que vous utilisez
- Il se peut que vous deviez ouvrir des ports sortants dans le pare-feu de votre serveur pour permettre à Azure Monitor et à Log Analytics d’envoyer des données au portail. Pour savoir comment procéder, consultez les articles suivants :
      - [Ports sortants Azure Monitor](../azure-monitor/app/ip-addresses.md)
      - [Configuration requise du pare-feu Log Analytics](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements). 
- Vous ne voyez pas les données relatives aux activités récentes ? Patientez 15 minutes et actualisez le flux. Azure Monitor présente une période de latence de 15 minutes pour le remplissage des données de journal. Pour plus d’informations, consultez [Durée d’ingestion des données de journal dans Azure Monitor](../azure-monitor/logs/data-ingestion-time.md).

S’il ne manque aucune information, mais que les données ne s’affichent toujours pas correctement, il peut y avoir un problème dans la requête ou les sources de données. Consultez les [Problèmes connus et les limitations](#known-issues-and-limitations). 

## <a name="i-want-to-customize-azure-monitor-for-azure-virtual-desktop"></a>Je veux personnaliser Azure Monitor pour Azure Virtual Desktop

Azure Monitor pour Azure Virtual Desktop utilise des classeurs Azure Monitor. Les classeurs vous permettent d’enregistrer une copie du modèle de classeur Azure Virtual Desktop et d’effectuer vos propres personnalisations.

Par défaut, les modèles de classeur personnalisés n'adoptent pas automatiquement les mises à jour du groupe de produits. Pour plus d’informations, consultez [Résolution des problèmes liés aux insights issus des classeurs](../azure-monitor/insights/troubleshoot-workbooks.md) et [Vue d’ensemble des classeurs](../azure-monitor/visualize/workbooks-overview.md).

## <a name="i-cant-interpret-the-data"></a>Je ne parviens pas à interpréter les données

Pour plus d’informations sur les termes liés aux données, consultez le [glossaire Azure Monitor pour Windows Virtual Desktop](azure-monitor-glossary.md).

## <a name="the-data-i-need-isnt-available"></a>Les données dont j’ai besoin ne sont pas disponibles

Si vous souhaitez surveiller d’autres compteurs de performances ou journaux des événements Windows, vous pouvez les activer pour qu’ils envoient des informations de diagnostic à votre espace de travail Log Analytics, et les surveiller dans **Diagnostics de l’ordinateur hôte : navigateur hôte**. 

- Pour ajouter des compteurs de performances, consultez [Configuration des compteurs de performances](../azure-monitor/agents/data-sources-performance-counters.md#configuring-performance-counters)
- Pour ajouter des événements Windows, consultez [Configuration des journaux des événements Windows](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)

Il vous manque un point de données pour diagnostiquer un problème ? Envoyez-nous vos commentaires.

- Pour savoir comment laisser des commentaires, consultez [Vue d’ensemble de la résolution des problèmes, des commentaires et du support pour Azure Virtual Desktop](troubleshoot-set-up-overview.md).
- Vous pouvez également formuler vos commentaires concernant Azure Virtual Desktop sur le [Hub de commentaires Azure Virtual Desktop](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

## <a name="known-issues-and-limitations"></a>Problèmes connus et limitations

Voici les problèmes et limitations dont nous avons actuellement connaissance et que nous nous efforçons de corriger :

- Vous ne pouvez surveiller qu'un seul pool d'hôtes à la fois. 
- Pour enregistrer vos paramètres favoris, vous devez enregistrer un modèle personnalisé du classeur. Les modèles personnalisés n'adoptent pas automatiquement les mises à jour du groupe de produits.
- Le classeur de configuration indique parfois des erreurs « Échec de la requête » lors du chargement de vos sélections. Actualisez la requête et entrez à nouveau votre sélection si nécessaire. L’erreur devrait se résoudre d’elle-même. 
- Certains messages d’erreur ne sont pas formulés de manière conviviale, et tous ne sont pas décrits dans la documentation.
- Le compteur de performances du nombre total de sessions peut légèrement dépasser le nombre de sessions réel, et le nombre total de vos sessions peut sembler dépasser votre limite maximale de sessions.
- Le nombre de sessions disponibles ne reflète pas les stratégies de mise à l'échelle sur le pool d'hôtes.   
- Voyez-vous des temps de connexion contradictoires ou inattendus ? Bien que cela soit rare, l’événement de fin d’une connexion peut disparaître, ce qui peut avoir un impact sur certaines vues et métriques.
- Le « temps pour se connecter » englobe le temps nécessaire aux utilisateurs pour entrer leurs informations d'identification ; ce temps est corrélé à l'expérience mais peut, dans certains cas, afficher de faux pics. 
    

## <a name="next-steps"></a>Étapes suivantes

- Pour commencer, consultez [Monitoring d’un déploiement avec Azure Monitor pour Azure Virtual Desktop](azure-monitor.md).
- Pour estimer, mesurer et gérer les coûts de stockage de vos données, consultez [Estimer les coûts d’Azure Monitor](azure-monitor-costs.md).
- Consultez notre [glossaire](azure-monitor-glossary.md) pour en savoir plus sur les termes et les concepts liés à Azure Monitor pour Azure Virtual Desktop.
