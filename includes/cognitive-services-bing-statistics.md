---
title: Fichier Include
description: Fichier Include
services: cognitive-services
author: scottwhi-msft
ms.service: cognitive-services
ms.topic: include
ms.date: 04/09/2018
ms.author: scottwhi
ms.custom: include file
ms.openlocfilehash: a8d588b186652ab86ee1e8152bd9be08f0f1ef04
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52978680"
---
Bing Statistiques fournit l’analytique pour les API Recherche Bing. L’analytique inclut le volume d’appels, les principales chaînes de requête, la répartition géographique, et bien plus encore. Pour activer Bing Statistiques dans votre abonnement payant Recherche Bing, accédez à votre [Tableau de bord Azure](https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7), sélectionnez votre abonnement payant, puis cliquez sur Enable Bing Statistics (Activer Bing Statistiques). L’activation de Bing Statistiques augmente légèrement le prix de votre abonnement (voir [Tarification](https://aka.ms/bingstatisticspricing)).

> [!NOTE]
> Bing Statistiques est disponible uniquement avec des abonnements payants, pas avec des abonnements d’essai gratuit.

> [!NOTE]
> Vous ne pouvez pas utiliser de données disponibles via le tableau de bord Bing statistiques pour créer des applications en vue de les distribuer à des tiers.

Bing met à jour les données d’analytique toutes les 24 heures et conserve un historique pendant jusqu’à 13 mois.

## <a name="accessing-your-analytics"></a>Accès à votre analytique

Pour consulter votre tableau de bord d’analyse, accédez à https://bingapistatistics.com. Assurez-vous que vous êtes connecté à l’aide du compte Microsoft (MSA) que vous avez utilisé pour obtenir votre abonnement payant.

## <a name="filtering-the-data"></a>Filtrage des données

Par défaut, les graphiques et diagrammes reflètent toutes les données de métriques auxquelles vous avez accès. Vous pouvez filtrer les données affichées dans les graphiques et diagrammes en sélectionnant les ressources, les marchés, les points de terminaison et la période qui vous intéressent. Les graphiques et diagrammes changent pour refléter les filtres que vous appliquez. La section suivante décrit les filtres que vous pouvez modifier.

- **ID de ressource** : ID de ressource unique identifiant votre abonnement Azure. La liste contient plusieurs ID si vous vous abonnez à plusieurs niveaux d’API Recherche Bing. Par défaut, toutes les ressources sont sélectionnées.  
  
- **Marchés** : marchés d’où proviennent les résultats. Par exemple, en-us (anglais, États-Unis). Par défaut, tous les objets sont sélectionnés. Notez que le marché en-WW est le marché que Bing utilise si l’appel ne spécifie pas de marché et si Bing est incapable de déterminer le marché de l’utilisateur.  
  
- **Points de terminaison** : points de terminaison de l’API Recherche Bing. La liste contient tous les points de terminaison pour lesquels vous avez un abonnement payant. Par défaut, tous les points de terminaison sont sélectionnés.  

- **Délai d’exécution** : période de rapport. Vous pouvez spécifier les valeurs suivantes :
  - Tous&mdash;Inclut jusqu’à 13 mois de données  
  - Dernières 24 heures&mdash;Inclut l’analytique des dernières 24 heures  
  - Semaine dernière&mdash;Inclut l’analytique des sept derniers jours  
  - Mois précédent&mdash;Inclut l’analytique des 30 derniers jours  
  - Plage de dates personnalisée&mdash;Inclut l’analytique de la plage de dates spécifiée, si elle est disponible  

  > [!NOTE]  
  > Jusqu’à 24 heures peuvent s’écouler avant que les mesures apparaissent sur tableau de bord. Le tableau de bord affiche la date et l’heure de dernière mise à jour des données.  

  > [!NOTE]  
  > Les métriques sont disponibles à partir du moment où vous activez le complément Bing Statistiques.

## <a name="charts-and-graphs"></a>Diagrammes et graphiques

Le tableau de bord affiche les diagrammes et graphiques des métriques disponibles pour le point de terminaison sélectionné. Certaines métriques ne sont pas disponibles pour tous les points de terminaison. Les diagrammes et graphiques pour chaque point de terminaison sont statiques (vous ne pouvez pas choisir de les afficher). Le tableau de bord affiche uniquement les diagrammes et graphiques pour lesquelles il existe des données.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

Les métriques possibles sont les suivantes. Chaque mesure présente des restrictions de point de terminaison.

- **Volume d’appels** : indique le nombre d’appels effectués pendant la période de rapport. Si la période de rapport est d’un jour, le graphique montre le nombre d’appels par heure. Dans le cas contraire, le graphique montre le nombre d’appels effectués par jour durant la période du rapport.  
  
  > [!NOTE]
  > Le volume d’appels peut différer du rapport de facturation, qui n’inclut généralement que les appels réussis.

- **Requêtes les plus longues** : montre les principales requêtes et le nombre d’occurrences de chacune d’elles durant la période du rapport. Vous pouvez configurer le nombre de requêtes affiché. Par exemple, vous pouvez afficher les 25, 50 ou 75 principales requêtes. Les requêtes les plus courantes ne sont pas disponibles pour les points de terminaison suivants :  

  - /images/trending
  - /images/details
  - /images/visualsearch
  - /videos/trending
  - /videos/details
  - /news
  - /news/trendingtopics
  - /suggestions  
  
  > [!NOTE]  
  > Certains termes de requête peuvent être supprimés pour retirer des informations confidentielles telles que des e-mails, des numéros de téléphone, des numéros de sécurité sociale, etc...

- **Répartition géographique** : marchés d’où proviennent les résultats. Par exemple, en-us (anglais, États-Unis). Bing utilise le paramètre de requête `mkt` pour déterminer le marché, s’il est spécifié. Autrement, Bing utilise des signaux tels que l’adresse IP de l’appelant pour déterminer le marché.

- **Distribution du code de réponse** : codes d’état HTTP de tous les appels pendant la période de rapport.

- **Distribution d’origine des appels** : types de navigateurs utilisés par les utilisateurs. Par exemple, Edge, Chrome, Safari et FireFox. Les appels non établis par un navigateur, par exemple par des robots, Postman ou à l’aide de curl à partir d’une application console, sont regroupés sous Bibliothèques. L’origine est déterminée à l’aide de la valeur d’en-tête User-Agent de la demande. Si la demande n’inclut pas l’en-tête User-Agent, Bing tente de dériver l’origine à partir d’autres signaux.  

- **Distribution de recherche sécurisée** : distribution des valeurs de recherche sécurisée. Par exemple, off, moderate ou strict. Le paramètre de requête `safeSearch` contient la valeur, si celle-ci est spécifiée. Dans le cas contraire, la valeur par défaut de Bing est moderate.  

- **Distribution demandée des réponses** : réponses de l’API Recherche web que vous avez demandées dans le paramètre de requête `responseFilter`.  

- **Distribution retournées des réponses** : réponses que l’API Recherche web a retournées dans la réponse.

- **Distribution du serveur réponse** : serveur d’applications ayant servi vos requêtes d’API. Les valeurs possibles sont Bing.com (pour le trafic provenant d’appareils de bureau et portables) et Bing.com-mobile (pour le trafic provenant d’appareils mobiles). Le serveur est déterminé à l’aide de la valeur d’en-tête User-Agent de la demande. Si la demande n’inclut pas l’en-tête User-Agent, Bing tente de dériver le serveur à partir d’autres signaux.

Le tableau suivant montre les analyses disponibles pour chaque point de terminaison.

![Distribution par matrice de prise en charge de point de terminaison](./media/cognitive-services-bing-statistics/bing-statistics-matrix.PNG)
