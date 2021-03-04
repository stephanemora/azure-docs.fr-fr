---
title: Explication de l’agrégation et de l’affichage des métriques Azure Monitor
description: Informations détaillées sur la façon dont les métriques sont agrégées dans Azure Monitor
author: rboucher
ms.author: robb
services: azure-monitor
ms.topic: conceptual
ms.date: 01/12/2020
ms.subservice: metrics
ms.openlocfilehash: a88a52163f03d2bffb0c9c4eab53fd814d9554fb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101725864"
---
# <a name="azure-monitor-metrics-metrics-aggregation-and-display-explained"></a>Explication de l’agrégation et de l’affichage des métriques Azure Monitor

Cet article explique l’agrégation des métriques dans la base de données de série chronologique Azure Monitor où sont sauvegardées les [métriques de plateforme](../data-platform.md) et les [métriques personnalisées](../essentials/metrics-custom-overview.md) Azure Monitor. Cet article s’applique également aux [métriques Application Insights](../app/app-insights-overview.md) standard. 

Vous n’êtes pas obligé de comprendre tout le contenu de cet article pour utiliser efficacement les métriques Azure Monitor, car il s’agit d’un sujet complexe.

## <a name="overview-and-terms"></a>Présentation et terminologie

Lorsque vous ajoutez une métrique à un graphique, Metrics Explorer présélectionne automatiquement son agrégation par défaut. La valeur par défaut convient aux scénarios de base. Toutefois, vous pouvez utiliser d’autres agrégations pour obtenir des insights supplémentaires sur la métrique. Pour afficher différentes agrégations sur un graphique, vous devez comprendre comment Metrics Explorer les gère.

Nous allons tout d’abord définir quelques termes :

- **Valeur de métrique** : valeur de mesure collectée pour une ressource spécifique.
- **Période** : période de temps générique.
- **Intervalle de temps** : temps écoulé entre deux collectes de valeurs de métrique. 
- **Plage temporelle** : période affichée sur un graphique. La valeur par défaut est généralement de 24 heures. Seules les plages précises sont disponibles. 
- **Granularité temporelle** ou **Fragment de temps** : période utilisée pour agréger des valeurs en vue de leur affichage sur un graphique. Seules les plages précises sont disponibles. La valeur minimale est de 1 minute. Pour être utile, la valeur de granularité temporelle doit être inférieure à celle de la plage temporelle sélectionnée, sinon, une seule valeur sera affichée pour l’ensemble du graphique. 
- **Type d’agrégation** : type de statistique calculé à partir de plusieurs valeurs de métriques.  
- **Agrégat** : processus qui consiste à utiliser plusieurs valeurs d’entrée, puis à les utiliser pour produire une seule valeur de sortie via les règles définies par le type d’agrégation. Par exemple, en utilisant la moyenne de plusieurs valeurs.  

Les métriques correspondent à une série de valeurs de métriques capturées à un intervalle de temps régulier. Lorsque vous tracez un graphique, les valeurs de la métrique sélectionnée sont agrégées séparément pendant la granularité temporelle (également appelée « fragment de temps »). Vous sélectionnez la taille de la granularité temporelle à l’aide du [panneau du sélecteur d’heure Metrics Explorer](../essentials/metrics-getting-started.md#select-a-time-range). Si vous n’effectuez pas de sélection explicite, la granularité temporelle est automatiquement sélectionnée en fonction de la plage temporelle actuellement sélectionnée. Une fois la sélection effectuée, les valeurs de métriques qui ont été capturées à chaque intervalle de granularité temporelle sont agrégées et placées sur le graphique (un point de données par intervalle).

## <a name="aggregation-types"></a>Types d’agrégation 

Cinq types d’agrégation de base sont disponibles dans Metrics Explorer : Metrics Explorer masque les agrégations qui ne sont pas pertinentes et ne peut pas être utilisé pour une métrique donnée. 

- **Somme** : somme de toutes les valeurs capturées au cours de l’intervalle d’agrégation. La somme est parfois appelée « agrégation totale ».
- **Nombre** : nombre de mesures capturées au cours de l’intervalle d’agrégation. Il ne s’intéresse pas à la valeur de la mesure, mais uniquement au nombre d’enregistrements. 
- **Moyenne** : moyenne des valeurs de métriques capturées au cours de l’intervalle d’agrégation. Pour la plupart des métriques, cette valeur correspond à celle de Somme ou de Nombre. 
- **Min** : la plus petite valeur capturée au cours de l’intervalle d’agrégation.
- **Max** : la plus grande valeur capturée au cours de l’intervalle d’agrégation.

Supposons, par exemple, qu’un graphique montre la métrique **Octets sortants réseau totaux** pour une machine virtuelle, en utilisant l’agrégation **SUM** obtenue au cours des dernières 24 heures. La plage temporelle et la granularité peuvent être modifiées en haut à droite du graphique, comme le montre la capture d’écran suivante.

:::image type="content" source="media/metrics-aggregation-explained/time-range-granularity-picker.png" alt-text="Capture d’écran montrant le sélecteur de plage temporelle et de granularité temporelle" border="true":::

Pour la granularité temporelle = 30 minutes et pour la plage temporelle = 24 heures :

- Le graphique est dessiné à partir de 48 points de données se présentant sous la forme de points de données de 1 minute agrégés (24 heures x 2 points de données par heure (60 minutes/30 minutes)). 
- Le graphique en courbes connecte 48 points dans la zone de traçage du graphique. 
- Chaque point de données représente la somme de tous les octets sortants réseau qui sont envoyés au cours de chaque période de 30 minutes. 


 :::image type="content" source="media/metrics-aggregation-explained/24-hour-30-min-gran.png" alt-text="Capture d’écran montrant les données d’un graphique en courbes configuré sur une plage temporelle de 24 heures et une granularité temporelle de 30 minutes" border="true" lightbox="media/metrics-aggregation-explained/24-hour-30-min-gran.png":::

*Cliquez sur les images de cette section pour les afficher en plus grand.*

Si vous configurez la granularité temporelle sur 15 minutes, le graphique sera dessiné à partir de 96 points de données agrégés. Autrement dit, 60 min/15 min = 4 points de données par heure x 24 heures.

:::image type="content" source="media/metrics-aggregation-explained/24-hour-15-min-gran.png" alt-text="Capture d’écran montrant les données d’un graphique en courbes configuré sur une plage temporelle de 24 heures et une granularité temporelle de 15 minutes" border="true" lightbox="media/metrics-aggregation-explained/24-hour-15-min-gran.png":::

Pour une granularité temporelle de 5 minutes, vous obtenez 24 x (60/5) = 288 points. 

:::image type="content" source="media/metrics-aggregation-explained/24-hour-5-min-gran.png" alt-text="Capture d’écran montrant les données d’un graphique en courbes configuré sur une plage temporelle de 24 heures et une granularité temporelle de 5 minutes" border="true" lightbox="media/metrics-aggregation-explained/24-hour-15-min-gran.png":::

Pour une granularité temporelle de 1 minute (la plus petite possible sur le graphique), vous obtenez 24 x 60/1 = 1 440 points. 

:::image type="content" source="media/metrics-aggregation-explained/24-hour-1-min-gran.png" alt-text="Capture d’écran montrant les données d’un graphique en courbes configuré sur une plage temporelle de 24 heures et une granularité temporelle de 1 minute" border="true" lightbox="media/metrics-aggregation-explained/24-hour-1-min-gran.png":::

Comme vous pouvez le voir dans les captures d’écran précédentes, les graphiques ont une apparence différente selon les sommes.  Remarquez que cette machine virtuelle présente un grand nombre de sorties pendant une courte période par rapport au reste de la fenêtre de temps.  

La granularité temporelle vous permet d’ajuster le rapport signal/bruit sur un graphique. Les agrégations les plus élevées suppriment le bruit et aplanissent les pics.  Remarquez les variations du graphique avec une granularité de 1 minute, ainsi que la façon dont elles s’aplanissent à mesure que les valeurs de granularité augmentent. 

Ce comportement d’aplanissement est important lorsque vous envoyez ces données à d’autres systèmes, par exemple pour des alertes. En règle générale, vous ne souhaitez pas être alerté pour des pics très courts en temps processeur dépassant 90 %. Toutefois, si le processeur reste à 90 % pendant 5 minutes, il s’agit probablement d’un problème important. Si vous configurez une règle d’alerte pour le processeur (ou pour n’importe quelle métrique), le fait d’augmenter la granularité temporelle pourra réduire le nombre de fausses alertes que vous recevez. 

Il est important de déterminer ce qui est « normal » pour votre charge de travail afin de savoir quel intervalle de temps est le mieux adapté. Cela fait partie des avantages des [alertes dynamiques](../alerts/alerts-dynamic-thresholds.md), qui ne seront pas abordées ici.  

## <a name="how-the-system-collects-metrics"></a>Comment le système collecte les métriques

La collecte des données varie selon la métrique. Il existe deux types de périodes de collecte.

### <a name="measurement-collection-frequency"></a>Fréquence de collecte des mesures 

- **Régulier** : la métrique est collectée à un intervalle de temps qui ne varie pas.

- **D’après l’activité** : la métrique est collectée en fonction du moment où se produit une transaction d’un certain type. Chaque transaction est associée à une entrée de métrique et à un horodatage. Elle n’est pas collectée à intervalles réguliers. Par conséquent, le nombre d’enregistrements varie sur une période donnée. 

### <a name="granularity"></a>Granularité

L’intervalle de temps minimal est de 1 minute. Toutefois, le système sous-jacent peut capturer des données plus rapidement en fonction de la métrique. Par exemple, le pourcentage d’utilisation du processeur est capturé toutes les 15 secondes à intervalle régulier. Étant donné que les échecs HTTP sont capturés comme des transactions, ils peuvent dépasser largement 1 minute. D’autres métriques, telles que le stockage SQL, sont capturées toutes les 20 minutes. Ce choix dépend du fournisseur et du type de ressources. La plupart tentent de fournir le plus petit intervalle possible.

### <a name="dimensions-splitting-and-filtering"></a>Dimensions, fractionnement et filtrage

Des métriques sont capturées pour chaque ressource. Toutefois, le niveau auquel les métriques sont collectées, stockées et représentées sous forme de graphique peut varier. Ce niveau est représenté par des métriques supplémentaires qui sont disponibles dans les **dimensions de métriques**. Chaque fournisseur de ressources choisit le niveau de détail des données collectées. Azure Monitor définit uniquement la manière dont ces détails sont présentés et stockés. 

Dans Metrics Explorer, lorsque vous représentez une métrique sous forme de graphique, vous avez la possibilité de « fractionner » le graphique en fonction d’une dimension.  Le fractionnement d’un graphique vous permet d’examiner les données sous-jacentes pour obtenir plus de détails, et de voir que les données sont représentées sous forme graphique ou qu’elles sont filtrées dans Metrics Explorer.

Par exemple, pour [Microsoft.ApiManagement/service](./metrics-supported.md#microsoftapimanagementservice), *Emplacement* est une dimension qui est définie pour de nombreuses métriques. 

- **Capacité** fait partie de ces métriques. L’utilisation de la dimension *Emplacement* signifie que le système sous-jacent va stocker un enregistrement de métrique pour la capacité de chaque emplacement, plutôt qu’un seul pour la quantité agrégée. Vous pouvez ensuite récupérer ou fractionner ces informations dans un graphique de métriques.  

- Lorsque vous regardez **Durée globale des demandes de passerelle**, vous voyez deux dimensions : *Emplacement* et *Nom d’hôte*. Celles-ci vous indiquent l’emplacement d’une durée, ainsi que le nom d’hôte dont elle provient.  

- L’une des métriques les plus flexibles, **Demandes**, comporte 7 dimensions. 
 
Pour plus d’informations sur chacune des métriques et sur les dimensions disponibles, consultez l’article Azure Monitor sur les [métriques prises en charge](./metrics-supported.md). De plus, la documentation de chaque type et chaque fournisseur de ressources peut fournir des informations supplémentaires sur les dimensions et sur ce qu’elles permettent de mesurer.

Vous pouvez utiliser à la fois le fractionnement et le filtrage des données pour examiner un problème. Vous trouverez ci-dessous un exemple de graphique présentant la métrique *Moy. disque, octets/écriture* pour un groupe de machines virtuelles au sein d’un groupe de ressources. Avec cette métrique, nous avons un récapitulatif pour toutes les machines virtuelles. Toutefois, nous pourrions vouloir connaître les machines qui sont responsables des pics qui se sont produits aux environs de 6 h 00. S’agit-il d’une seule et même machine ? Combien de machines sont impliquées ?  

:::image type="content" source="media/metrics-aggregation-explained/total-disk write-bytes-all-VMs.png" alt-text="Capture d’écran montrant le nombre total d’octets d’écriture sur le disque pour toutes les machines virtuelles appartenant au groupe de ressources Contoso Hotels" border="true" lightbox="media/metrics-aggregation-explained/total-disk write-bytes-all-VMs.png":::

*Cliquez sur les images de cette section pour les afficher en plus grand.*

Lorsque nous appliquons le fractionnement, nous pouvons voir les données sous-jacentes, toutefois, leur affichage n’est pas très clair. Il se trouve que 20 machines virtuelles sont agrégées dans le graphique ci-dessus. Ici, nous avons utilisé notre souris pour pointer sur le pic de 6 h 00, qui nous indique que CH-DCVM11 est en cause. Toutefois, il est difficile de voir les autres données associées à cette machine virtuelle, car d’autres machines virtuelles encombrent le graphique. 

:::image type="content" source="media/metrics-aggregation-explained/split-total-disk write-bytes-all-VMs.png" alt-text="Capture d’écran montrant le nombre d’octets d’écriture sur le disque pour toutes les machines virtuelles appartenant au groupe de ressources Contoso Hotels, avec une segmentation par nom de machine virtuelle" border="true" lightbox="media/metrics-aggregation-explained/split-total-disk write-bytes-all-VMs.png":::

L’utilisation du filtrage nous permet de nettoyer le graphique pour voir ce qu’il se passe vraiment. Vous pouvez cocher ou décocher les machines virtuelles que vous souhaitez afficher. Remarquez les lignes en pointillés. Celles-ci seront mentionnées dans une prochaine section.

:::image type="content" source="media/metrics-aggregation-explained/split-filter-total-disk write-bytes-all-VMs.png" alt-text="Capture d’écran montrant le nombre d’octets d’écriture sur le disque pour toutes les machines virtuelles appartenant au groupe de ressources Contoso Hotels, avec une segmentation et un filtrage par nom de machine virtuelle" border="true" lightbox="media/metrics-aggregation-explained/split-filter-total-disk write-bytes-all-VMs.png":::

Pour plus d’informations sur la façon d’afficher des données fractionnées par dimension sur un graphique Metrics Explorer, consultez [Fonctionnalités avancées de Metrics Explorer - Filtres et fractionnement](../essentials/metrics-charts.md#filters).

### <a name="null-and-zero-values"></a>Valeurs NULL et égales à zéro

Lorsque le système s’attend à recevoir des données de métriques d’une ressource, mais ne les reçoit pas, il enregistre une valeur NULL.  Une valeur NULL n’équivaut pas à une valeur de zéro, ce qu’il est très important de prendre en compte lors du calcul des agrégations et de la génération des graphiques. Les valeurs NULL ne sont pas comptabilisées comme des mesures valides. 

Les valeurs NULL s’affichent différemment selon les graphiques. Les nuages de points ne montrent pas l’un des points sur le graphique. Les graphiques à barres ne montrent pas la barre. Sur les graphiques en courbes, les valeurs NULL peuvent s’afficher sous forme de [lignes en traits ou en pointillés](../essentials/metrics-troubleshoot.md#chart-shows-dashed-line), comme celles que montre la capture d’écran de la section précédente. Lorsque vous calculez des moyennes comprenant des valeurs NULL, il y a moins de points de données desquels utiliser la moyenne.  Ce comportement peut parfois entraîner une baisse inattendue des valeurs d’un graphique, même si, généralement, cela se produit plus fréquemment lorsque la valeur a été convertie en une valeur de zéro, puis utilisée comme un point de données valide.  

Les [métriques personnalisées](../essentials/metrics-custom-overview.md) utilisent toujours des valeurs NULL lorsqu’elles ne reçoivent aucune donnée. Avec les [métriques de plateforme](../data-platform.md), chaque fournisseur de ressources décide s’il faut utiliser des zéros ou des valeurs NULL en fonction de ce qui convient le mieux pour une métrique donnée.

Les alertes Azure Monitor utilisent les valeurs que le fournisseur de ressources écrit dans la base de données de métriques. Il est donc important de savoir comment le fournisseur de ressources gère les valeurs NULL en affichant d’abord les données.

## <a name="how-aggregation-works"></a>Fonctionnement de l’agrégation

Les graphiques de métriques du système précédent affichent différents types de données agrégées. Le système préagrège les données afin que les graphiques demandés puissent s’afficher plus rapidement sans nécessiter de nombreux de calculs répétés.  

Dans cet exemple :

- Nous collectons une métrique transactionnelle **fictive** appelée **Échecs HTTP**. 
- La dimension *Serveur* est une dimension pour la métrique **Échecs HTTP**.
- Nous avons trois serveurs : les serveurs A, B et C.

Pour simplifier l’explication, nous allons commencer par le type d’agrégation SUM uniquement. 

### <a name="sub-minute-to-1-minute-aggregation"></a>Agrégation allant de moins d’une minute à 1 minute

Les premières données de métriques brutes sont collectées et stockées dans la base de données de métriques Azure Monitor. Dans ce cas, chaque serveur stocke des enregistrements de transaction avec un horodatage, car *Serveur* est une dimension. Étant donné que la plus courte période que vous pouvez afficher en tant que client est égale à 1 minute, ces horodatages sont d’abord regroupés en valeurs de métriques d’une minute pour chacun des serveurs.  Le processus d’agrégation pour le serveur B est indiqué dans le graphique ci-dessous. L’agrégation des serveurs A et C est effectuée de la même façon, et ceux-ci comportent des données différentes.  

:::image type="content" source="media/metrics-aggregation-explained/sub-minute-transaction.png" alt-text="Capture d’écran montrant les entrées transactionnelles inférieures à 1 minute agrégées pour former des entrées de 1 minute." border="false":::

Les valeurs agrégées de 1 minute obtenues sont stockées en tant que nouvelles entrées dans la base de données de métriques, afin qu’elles puissent être collectées dans le cadre de prochains calculs. 

:::image type="content" source="media/metrics-aggregation-explained/sub-minute-transaction-dimension-aggregated.png" alt-text="Capture d’écran montrant plusieurs entrées agrégées de 1 minute pour la dimension Serveur. Les serveurs A, B et C sont affichés individuellement" border="false":::

### <a name="dimension-aggregation"></a>Agrégation des dimensions

Les calculs de 1 minute sont ensuite réduits par dimension et stockés à nouveau comme des enregistrements individuels.   Dans ce cas, toutes les données de tous les serveurs sont agrégées dans une métrique d’intervalle de 1 minute, puis elles sont stockées dans la base de données de métriques en vue d’une utilisation dans de prochaines agrégations.

:::image type="content" source="media/metrics-aggregation-explained/1-minute-transaction-dimension-flattened-aggregated.png" alt-text="Capture d’écran montrant plusieurs entrées agrégées de 1 minute pour les serveurs A, B et C, agrégées sous la forme d’entrées de 1 minute pour tous les serveurs" border="false":::

Par souci de clarté, le tableau suivant montre la méthode d’agrégation.

| Période   | Serveur A | serveur B. | le serveur C. | Somme (A+B+C)|
| -------- | -------- | -------- | -------- | --------   |  
| Minute 1 | 1        | 1        | 1        | 3          |
| Minute 2 | 0        | 5        | 1        | 6          |
| Minute 3 | 0        | 5        | 1        | 6          |
| Minute 4 | 2        | 3        | 4        | 9          |
| Minute 5 | 1        | 0        | 3        | 4          |
| Minute 6 | 1        | 0        | 4        | 5          |
| Minute 7 | 1        | 2        | 4        | 7          |
| Minute 8 | 0        | 1        | 0        | 1          |
| Minute 9 | 1        | 1        | 4        | 6          |
| Minute 10| 2        | 1        | 0        | 3          |

Une seule dimension est présentée ci-dessus. Toutefois, le même processus d’agrégation et de stockage se produit pour **toutes les dimensions** qui sont prises en charge par une métrique.

- Collectez les valeurs dans un ensemble agrégé de 1 minute selon cette dimension. Stockez ces valeurs. 
- Réduisez la dimension en une somme agrégée de 1 minute. Stockez ces valeurs. 

Nous allons présenter une autre dimension de la métrique Échecs HTTP : « NetworkAdapter ». Supposons que nous ayons un nombre variable de cartes par serveur.

- Le serveur A a 1 carte
- Le serveur B a 2 cartes
- Le serveur C a 3 cartes

Nous allons collecter les données des transactions suivantes séparément. Celles-ci seront associées aux éléments suivants :

- Un horodatage
- Une valeur
- Le serveur d’où provient la transaction
- La carte d’où provient la transaction

Ces flux inférieurs à 1 minute sont ensuite agrégés en valeurs de série chronologique de 1 minute, puis ils sont stockés dans la base de données de métriques Azure Monitor :

- Serveur A, Carte 1
- Serveur B, Carte 1
- Serveur B, Carte 2
- Serveur C, Carte 1
- Serveur C, Carte 2
- Serveur C, Carte 3

En outre, les agrégations réduites suivantes sont également stockées :

- Serveur A, carte 1 (étant donné qu’il n’y a rien à réduire, les données seront à nouveau stockées)
- Serveur B, Cartes 1+2
- Serveur C, Cartes 1+2+3
- Tous les serveurs et toutes les cartes

Cela montre que les métriques qui ont un grand nombre de dimensions comportent un plus grand nombre d’agrégations. Il n’est pas nécessaire de connaître toutes les permutations, mais simplement de comprendre le raisonnement. Le système souhaite que les données individuelles et les données agrégées soient stockées en vue d’être récupérées rapidement à partir de n’importe quel graphique. Le système sélectionne l’agrégation stockée ou les données brutes sous-jacentes les plus pertinentes en fonction de ce que vous choisissez d’afficher. 

### <a name="aggregation-with-no-dimensions"></a>Agrégation sans dimension

Étant donné que cette métrique comporte une dimension *Serveur*, vous pouvez accéder aux données sous-jacentes des serveurs A, B et C ci-dessus via le fractionnement et le filtrage, comme expliqué plus haut dans cet article. Si la métrique n’avait pas la dimension *Serveur*, vous, en tant que client, pourriez uniquement accéder aux sommes agrégées de 1 minute qui s’affichent en noir sur le diagramme. Ce sont les valeurs 3, 6, 6, 9, etc. Le système n’effectuera pas non plus le travail sous-jacent pour agréger les valeurs fractionnées, il ne les utilisera pas dans Metrics Explorer et il ne les enverra pas via l’API REST Metrics. 

## <a name="viewing-time-granularities-above-1-minute"></a>Affichage des granularités temporelles supérieures à 1 minute

Si vous demandez des métriques en utilisant une granularité plus longue, le système utilisera les sommes agrégées de 1 minute afin de calculer les sommes pour des granularités temporelles supérieures.  Les lignes en pointillés ci-dessous montrent la méthode de somme qui est employée pour les granularités temporelles de 2 et 5 minutes. Là encore, nous n’affichons que le type d’agrégation SUM pour des raisons de simplicité.

:::image type="content" source="media/metrics-aggregation-explained/1-minute-to-2-min-5-min.png" alt-text="Capture d’écran montrant plusieurs entrées agrégées de 1 minute pour la dimension Serveur, qui sont ensuite agrégées en périodes de 2 et 5 minutes." border="false":::

Pour la granularité temporelle de 2 minutes.

| Période       | Sommes       
| -------------|-------------|  
| Minutes 1 et 2 | (3 + 6) = 9 |
| Minutes 3 et 4 | (6 + 9) = 15|
| Minutes 4 et 5 | (4 + 5) = 9 |
| Minutes 6 et 7 | (7 + 1) = 8 |
| Minutes 8 et 9 | (6 + 3) = 9 |

Pour la granularité temporelle de 5 minutes.

| Période              |            Sommes        |
|---------------------|------------------------|  
| Minutes de 1 à 5  | 3 + 6 + 6 + 9 + 4 = 28 |
| Minutes de 6 à 10 | 5 + 7 + 1 + 6 + 3 = 22 |

Le système utilise les données agrégées stockées qui offrent les meilleures performances. 

Vous trouverez ci-dessous une version plus complexe du diagramme montrant le processus d’agrégation de 1 minute, auquel nous avons retiré certaines flèches pour améliorer la lisibilité.

:::image type="content" source="media/metrics-aggregation-explained/sum-aggregation-full.png" alt-text="Capture d’écran montrant la consolidation des trois captures d’écran précédentes. Plusieurs entrées agrégées de 1 minute pour la dimension Serveur, qui sont ensuite agrégées à intervalles de 1, 2 et 5 minutes. Les serveurs A, B et C sont affichés individuellement" border="false":::

## <a name="more-complex-example"></a>Exemple plus complexe

Voici un exemple plus complexe qui utilise des valeurs pour une métrique fictive appelée « Temps de réponse HTTP en millisecondes ».  Ici, nous utilisons d’autres niveaux de complexité.

1. Nous affichons l’agrégation pour Somme, Nombre, Min et Max, et le calcul pour Moyenne.
2. Nous montrons les valeurs NULL et la façon dont elles affectent les calculs. 

Considérez l'exemple suivant. Les flèches et les données encadrées montrent comment les valeurs sont agrégées et calculées. 

Le processus de préagrégation de 1 minute qui est décrit dans la section précédente se produit également pour Sommes, Nombre, Minimum et Maximum.  Toutefois, la Moyenne n’est pas préagrégée. Elle est recalculée à l’aide de données agrégées pour éviter les erreurs de calcul.
 
:::image type="content" source="media/metrics-aggregation-explained/full-aggregation-example-all-types.png" alt-text="Capture d’écran montrant un exemple complexe d’agrégation et de calcul pour Somme, Nombre, Min, Max et Moyenne, allant de 1 minute à 10 minutes." border="false" lightbox="media/metrics-aggregation-explained/full-aggregation-example-all-types.png":::

Prenons la minute 6 pour l’agrégation de 1 minute qui est mise en évidence ci-dessus. Cette minute correspond au moment où le serveur B a été mis hors connexion et a arrêté les données de rapport, peut-être en raison d’un redémarrage. 

À partir de la minute 6 ci-dessus, les types d’agrégation de 1 minute calculés sont les suivants : 

| Type d’agrégation | Valeur        | Notes |
|------------------|--------------|-------|
| SUM              | 53+20=73 | |
| Nombre            | 2            | Montre l’effet des valeurs NULL.  Si le serveur avait été en ligne, la valeur aurait été de 3.  |
| Minimum          | 20           | |
| Maximale          | 53           | |
| Average          | 73/2       | Toujours la somme divisée par le nombre. Elle n’est jamais stockée et toujours recalculée pour chaque granularité temporelle à l’aide des nombres agrégés pour cette granularité. Remarquez le recalcul pour les granularités temporelles de 5 minutes et 10 minutes qui est mis en évidence ci-dessus. |

Le texte en rouge indique les valeurs qui peuvent être considérées comme étant en dehors de la plage normale, et montre comment elles se propagent (ou échouent) à mesure que la granularité temporelle augmente. Remarquez que les valeurs *Min* et *Max* indiquent des anomalies sous-jacentes, et que *Moyenne* et *Sommes* perdent ces informations à mesure que la granularité temporelle augmente.  

Vous pouvez également voir que les valeurs NULL offrent un meilleur calcul de moyenne que les zéros.  

> [!NOTE] 
> Même si ce n’est pas le cas dans cet exemple, *Nombre* est égal à *Somme* lorsqu’une métrique est systématiquement capturée avec une valeur de 1. C’est courant lorsqu’une métrique effectue le suivi de l’occurrence d’un événement transactionnel (par exemple, le nombre d’échecs HTTP mentionnés dans un précédent exemple de cet article).

## <a name="next-steps"></a>Étapes suivantes

- [Bien démarrer avec Metrics Explorer](../essentials/metrics-getting-started.md)
- [Fonctionnalités avancées de Metrics Explorer](../essentials/metrics-charts.md)