---
title: Rapports Azure Front Door Standard/Premium (préversion)
description: Cet article explique comment fonctionne la création de rapports dans Azure Front Door.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 9670d8204d04fc770bf3fe98a270a3f6ccbf234b
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101097836"
---
# <a name="azure-front-door-standardpremium-preview-reports"></a>Rapports Azure Front Door Standard/Premium (préversion)

> [!IMPORTANT]
> Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Les rapports d’analytique Azure Front Door Standard/Premium fournissent une vue intégrée et intégrale de la façon dont Azure Front Door se comporte ainsi que des métriques Web Application Firewall associées. Vous pouvez également tirer parti des journaux d’accès pour effectuer des tâches de résolution des problèmes et de débogage supplémentaires. Les rapports d’analytique Azure Front Door incluent des rapports de trafic et des rapports de sécurité.

| Rapports | Détails |
|---------|---------|
| Vue d’ensemble des métriques clés | Affiche les données globales envoyées depuis les périphéries Azure Front Door vers les clients<br/>- Bande passante maximale<br/>- Demandes <br/>- Taux de succès du cache<br/> - Latence totale<br/>- Taux d’erreur 5XX |
| Trafic par domaine | - Fournit une vue d’ensemble de tous les domaines sous le profil<br/>- Répartition des données transférées à partir de la périphérie AFD vers le client<br/>- Nombre total de demandes<br/>- Code de réponse 3XX/4XX/5XX par domaine |
| Trafic par localisation | - Affiche une vue cartographique de la demande et de l’utilisation selon les principaux pays<br/>- Vue de la tendance des principaux pays |
| Usage | - Affiche le transfert de données sortant depuis la périphérie Azure Front Door vers les clients<br/>- Transfert de données sortant depuis l’origine vers la périphérie AFD<br/>- Bande passante depuis la périphérie AFD vers les clients<br/>- Bande passante depuis l’origine vers périphérie AFD<br/>- Demandes<br/>- Latence totale<br/>- Tendance du nombre de demandes par code d’état HTTP |
| Mise en cache | - Affiche le taux de succès du cache par nombre de demandes<br/>- Vue de la tendance des demandes aboutissant à un succès/défaut |
| URL principale | - Affiche le nombre de demandes <br/>- Données transférées <br/>- taux de succès du cache <br/>- Distribution du code d’état de la réponse pour les 50 actifs multimédias les plus demandés. |
| Référent principal | - Affiche le nombre de demandes <br/>- Données transférées <br/>- taux de succès du cache <br/>- Distribution du code d’état de la réponse pour les 50 principaux référents qui génèrent du trafic. |
| Agent utilisateur principal | - Affiche le nombre de demandes <br/>- Données transférées <br/>- taux de succès du cache <br/>- Distribution du code d’état de la réponse pour les 50 principaux agents utilisateur qui ont été utilisés pour demander du contenu. |

| Rapports de sécurité | Détails |
|---------|---------|
| Vue d’ensemble des métriques clés | - Affiche les règles WAF mises en correspondance<br/>- Règles OWASP mises en correspondance<br/>-Règles BOT mises en correspondance<br/>- Règles personnalisées mises en correspondance |
| Métriques par dimensions | - Répartition de la tendance des règles WAF mises en correspondance par action<br/>- Graphique en anneau des événements par type d’ensemble de règles et par groupe de règles<br/>- Liste de répartition des principaux événements par ID de règle, pays, adresse IP, URL et agent utilisateur  |

> [!NOTE]
> Les rapports de sécurité sont uniquement disponibles avec la référence SKU Azure Front Door Premium.

La plupart des rapports sont basés sur les journaux d’accès et sont proposés gratuitement aux clients sur Azure Front Door. Le client n’a pas besoin d’activer les journaux d’accès ou d’effectuer de configuration particulière pour afficher ces rapports. Les rapports sont accessibles par le biais du portail et de l’API. Le téléchargement CSV est également pris en charge. 

Les rapports prennent en charge toute plage de dates sélectionnée au cours des 90 jours précédents, avec des points de données toutes les 5 minutes, toutes les heures ou tous les jours, en fonction de la plage de dates sélectionnée. Normalement, vous pouvez afficher les données avec un délai d’une heure maximum et parfois avec un délai pouvant atteindre quelques heures. 

## <a name="access-reports-using-the-azure-portal"></a>Accéder aux rapports avec le portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) et sélectionnez votre profil Azure Front Door Standard/Premium.

1. Dans le volet de navigation, sélectionnez **Rapports ou Sécurité** sous *Analytique*.

   :::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="Capture d’écran de la page d’arrivée Rapports":::

1. Il existe sept onglets pour différentes dimensions ; sélectionnez la dimension qui vous intéresse.

   * Trafic par domaine
   * Usage 
   * Trafic par localisation
   * Cache
   * URL principale
   * Référent principal
   * Agent utilisateur principal

1. Après avoir choisi la dimension, vous pouvez sélectionner différents filtres.
  
    1. **Afficher les données pour** : sélectionnez la plage de dates pour laquelle vous souhaitez afficher le trafic par domaine. Les plages disponibles sont les suivantes :
        
        * 24 dernières heures
        * 7 derniers jours
        * 30 derniers jours
        * 90 derniers jours
        * This month
        * Le mois dernier
        * Date personnalisée

         Par défaut, les données sont affichées pour les sept derniers jours. Pour les onglets avec des graphiques en courbes, la précision des données est liée aux plages de dates que vous avez sélectionnées comme comportement par défaut. 
    
        * 5 minutes : un point de données toutes les 5 minutes pour les plages de dates inférieures ou égales à 24 heures
        * Par heure : un point de données toutes les heures pour les plages de dates comprises entre 24 heures et 30 jours
        * Par jour : un point de données par jour pour les plages de dates supérieures à 30 jours

        Vous pouvez toujours utiliser la fonction d’agrégation pour changer la précision d’agrégation par défaut. Remarque : la précision de 5 minutes ne fonctionne pas pour les plages de données de plus de 14 jours. 

    1. **Localisation** : sélectionnez une ou plusieurs localisations clientes par pays. Les pays sont regroupés en six régions : Amérique du Nord, Asie, Europe, Afrique, Océanie et Amérique du Sud. Reportez-vous à la [correspondance région/pays](https://en.wikipedia.org/wiki/Subregion). Par défaut, tous les pays sont sélectionnés.
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-locations.png" alt-text="Capture d’écran des rapports pour la dimension de localisation":::
   
    1. **Protocole** : sélectionnez HTTP ou HTTPS pour afficher les données de trafic.
 
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-protocol.png" alt-text="Capture d’écran de Rapports pour la dimension de protocole":::
    
    1. **Domaines** : sélectionnez des points de terminaison uniques ou multiples ou des domaines personnalisés. Par défaut, tous les points de terminaison et les domaines personnalisés sont sélectionnés. 
    
        * Si vous supprimez un point de terminaison ou un domaine personnalisé dans un profil, puis recréez le même point de terminaison ou domaine dans un autre profil, le point de terminaison est considéré comme un deuxième point de terminaison.  
        * Si vous affichez des rapports en fonction d’un domaine personnalisé, quand vous supprimez un domaine personnalisé et que vous le liez à un autre point de terminaison, ils sont traités comme un seul domaine personnalisé. Si vous les affichez par point de terminaison, ils sont traités en tant qu’éléments distincts. 
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-domain.png" alt-text="Capture d’écran de Rapports pour la dimension de domaine":::

1. Si vous souhaitez exporter les données vers un fichier CSV, sélectionnez le lien *Télécharger le fichier CSV* sous l’onglet sélectionné.

    :::image type="content" source="../media/how-to-reports/front-door-reports-download-csv.png" alt-text="Capture d’écran du téléchargement d’un fichier CSV pour les rapports":::

### <a name="key-metrics-for-all-reports"></a>Métriques clés pour tous les rapports

| Métrique | Description |
|---------|---------|
| Données transférées | Affiche les données transférées à partir des POP de périphérie AFD vers le client pour la période, les localisations clientes, les domaines et les protocoles sélectionnés. |
| Bande passante maximale | Utilisation maximale de la bande passante en bits par seconde depuis les POP de périphérie Azure Front Door vers le client pour la période, les localisations clientes, les domaines et les protocoles sélectionnés. | 
| Total de requêtes | Nombre de demandes du client auxquelles ont répondu les POP de périphérie AFD pour la période, les localisations clientes, les domaines et les protocoles sélectionnés. |
| Taux d'accès au cache | Pourcentage de toutes les demandes pouvant être mises en cache pour lesquelles AFD a servi le contenu de ses caches de périphérie pour la période, les localisations clientes, les domaines et les protocoles sélectionnés. |
| Taux d’erreur 5XX | Pourcentage de demandes pour lesquelles le code d’état HTTP envoyé au client était 5XX pour la période, les localisations clientes, les domaines et les protocoles sélectionnés. |
| Latence totale | Latence moyenne de toutes les demandes pour la période, les localisations clientes, les domaines et les protocoles sélectionnés. La latence de chaque demande représente la durée totale allant de la réception de la demande du client par Azure Front Door à l’envoi du dernier octet de réponse par Azure Front Door au client. |

## <a name="traffic-by-domain"></a>Trafic par domaine

Le trafic par domaine fournit une vue de grille de tous les domaines sous ce profil Azure Front Door. Dans ce rapport, vous pouvez voir les éléments suivants : 
* Demandes
* Données transférées depuis Azure Front Door vers le client
* Demandes avec le code d’état (3XX, 4Xx et 5XX) de chaque domaine

Les domaines incluent le point de terminaison et les domaines personnalisés, comme expliqué dans la section consacrée à l’accès aux rapports.  

Vous pouvez accéder à d’autres onglets pour approfondir votre investigation ou afficher le journal d’accès afin d’obtenir des informations supplémentaires si vous trouvez que les métriques sont en deçà de vos attentes. 

:::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="Capture d’écran de la page d’arrivée pour les rapports":::


## <a name="usage"></a>Usage

Ce rapport présente les tendances de trafic et de code d’état de la réponse selon différentes dimensions, notamment :

* Données transférées depuis la périphérie vers le client et depuis l’origine vers la périphérie dans un graphique en courbes. 

* Données transférées depuis la périphérie vers le client par protocole dans un graphique en courbes. 

* Nombre de demandes depuis la périphérie vers les clients dans un graphique en courbes.  

* Nombre de demandes depuis la périphérie vers les clients par protocole (HTTP et HTTPS) dans un graphique en courbes. 

* Bande passante depuis la périphérie vers un client dans un graphique en courbes. 

* Latence totale, qui mesure la durée totale de la requête du client reçue par Front Door jusqu’au dernier octet de réponse envoyé de Front Door au client.

* Nombre de demandes depuis la périphérie vers les clients par code d’état HTTP, dans un graphique en courbes. Chaque demande génère un code d’état HTTP. Le code d’état HTTP apparaît dans HTTPStatusCode dans le journal brut. Le code d’état décrit comment la périphérie CDN a géré la demande. Par exemple, un code d’état 2xx indique que la demande a été correctement servie à un client. Pour sa part, un code d’état 4xx indique qu’une erreur s’est produite. Pour plus d’informations sur les codes d’état HTTP, consultez la Liste des codes d’état HTTP. 

* Nombre de demandes depuis la périphérie vers les clients par code d’état HTTP. Pourcentage de demandes par code d’état HTTP parmi toutes les demandes dans la grille. 

:::image type="content" source="../media/how-to-reports/front-door-reports-usage.png" alt-text="Capture d’écran de Rapports par utilisation" lightbox="../media/how-to-reports/front-door-reports-usage-expanded.png":::

## <a name="traffic-by-location"></a>Trafic par localisation

Ce rapport affiche les 50 principales localisations, correspondant aux pays des visiteurs qui accèdent le plus à votre actif multimédia. Le rapport fournit également une répartition des métriques par pays et vous donne une vue globale des pays où la plupart du trafic est généré. Enfin, vous pouvez voir quel pays a un taux de succès du cache plus élevé ou des codes d’erreur 4XX/5XX.

:::image type="content" source="../media/how-to-reports/front-door-reports-by-location.png" alt-text="Capture d’écran de Rapports par localisation" lightbox="../media/how-to-reports/front-door-reports-by-location-expanded.png":::

Les éléments suivants sont inclus dans les rapports :

* Vue cartographique mondiale des 50 premiers pays par données transférées ou demandes de votre choix.
* Vue des tendances dans deux graphiques en courbes des cinq premiers pays par données transférées et demandes de votre choix. 
* Une grille des principaux pays indiquant, pour chacun, les données transférées depuis AFD vers les clients, le pourcentage de données transférées, les demandes, le pourcentage de demandes, le taux de succès du cache, le code de réponse 4XX et le code de réponse 5XX.

## <a name="caching"></a>Mise en cache

La mise en cache des rapports fournit une vue graphique des succès/défauts du cache et du taux de succès du cache en fonction des demandes. Ces métriques clés expliquent comment le CDN met en cache le contenu, car les résultats de performances les plus rapides proviennent des succès du cache. Vous pouvez optimiser les vitesses de remise de données en réduisant les défauts du cache. Ce rapport comprend les éléments suivants :

* Tendance du nombre de succès et de défauts du cache, dans un graphique en courbes.

* Taux de succès du cache dans un graphique en courbes.

La métrique de succès/défauts du cache décrit le nombre de succès et de défauts du cache pour les demandes du client.

* Succès : demandes du client qui sont traitées directement à partir des serveurs de périphérie Azure CDN. Fait référence aux demandes dont les valeurs pour CacheStatus dans les journaux bruts sont HIT, PARTIAL_HIT ou REMOTE HIT. 

* Défauts : demandes du client traitées par les serveurs de périphérie Azure CDN qui récupèrent le contenu de l’origine. Fait référence aux demandes dont les valeurs pour le champ CacheStatus dans les journaux bruts sont MISS. 

Le **taux de succès du cache** décrit le pourcentage de demandes mises en cache traitées directement depuis la périphérie. La formule du taux de succès du cache est : `(PARTIAL_HIT +REMOTE_HIT+HIT/ (HIT + MISS + PARTIAL_HIT + REMOTE_HIT)*100%`. 

Ce rapport prend en considération les scénarios de mise en cache, et les demandes respectant les exigences suivantes sont prises en compte dans le calcul. 

* Le contenu demandé a été mis en cache sur le serveur POP le plus proche du demandeur ou de la protection de l’origine (origin shield). 

* Contenu partiellement mis en cache pour la segmentation d’objets.

Tous les cas suivants sont exclus : 

* Demandes refusées en raison d’un ensemble de règles.

* Demandes qui contiennent un ensemble de règles de correspondance défini sur un cache désactivé. 

* Demandes bloquées par WAF. 

* Les en-têtes de réponse d’origine indiquent qu’elles ne doivent pas être mises en cache. Par exemple, les en-têtes Cache-Control: private, Cache-Control: no-cache, ou Pragma: no-cache empêchent la mise en cache d’une ressource. 

:::image type="content" source="../media/how-to-reports/front-door-reports-caching.png" alt-text="Capture d’écran de Rapports pour la mise en cache":::

## <a name="top-urls"></a>URL principales

Les URL principales vous permettent d’afficher le volume de trafic généré sur un point de terminaison ou un domaine personnalisé particulier. Les données indiquées concernent les 50 actifs multimédias les plus demandés et couvrent une période au cours des 90 derniers jours. Les URL populaires sont affichées avec les valeurs suivantes. L’utilisateur peut trier les URL par nombre de demandes, pourcentage de demandes, données transférées et pourcentage de données transférées. Toutes les métriques sont agrégées par heure et peuvent varier selon la période sélectionnée. URL fait référence à la valeur de RequestUri dans le journal d’accès. 

:::image type="content" source="../media/how-to-reports/front-door-reports-top-url.png" alt-text="Capture d’écran de Rapports pour l’URL principale":::

* URL, fait référence au chemin complet de l’actif multimédia demandé au format `http(s)://contoso.com/index.html/images/example.jpg`. 
* Nombres de demandes. 
* Pourcentage du nombre total de demandes traitées par Azure Front Door. 
* Données transférées. 
* Pourcentage de données transférées. 
* Taux de succès du cache, en pourcentage
* Demandes avec code de réponse au format 4XX
* Demandes avec code de réponse au format 5XX

> [!NOTE]
> Les URL principales peuvent changer au fil du temps ; pour obtenir une liste exacte des 50 URL principales, Azure Front Door compte toutes vos demandes d’URL par heure et conserve le total cumulé au fil d’une journée. Les URL situées à la fin des 500 URL pouvant monter dans la liste ou la quitter au fil de la journée, le nombre total de ces URL repose sur des approximations.  
>
> Les 50 URL principales peuvent monter ou descendre dans la liste, mais elles disparaissent rarement de la liste ; ainsi, les nombres d’URL principales sont généralement fiables. Quand une URL quitte la liste et y revient au cours de la journée, le nombre de demandes pendant la période où elle est absente de la liste est estimé en fonction du nombre de demandes de l’URL qui apparaît dans cette période.  
>
> La même logique s’applique à l’agent Utilisateur principal. 

## <a name="top-referrers"></a>Référents principaux

Les référents principaux permettent aux clients d’afficher les 50 référents principaux à l’origine du plus grand nombre de demandes vers le contenu sur un point de terminaison ou un domaine personnalisé particulier. Vous pouvez afficher les données pour n’importe quelle période au cours des 90 derniers jours. Un référent indique l’URL à partir de laquelle une demande a été générée. Le référent peut provenir d’un moteur de recherche ou d’autres sites web. Si un utilisateur tape une URL (par exemple, http(s)://contoso.com/index.html) directement dans la ligne d’adresse d’un navigateur, le référent pour ce qui est demandé est « Empty » (vide). Le rapport sur les référents principaux comprend les valeurs suivantes. Vous pouvez effectuer un tri par nombre de demandes, pourcentage de demandes, données transférées et pourcentage de données transférées. Toutes les métriques sont agrégées par heure et peuvent varier selon la période sélectionnée. 

* Référent, la valeur de Referrer dans les journaux bruts 
* Nombres de demandes 
* Pourcentage du nombre total de demandes traitées par Azure CDN dans la période sélectionnée. 
* Données transférées 
* Pourcentage de données transférées 
* Taux de succès du cache, en pourcentage
* Demandes avec code de réponse au format 4XX
* Demandes avec code de réponse au format 5XX

:::image type="content" source="../media/how-to-reports/front-door-reports-top-referrer.png" alt-text="Capture d’écran de Rapports pour le référent principal":::

## <a name="top-user-agent"></a>Agent utilisateur principal

Ce rapport vous permet d’afficher des graphiques et des statistiques sur les 50 principaux agents utilisateur qui ont été utilisés pour demander du contenu. Par exemple,
* Mozilla/5.0 (Windows NT 10.0 ; WOW64) 
* AppleWebKit/537.36 (KHTML, comme Gecko) 
* Chrome/86.0.4240.75 
* Safari/537.36.  

Une grille affiche le nombre de demandes, le pourcentage de demandes, les données transférées et le pourcentage de données transférées, le taux de succès du cache (en pourcentage), les demandes avec code de réponse au format 4XX et les demandes avec code de réponse au format 5XX. La métrique Agent utilisateur fait référence à la valeur de UserAgent dans les journaux d’accès.

## <a name="security-report"></a>Rapport de sécurité

Ce rapport vous permet d’afficher des graphiques et des statistiques sur les modèles WAF selon différentes dimensions.

| Dimensions | Description |
|---------|---------|
| Métriques d’ensemble - Règles WAF mises en correspondance | Demandes qui correspondent à des règles WAF personnalisées, des règles WAF managées et au gestionnaire de bots. |
| Métriques d’ensemble - Demandes bloquées | Pourcentage de demandes bloquées par des règles WAF par rapport à toutes les demandes qui correspondent à des règles WAF. |
| Métriques d’ensemble - Règles managées mises en correspondance | Quatre graphiques en courbes des tendances pour les demandes : Block (Bloquer), Log (Journaliser), Allow (Autoriser) et Redirect (Rediriger). |
| Métriques d’ensemble - Règle personnalisée mise en correspondance | Demandes qui correspondent à des règles WAF personnalisées. |
| Métriques d’ensemble - Règles de bot mises en correspondance | Demandes qui correspondent au gestionnaire de bots. |
| Tendance des demandes WAF par action | Quatre graphiques en courbes des tendances pour les demandes : Block (Bloquer), Log (Journaliser), Allow (Autoriser) et Redirect (Rediriger). |
| Événements par type de règle | Graphique en anneau de la distribution des demandes WAF par type de règle, par exemple, Bot, règles personnalisées et règles managées. |
| Événements par groupe de règles | Graphique en anneau de la distribution des demandes WAF par groupe de règles. |
| Demandes par actions | Tableau des demandes par actions, dans l’ordre décroissant. |
| Demandes par ID de règle principale | Tableau des demandes en fonction des ID des 50 règles principales, dans l’ordre décroissant. |
| Demandes en fonction des pays principaux |  Tableau des demandes en fonction des 50 pays principaux, dans l’ordre décroissant. |
| Demandes en fonction des IP principales de client |  Tableau des demandes en fonction des 50 IP principales, dans l’ordre décroissant. |
| Requêtes par URL de requête principale |  Tableau des demandes en fonction des 50 URL principales, dans l’ordre décroissant. |
| Demandes en fonctions des noms d’hôte principaux | Tableau des demandes en fonction des 50 noms d’hôte principaux, dans l’ordre décroissant. |
| Demandes en fonction des agents utilisateur principaux | Tableau des demandes en fonction des 50 agents utilisateur principaux, dans l’ordre décroissant. |

## <a name="cvs-format"></a>Format CSV

Vous pouvez télécharger des fichiers CSV pour les différents onglets des rapports. Cette section décrit les valeurs de chaque fichier CSV.

### <a name="general-information-about-the-cvs-report"></a>Informations générales sur le rapport CSV

Chaque rapport CSV comprend des informations générales et les informations sont disponibles dans tous les fichiers CSV, avec des variables basées sur le rapport que vous téléchargez. 


| Value | Description |
|---------|---------|
| Rapport | Nom du rapport. | 
| Domaines | Liste des points de terminaison ou des domaines personnalisés pour le rapport. |
| StartDateUTC | Début de la plage de dates pour laquelle vous avez généré le rapport, en temps universel coordonné (UTC) |
| EndDateUTC | Fin de la plage de dates pour laquelle vous avez généré le rapport, en temps universel coordonné (UTC) |
| GeneratedTimeUTC | Date et heure auxquelles vous avez généré le rapport, en temps universel coordonné (UTC) |
| Emplacement | Liste des pays d’où proviennent les demandes du client. La valeur est ALL (TOUS) par défaut. Non applicable au rapport de sécurité. |
| Protocole | Protocole de la demande : HTTP ou HTTPs. Non applicable à l’URL principale et au trafic par agent utilisateur dans le rapport Rapports et sécurité. |
| Agrégation | Précision de l’agrégation des données dans chaque ligne : toutes les 5 minutes, toutes les heures et tous les jours. Non applicable au trafic par domaine, à l’URL principale et au trafic par agent utilisateur dans le rapport Rapports et sécurité. |

### <a name="data-in-traffic-by-domain"></a>Données du trafic par domaine

* Domain 
* Nombre total de demandes 
* Taux d'accès au cache 
* Demandes 3XX 
* Demandes 4XX 
* Demandes 5XX 
* ByteTransferredFromEdgeToClient 

### <a name="data-in-traffic-by-location"></a>Données du trafic par localisation

* Emplacement
* TotalRequests
* Request%
* BytesTransferredFromEdgeToClient

### <a name="data-in-usage"></a>Données d’utilisation

Il existe trois rapports dans ce fichier CSV : un pour le protocole HTTP, un pour le protocole HTTPS et un pour le code d’état HTTP. 

Les rapports pour HTTP et HTTPs partagent le même jeu de données. 

* Temps 
* Protocole 
* DataTransferred(bytes) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest 

Rapport pour le code d’état HTTP 

* Temps 
* DataTransferred(bytes) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest

### <a name="data-in-caching"></a>Données de la mise en cache 

* Temps
* CacheHitRatio 
* HitRequests 
* MissRequests 

### <a name="data-in-top-url"></a>Données de l’URL principale 

* URL 
* TotalRequests 
* Request% 
* DataTransferred(bytes) 
* DataTransferred% 

### <a name="data-in-user-agent"></a>Données de l’agent utilisateur 

* UserAgent 
* TotalRequests 
* Request% 
* DataTransferred(bytes) 
* DataTransferred% 

### <a name="security-report"></a>Rapport de sécurité 

Il y a sept tableaux, contenant tous les mêmes champs ci-dessous.  

* BlockedRequests 
* AllowedRequests 
* LoggedRequests 
* RedirectedRequests 
* OWASPRuleRequests 
* CustomRuleRequests 
* BotRequests 

Les sept tableaux concernent l’heure, l’ID de règle, le pays, l’adresse IP, l’URL, le nom d’hôte et l’agent utilisateur. 

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur les [métriques de supervision en temps réel Azure Front Door Standard/Premium](how-to-monitor-metrics.md).
