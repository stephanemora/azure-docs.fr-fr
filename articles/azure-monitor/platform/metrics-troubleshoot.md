---
title: Dépannage des graphiques de métrique Azure Monitor
description: Résoudre les problèmes liés à la création, la personnalisation ou l’interprétation des graphiques de métriques
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: cff1bbefc3c54f7f9c02c646bd9eef528fe28c73
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939570"
---
# <a name="troubleshooting-metrics-charts"></a>Dépannage des graphiques de métriques

Utilisez cet article si vous rencontrez des problèmes avec la création, la personnalisation ou l’interprétation des graphiques dans l’Explorateur de mesures Azure. Si vous ne connaissez pas les mesures, en savoir plus sur [mise en route avec l’Explorateur de mesures](metrics-getting-started.md) et [les fonctionnalités avancées d’Explorateur de mesures](metrics-charts.md). Vous pouvez également voir [exemples](metric-chart-samples.md) des graphiques de métrique configurés.

## <a name="cant-find-your-resource-to-select-it"></a>Impossible de trouver votre ressource pour le sélectionner

Vous avez cliqué sur le **sélectionner une ressource** bouton, mais ne voyez pas votre ressource dans la boîte de dialogue de sélecteur de ressources.

**Solution :** Explorateur de mesures, vous devez sélectionner les abonnements et les groupes de ressources avant de la liste des ressources disponibles. Si vous ne voyez pas votre ressource :

1. Vérifiez que vous avez sélectionné l’abonnement approprié dans le **abonnement** liste déroulante. Si votre abonnement n’est pas répertorié, cliquez sur le **répertoire + abonnement paramètres** et ajouter un abonnement avec votre ressource.

1. Vérifiez que vous avez sélectionné le groupe de ressources approprié.
    > [!WARNING]
    > Pour de meilleures performances, lorsque vous ouvrez l’Explorateur de mesures, les **groupe de ressources** liste déroulante n’a aucun groupe de ressources sélectionné au préalable. Vous devez choisir au moins un groupe avant de pouvoir voir toutes les ressources.

## <a name="chart-shows-no-data"></a>Graphique n’affiche aucune donnée

Parfois, les graphiques ne peuvent afficher aucune donnée après avoir sélectionné les métriques et les ressources appropriées. Ce comportement peut être provoqué par plusieurs des raisons suivantes :

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>Fournisseur de ressources Microsoft.Insights n’est pas inscrit pour votre abonnement

Exploration des mesures nécessite *Microsoft.Insights* fournisseur de ressources inscrit dans votre abonnement. Dans de nombreux cas, il est automatiquement inscrit (autrement dit, une fois que vous configurez une règle d’alerte, personnalisez les paramètres de diagnostic pour n’importe quelle ressource ou configurez une règle de mise à l’échelle). Si le fournisseur de ressources Microsoft.Insights n’est pas inscrit, vous devez l’inscrire manuellement en suivant les étapes décrites dans [fournisseurs de ressources Azure et les types](../../azure-resource-manager/resource-manager-supported-services.md).

**Solution :** Ouvrez **abonnements**, **fournisseurs de ressources** onglet et vérifiez que *Microsoft.Insights* est inscrit pour votre abonnement.

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>Vous n’avez pas les droits d’accès suffisants à votre ressource

Dans Azure, l’accès aux métriques est contrôlé par [contrôle d’accès en fonction du rôle (RBAC)](../../role-based-access-control/overview.md). Vous devez être membre du [lecteur de surveillance](../../role-based-access-control/built-in-roles.md#monitoring-reader), [contributeur de surveillance](../../role-based-access-control/built-in-roles.md#monitoring-contributor), ou [contributeur](../../role-based-access-control/built-in-roles.md#contributor) pour Explorer les métriques pour n’importe quelle ressource.

**Solution :** Assurez-vous que vous disposez des autorisations suffisantes pour la ressource à partir de laquelle vous exploration des mesures.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>Votre ressource n’émettre des mesures pendant la période sélectionnée

Certaines ressources ne pas émettre constamment leurs mesures. Par exemple, Azure ne collectera pas les métriques pour les ordinateurs virtuels arrêtés. Autres ressources peuvent émettre leurs mesures uniquement lorsque certaines conditions se produit. Par exemple, une métrique montrant le temps de traitement d’une transaction requiert au moins une transaction. Si elle n’existait aucuns transactions dans la plage horaire sélectionnée, le graphique naturellement sera vide. En outre, si la plupart des mesures dans Azure est collectée toutes les minutes, il existe certaines qui sont collectés moins fréquemment. Consultez la documentation de métriques pour obtenir plus d’informations sur les mesures que vous tentez d’Explorer.

**Solution :** Modifiez l’heure du graphique à un éventail plus large. Vous pouvez démarrer à partir de « 30 derniers jours » à l’aide d’une plus grande granularité de temps (ou partie de confiance sur l’option « granularité heure automatique »).

### <a name="you-picked-a-time-range-greater-than-30-days"></a>Vous avez choisi un intervalle de temps supérieur à 30 jours

[La plupart des métriques dans Azure sont stockées pour 93 jours](data-platform-metrics.md#retention-of-metrics). Toutefois, vous pouvez interroger uniquement pour pas plus de 30 jours de données sur un graphique. Cette limitation ne s’applique pas à [métriques basé sur journal](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

**Solution :** Si vous voyez un graphique vide ou si votre graphique affiche uniquement la partie de données de mesure, vérifiez que l’intervalle de 30 jours ne dépasse pas la différence entre start - et -dates de fin dans le sélecteur d’heure.

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>Toutes les valeurs de mesure ont été en dehors de la plage de l’axe des y verrouillé

Par [les limites de l’axe y du graphique de verrouillage](metrics-charts.md#lock-boundaries-of-chart-y-axis), vous pouvez involontairement rendre la zone d’affichage graphique n’affiche ne pas la ligne de graphique. Par exemple, si l’axe des ordonnées sont verrouillée sur une plage comprise entre 0 et 50 %, et la mesure a une valeur constante de 100 %, la ligne est toujours rendue en dehors de la zone visible, rendre le graphique apparaît vide.

**Solution :** Vérifiez que les limites de l’axe des y du graphique ne sont pas verrouillés en dehors de la plage de valeurs métriques. Si les limites de l’axe des y sont verrouillés, vous souhaiterez temporairement les réinitialiser pour vous assurer que les mesures de valeurs ne se situent en dehors de la plage du graphique. La plage de l’axe des y le verrouillage n’est pas recommandée avec une granularité automatique pour les graphiques avec **somme**, **min**, et **max** agrégation, car leurs valeurs changent avec granularité en redimensionnant la fenêtre de navigateur ou en allant de la résolution d’un écran à un autre. Granularité de commutation peut laisser la zone d’affichage de votre vide de graphique.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>Vous examinez une métrique de système d’exploitation invité mais que vous n’avez pas activé l’Extension de Diagnostic Azure

Collection de **système d’exploitation invité** métriques nécessite la configuration de l’Extension de Diagnostics Azure ou l’activation à l’aide de la **les paramètres de Diagnostic** Panneau de configuration pour votre ressource.

**Solution :** Si l’Extension Azure Diagnostics est activée, mais vous ne parvenez pas à voir vos mesures, suivez les étapes décrites dans [guide de dépannage de l’Extension Azure Diagnostics](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal). Consultez également les étapes de dépannage pour [ne peut pas choisir espace de noms de système d’exploitation invité et métriques](metrics-troubleshoot.md#cannot-pick-guest-os-namespace-and-metrics)

## <a name="error-retrieving-data-message-on-dashboard"></a>Message « Erreur de récupération des données » sur le tableau de bord

Ce problème est courant lorsque votre tableau de bord a été créé avec une mesure qui a été plus tard déconseillée et supprimée à partir d’Azure. Pour vérifier que c’est le cas, ouvrez le **métriques** onglet de votre ressource et sélectionner les métriques disponibles dans le sélecteur de métriques. Si la métrique n’est pas affichée, la mesure a été supprimée à partir d’Azure. En règle générale, quand une métrique est dépréciée, il est une mesure de mieux nouvelle qui offre une perspective similaire sur l’intégrité des ressources.

**Solution :** Mettre à jour la vignette de l’échec en choisissant une autre métrique pour votre graphique sur le tableau de bord. Vous pouvez [consulter une liste des métriques disponibles pour les services Azure](metrics-supported.md).

## <a name="chart-shows-dashed-line"></a>Graphique affiche la ligne en pointillés

Graphiques de métriques Azure utilisent le style de ligne en pointillés pour indiquer qu’il existe une valeur manquante (également appelé « valeur null ») entre fragment de temps connu deux points de données. Par exemple, si dans le sélecteur d’heure que vous avez choisi la granularité temporelle de « 1 minute », mais la mesure a été signalée à 07:26, 07:27, 07:29 et 07 h 30 (Notez un intervalle de minutes entre les deuxième et troisième points de données), puis une ligne en pointillés se connectera 07:27 et 07:29 et une ligne pleine se connecteront tous les autres points de données. Les suppressions de ligne en pointillés à zéro lorsque la métrique utilise **nombre** et **somme** agrégation. Pour le **avg**, **min** ou **max** agrégations, la ligne en pointillés connecte deux points de données connues le plus proche. En outre, lorsque les données sont manquantes sur le côté le plus à droite ou à l’extrême gauche du graphique, la ligne en pointillés se développe vers la direction du point de données manquant.
  ![image de métrique](./media/metrics-troubleshoot/missing-data-point-line-chart.png)

**Solution :** Ce comportement est normal. Il est utile pour identifier les points de données manquants. Le graphique en courbes constitue un choix supérieur permettant de visualiser les tendances des métriques à haute densité, mais peut-être être difficile à interpréter pour les métriques avec des valeurs éparses, en particulier lorsque corelating valeurs avec le fragment de temps est important. La ligne en pointillés facilite la lecture de ces graphiques, mais si votre graphique n’est pas toujours clair, visionnez vos mesures avec un autre type de graphique. Par exemple, un graphique de dispersion pour la même mesure montre clairement chaque fragment de temps par seulement la visualisation d’un point lorsqu’il existe une valeur et ignorer les données point complètement lorsque la valeur est manquante : ![image de métrique](./media/metrics-troubleshoot/missing-data-point-scatter-chart.png)

   > [!NOTE]
   > Toutefois, si vous préférez un graphique en courbes pour votre métrique, déplacement de la souris sur le graphique peut aider à évaluer la granularité de temps en mettant en surbrillance le point de données à l’emplacement du pointeur de la souris.

## <a name="chart-shows-unexpected-drop-in-values"></a>Graphique affiche une chute dans les valeurs

Dans de nombreux cas, la liste déroulante perceptible dans les valeurs de métriques est méconnaissance des relations entre les données affichées sur le graphique. Vous pouvez peut prêter à confusion par une baisse de sommes ou compte lorsque le graphique affiche les minutes de la plus récente, car les derniers points de données métriques n’ont pas été reçus ou encore traitées par Azure. Selon le service, la latence de métriques de traitement peut être dans la plage de quelques minutes. Pour les graphiques affichant une période récente avec une granularité de 1 ou 5 minutes, une baisse de la valeur sur les dernières minutes devient plus évident : ![image de métrique](./media/metrics-troubleshoot/drop-in-values.png)

**Solution :** Ce comportement est normal. Nous pensons que l’affichage des données dès que nous recevons il est avantageux même lorsque les données sont *partielle* ou *incomplète*. Ainsi, vous permettent de rendre importante conclusion plus tôt et démarrer l’examen tout de suite. Par exemple, pour une mesure qui affiche le nombre d’échecs, voir une valeur partielle X vous indique qu’il y avait au moins X échecs sur une minute donnée. Vous pouvez démarrer immédiatement des informations sur le problème, au lieu d’attendre à voir le nombre exact d’échecs qui se sont produites dans cette minute, ce qui n’est peut-être pas aussi importante. Le graphique met à jour une fois que nous recevons l’ensemble des données, mais à ce moment-là peut également afficher nouveaux points de données incomplète de minutes plus récentes.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>Ne peut pas choisir des métriques et espace de noms de système d’exploitation invité

Machines virtuelles et les machines virtuelles identiques ont deux catégories de mesures : **Hôte d’ordinateur virtuel** métriques sont collectées par l’environnement d’hébergement Azure, et **système d’exploitation invité** métriques sont collectées par le [agent de surveillance](agents-overview.md) en cours d’exécution sur vos machines virtuelles. Vous installez l’agent de surveillance en activant [Extension de Diagnostic Azure](diagnostics-extension-overview.md).

Par défaut, les métriques du système d’exploitation invité sont stockés dans le compte de stockage Azure, que vous choisissez du **les paramètres de Diagnostic** onglet de votre ressource. Si les métriques de système d’exploitation invité ne sont pas collectées ou l’Explorateur de mesures ne peut pas y accéder, vous verrez seulement les **hôte d’ordinateur virtuel** espace de noms métrique :

![image de métrique](./media/metrics-troubleshoot/cannot-pick-guest-os-namespace.png)

**Solution :** Si vous ne voyez pas **système d’exploitation invité** espace de noms et les métriques dans metrics explorer :

1. Vérifiez que [Extension de Diagnostic Azure](diagnostics-extension-overview.md) est activé et configuré pour collecter les métriques.
    > [!WARNING]
    > Vous ne pouvez pas utiliser [agent d’Analytique de journal](agents-overview.md#log-analytics-agent) (également appelée le Microsoft Monitoring Agent, ou « MMA ») pour envoyer **système d’exploitation invité** dans un compte de stockage.

1. Vérifiez que le compte de stockage n’est pas protégé par le pare-feu.

1. Utilisez le [Explorateur stockage Azure](https://azure.microsoft.com/features/storage-explorer/) pour valider que les mesures sont transmis dans le compte de stockage. Si les mesures ne sont pas collectées, suivez le [guide de dépannage de l’Extension Azure Diagnostics](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal).

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur la mise en route avec Metrics Explorer](metrics-getting-started.md)
* [En savoir plus sur les fonctionnalités avancées de Metrics Explorer](metrics-charts.md)
* [Afficher la liste des métriques disponibles pour les services Azure](metrics-supported.md)
* [Consultez les exemples de graphiques configurés](metric-chart-samples.md)