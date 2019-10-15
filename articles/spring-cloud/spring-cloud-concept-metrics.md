---
title: Présentation des métriques pour Azure Spring Cloud
description: Découvrir comment passer en revue les métriques dans Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d9c6f14b7ecfc2929dc48c11e0df1fe80303c8b1
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038498"
---
# <a name="metrics-for-azure-spring-cloud"></a>Métriques pour Azure Spring Cloud

Azure Monitor Metrics Explorer est un composant du portail Microsoft Azure qui permet de tracer des graphiques, de corréler visuellement des tendances et d’investiguer sur les pics et les creux des métriques. Utilisez l’explorateur de métriques pour examiner l’intégrité et l’utilisation de vos ressources. Dans Azure Spring Cloud, nous proposons deux options pour l’affichage des métriques : des graphiques dans la page **Vue d’ensemble de l’application** et la page Métriques au niveau du service.

## <a name="application-overview-page"></a>Page Vue d’ensemble de l’application

La page **Vue d’ensemble de l’application** de chaque application, présente un graphique des métriques qui vous permet d’effectuer une vérification rapide de l’état de votre application.  Accédez à la page de votre service Azure Spring Cloud, sélectionnez **Tableau de bord de l’application**, puis sélectionnez une application dans la liste.  

Nous fournissons 5 graphiques avec des métriques mises à jour toutes les minutes pour les éléments suivants :

* **Erreurs de serveur Http** : Nombre d’erreurs pour les requêtes HTTP adressées à votre application.
* **Données entrantes** : Octets reçus par votre application.
* **Données sortantes** : Octets envoyés à votre application.
* **Requêtes** : Requêtes reçues par votre application.
* **Temps de réponse moyen** : Temps de réponse moyen de votre application.

Vous pouvez sélectionner un intervalle de temps pour le graphique entre 1 heure et 7 jours.

## <a name="service-level-metric-queries"></a>Requêtes sur les métriques au niveau du service

Azure Spring Cloud vous permet de superviser différentes métriques d’application. Pour plus d’informations sur ce service, passez en revue le [guide de prise en main](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) des métriques d’Azure Monitor.

Pour passer en revue les données des métriques, vous sélectionnez votre métrique, votre **agrégation** et votre intervalle de temps.  Ces concepts sont expliqués ci-dessous.

### <a name="aggregation"></a>Agrégation 

Azure interroge et met à jour les métriques toutes les minutes. Azure fournit trois moyens d’agréger les données pour une période de temps choisie :

* **Total** : Additionne toutes les métriques comme résultat cible.
* **Average** : Utilise la valeur moyenne de la période comme résultat cible.
* **Max/Min** : Utilise la valeur maximale/minimale de la période comme résultat cible.

### <a name="time-range"></a>Période

Sélectionnez un intervalle de temps par défaut ou définissez le vôtre.

### <a name="modifying-the-granularity-of-your-metric-query"></a>Modification de la précision de votre requête sur les métriques

Par défaut, Azure agrège les métriques pour toutes les applications du service Azure Spring Cloud. Pour passer en revue les métriques au niveau de l’application ou de l’instance, utilisez la fonction Filtrer.  
Sélectionnez **Ajouter un filtre**, définissez la propriété sur **Application**, puis sélectionnez l’application cible que vous voulez superviser. Si vous le souhaitez, utilisez **Appliquer une séparation** pour dessiner des lignes distinctes pour chaque application dans le graphique.

>[!TIP]
> Vous pouvez créer vos propres graphiques dans la page des métriques et les épingler à votre **tableau de bord**. Commencez par nommer votre graphique.  Ensuite, sélectionnez **Épingler au tableau de bord dans le coin supérieur droit**. Vous pouvez maintenant vérifier votre application sur votre **tableau de bord** du portail.