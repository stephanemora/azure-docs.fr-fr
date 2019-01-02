---
title: Questions fréquentes sur Azure Monitor pour machines virtuelles (préversion) | Microsoft Docs
description: Azure Monitor pour machines virtuelles (préversion) est une solution dans Azure qui combine la surveillance de l’intégrité et des performances du système d’exploitation machine virtuelle Azure. Cette solution détecte automatiquement les composants d’application et les dépendances avec d’autres ressources, et mappe la communication entre les différents éléments. Cet article contient des réponses aux questions fréquemment posées.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2018
ms.author: magoedte
ms.openlocfilehash: a97a7be0eaa8438a4df27b610106ec6ab9f60d30
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184387"
---
# <a name="azure-monitor-for-vms-preview-faq"></a>FAQ Azure Monitor pour machines virtuelles (préversion)
Cette page répond aux questions fréquentes à propos d’Azure Monitor pour machines virtuelles. Si vous avez d’autres questions sur la solution, rendez-vous sur le [forum de discussion Azure](https://feedback.azure.com/forums/34192--general-feedback) et publiez vos questions. Lorsque des questions sont fréquemment posées, nous les ajoutons à cet article pour qu’elles soient faciles et rapides à trouver.

## <a name="can-i-deploy-vms-to-an-existing-workspace"></a>Puis-je déployer des machines virtuelles dans un espace de travail existant ?
Si vos machines virtuelles sont déjà connectées à un espace de travail Log Analytics, vous pouvez continuer à utiliser celui-ci lors du déploiement des machines virtuelles dans Azure Monitor pour machines virtuelles. L’espace de travail doit se trouver dans l’une des régions prises en charge répertoriées dans la section « Prérequis » de [Déployer Azure Monitor pour machines virtuelles (préversion)](vminsights-onboard.md#prerequisites).

Au cours du déploiement, nous configurons des compteurs de performance pour l’espace de travail. Par conséquent, les machines virtuelles qui transmettent des données à l’espace de travail commencent à collecter les informations relatives à l’affichage et à l’analyse dans Azure Monitor pour machines virtuelles. En conséquence, les données de performance de toutes les machines virtuelles connectées à l’espace de travail sélectionné s’affichent. Les fonctionnalités Intégrité (Health) et Mappage (Map) ne sont activées que pour les machines virtuelles que vous avez choisi de déployer.

Pour plus d’informations sur les compteurs de performances qui sont activés, consultez [Déployer Azure Monitor pour machines virtuelles (préversion)](vminsights-onboard.md).

## <a name="can-i-deploy-vms-to-a-new-workspace"></a>Puis-je déployer des machines virtuelles dans un nouvel espace de travail ? 
Si vos machines virtuelles ne sont pas actuellement connectées à un espace de travail Log Analytics existant, vous devez créer un nouvel espace de travail pour stocker vos données. Vous pouvez en créer un automatiquement en configurant une seule machine virtuelle pour Azure Monitor pour machines virtuelles dans le portail Azure.

Si vous choisissez d’utiliser la méthode basée sur un script, consultez [Déployer Azure Monitor pour machines virtuelles (préversion)](vminsights-onboard.md). 

## <a name="what-can-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Que puis-je faire si ma machine virtuelle envoie déjà des rapports à un espace de travail existant ?
Si vous collectez déjà des données à partir de vos machines virtuelles, il se peut que vous l’ayez déjà configuré de façon à ce que les rapports des données soient transmis à un espace de travail Log Analytics existant. Si cet espace de travail se trouve dans l’une des régions que nous prenons en charge, vous pouvez y activer Azure Monitor pour machines virtuelles. Nous mettons tout en œuvre pour prendre en charge de nouvelles régions.

>[!NOTE]
>Nous configurons les compteurs de performance de l’espace de travail qui concerne toutes les machines virtuelles liées à l’espace de travail, que vous ayez choisi ou non de les déployer dans Azure Monitor pour machines virtuelles. Pour plus d’informations sur la façon dont les compteurs de performance sont configurés pour l’espace de travail, consultez la section « Configuration des compteurs de performances » dans [Sources des données de performances Windows et Linux dans Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md). Pour en savoir plus sur les compteurs configurés d’Azure Monitor pour machines virtuelles, consultez notre [Déployer Azure Monitor pour machines virtuelles (préversion)](vminsights-onboard.md). 

## <a name="why-did-my-vm-deployment-fail"></a>Pourquoi mon déploiement de machine virtuelle a-t-il échoué ?
Voici les événements qui se produisent lors du déploiement d’une machine virtuelle Azure depuis le portail Azure :

* Un espace de travail Log Analytics par défaut est créé, si cette option a été sélectionnée.
* Les compteurs de performance sont configurés pour l’espace de travail sélectionné. Si cette étape échoue, certains graphiques et tableaux de performances n’affichent pas les données de la machine virtuelle que vous avez déployée. Vous pouvez résoudre ce problème en exécutant le script PowerShell qui est documenté dans la section « Activer avec PowerShell » dans [Déployer Azure Monitor pour machines virtuelles (préversion)](vminsights-onboard.md#enable-with-powershell).
* L’agent Log Analytics est installé sur les machines virtuelles Azure à l’aide d’une extension machine virtuelle, au besoin. 
* L’agent Map Dependency d’Azure Monitor pour machines virtuelles est installé sur les machines virtuelles Azure à l’aide d’une extension, au besoin. 
* Les composants d’Azure Monitor prenant en charge la fonctionnalité Intégrité (Health) sont configurés, le cas échéant, et la machine virtuelle est configurée de façon à transmettre les rapports sur les données d’intégrité.

Lors du déploiement, nous vérifions l’état de chacune des étapes précédentes et vous retournons un état de notification dans le portail. La configuration de l’espace de travail et l’installation de l’agent prennent généralement de 5 à 10 minutes. L’affichage des données de supervision et d’intégrité sur le portail Azure prend de 5 à 10 minutes supplémentaires. 

Si vous avez commencé le déploiement et que des messages indiquant que la machine virtuelle doit être déployée s’affichent, attendez jusqu’à 30 minutes pour que la machine virtuelle puisse terminer le processus. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Aucune donnée ne figure dans les tableaux de performances de ma machine virtuelle, ou certaines ne s’affichent pas
Si les données de performance ne s’affichent pas dans le tableau du disque ou dans les graphiques de performance, vos compteurs de performance ne sont peut-être pas configurés dans l’espace de travail. Pour résoudre ce problème, exécutez le script PowerShell qui est documenté dans la section « Activer avec PowerShell » dans [Déployer Azure Monitor pour machines virtuelles (préversion)](vminsights-onboard.md#enable-with-powershell).

## <a name="how-is-the-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Quelle est la différence entre la fonctionnalité de mappage d’Azure Monitor pour machines virtuelles et celle de Service Map ?
La fonctionnalité de mappage d’Azure Monitor pour machines virtuelles est basée sur celle de Service Map, mais présente les différences suivantes :

* La fenêtre Mappage (Map) est accessible depuis le volet de la machine virtuelle et depuis Azure Monitor pour machines virtuelles sous Azure Monitor.
* Il est maintenant possible de cliquer sur les connexions dans la fenêtre Mappage pour afficher les données métriques de connexion dans le panneau latéral.
* Une nouvelle API permet de créer les mappages afin de mieux prendre en charge les plus complexes d’entre eux.
* Les machines virtuelles supervisées font maintenant partie du nœud du groupe de clients, et le graphique en secteurs montre la répartition entre les machines virtuelles supervisées et non supervisées. Vous pouvez également filtrer la liste des machines lorsque le groupe est développé.
* Les machines virtuelles supervisées font maintenant partie des nœuds du groupe de ports de serveur, et le graphique en secteurs montre la répartition entre les machines supervisées et non supervisées. Vous pouvez également filtrer la liste des machines lorsque le groupe est développé.
* Le style de mappage a été mis à jour pour assurer une meilleure cohérence avec la Mise en correspondance d'applications dans Azure Application Insights.
* Les panneaux latéraux ont été mis à jour mais n’ont pas encore l’ensemble complet des intégrations qui étaient prises en charge dans Service Map : Update Management, Change Tracking, Security et Service Desk. 
* L’option permettant de choisir des groupes et des machines à mapper a été mise à jour. Elle prend désormais en charge Abonnements, Groupes de ressources, Groupes de machines virtuelles identiques Azure et Services cloud.
* Vous ne pouvez pas créer de nouveaux groupes de machines Service Map dans la fonctionnalité de mappage d’Azure Monitor pour machines virtuelles. 

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>Pourquoi des lignes pointillées apparaissent sur mes graphiques de performances ?

Les graphiques de performances affichent des lignes en pointillés au lieu de lignes pleines pour deux raisons :
* Il peut y avoir un écart dans la collecte de données. 

* Le paramètre par défaut pour l’échantillonnage des données est toutes les 60 secondes. Vous pouvez voir des lignes en pointillés si vous choisissez un intervalle de temps court pour le graphique et que votre fréquence d’échantillonnage est inférieure à la taille du compartiment utilisée dans le graphique. Supposons que vous avez choisi une fréquence d’échantillonnage de 10 minutes et que chaque compartiment dans le graphique est de 5 minutes. Si vous choisissez un plus grand intervalle de temps pour la visualisation, les lignes du graphique devraient apparaître sous forme de lignes pleines plutôt que de points dans ce cas.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Les groupes sont-ils pris en charge avec Azure Monitor pour machines virtuelles ?
Oui, une fois que vous installez Dependency Agent, nous collectons des informations depuis les machines virtuelles pour afficher les groupes en fonction de l’abonnement, du groupe de ressources, des groupes de machines virtuelles identiques et des services cloud. Si vous utilisez Service Map et que vous avez créé des groupes d’ordinateurs, ceux-ci sont également affichés. Les groupes d’ordinateurs apparaissent également dans le filtre de groupes si vous les avez créés pour l’espace de travail que vous affichez. 

## <a name="how-can-i-display-the-details-about-whats-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Comment afficher les détails de ce qui détermine la ligne du 95e percentile dans les graphiques de performance globale ?
Par défaut, la liste est triée pour vous montrer les machines virtuelles qui ont la valeur la plus élevée du 95e centile pour la métrique sélectionnée. Le graphique **Mémoire disponible** qui affiche les machines avec la valeur la plus basse du 5e centile constitue une exception. Sélectionnez le graphique pour ouvrir la fenêtre **Top N List** avec la métrique appropriée sélectionnée.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-various-virtual-networks-and-subnets"></a>Comment la fonctionnalité Map (Mappage) gère-t-elle les adresses IP dupliquées sur différents réseaux virtuels et sous-réseaux ?
Si vous dupliquez des plages d’adresses IP avec des machines virtuelles ou des groupes d’échelles de machines virtuelles identiques Azure sur des sous-réseaux et des réseaux virtuels, il se peut que la fonctionnalité de mappage d’Azure Monitor pour machines virtuelles affiche des informations erronées à la place. Il s’agit d’un problème connu et nous étudions des options pour offrir une meilleure expérience.

## <a name="does-the-map-feature-support-ipv6"></a>La fonctionnalité de mappage prend-elle en charge le protocole IPv6 ?
La fonctionnalité de mappage ne prend actuellement en charge que le protocole IPv4, mais nous étudions actuellement la prise en charge du protocole IPv6. Nous prenons également en charge le protocole IPv4 tunnelisé dans le protocole IPv6.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-why-is-the-map-difficult-to-view"></a>Lorsque je charge un mappage pour un groupe de ressources ou un autre grand groupe, pourquoi celui-ci est-il difficile à visualiser ?
Bien que nous ayons apporté des améliorations à la fonctionnalité de mappage pour la gestion de configurations grandes et complexes, nous savons qu’il peut comporter un grand nombre de nœuds, de connexions et de nœuds fonctionnant en cluster. Nous continuons d’accroître notre soutien afin de renforcer la mise à l’échelle.  

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-from-the-network-chart-on-the-azure-vm-overview-page"></a>Pourquoi le graphique du réseau dans l’onglet Performance est-il différent de celui de la page d’aperçu de la machine virtuelle Azure ?

La page d’aperçu d’une machine virtuelle Azure affiche des graphiques basés sur la mesure de l’activité de l’hôte dans la machine virtuelle hôte. Dans le cas du graphique du réseau sur la page d’aperçu de la machine virtuelle Azure, seul le trafic réseau qui sera facturé est affiché. Cet affichage n’inclut pas le trafic entre les réseaux virtuels. Les données et les graphiques présentés dans Azure Monitor pour machines virtuelles sont basés sur les données de la machine virtuelle invitée et le graphique du réseau affiche tout le trafic TCP/IP entrant et sortant vers cette machine virtuelle, y compris le trafic entre réseaux virtuels.

## <a name="what-are-the-limitations-of-the-log-analytics-free-pricing-plan"></a>Quelles sont les limites du plan tarifaire Gratuit de Log Analytics ?
Si vous avez configuré Azure Monitor avec un espace de travail Log Analytics en utilisant le niveau tarifaire *Gratuit*, la fonctionnalité de mappage d'Azure Monitor pour machines virtuelles ne prend en charge que cinq machines connectées à l'espace de travail. 

Par exemple, supposons que vous avez cinq machines virtuelles connectées à un espace de travail gratuit. Si vous déconnectez une machine virtuelle et en connectez une autre ultérieurement, la nouvelle machine virtuelle n’est pas surveillée, ni reflétée sur la page du mappage. Dans ce cas, l'option **Essayer maintenant** s’affiche lorsque vous ouvrez la nouvelle machine virtuelle et sélectionnez **Insights (préversion)** dans le volet de gauche, même après son installation sur la machine virtuelle. Toutefois, vous n’êtes pas invité à choisir autant d’options que ce serait normalement le cas si la machine virtuelle n’était pas déployée dans Azure Monitor pour machines virtuelles. 

## <a name="next-steps"></a>Étapes suivantes
Pour découvrir les exigences et les méthodes relatives à l’activation de la supervision de vos machines virtuelles, consultez [Déployer Azure Monitor pour machines virtuelles](vminsights-onboard.md).
