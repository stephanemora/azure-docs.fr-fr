---
title: Intégrer la Gestion des API Azure avec Azure Application Insights
titleSuffix: Azure API Management
description: Découvrez comment consigner et afficher des événements de la Gestion des API Azure dans Azure Application Insights.
services: api-management
author: mikebudzynski
ms.service: api-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2021
ms.author: apimpm
ms.openlocfilehash: 97f4eb34b88b3454d65b65d236833e1256c98671
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103564249"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Guide pratique pour intégrer la Gestion des API Azure avec Azure Application Insights

La Gestion des API Azure est parfaitement compatible avec Azure Application Insights, un service extensible permettant aux développeurs web de créer et de gérer des applications sur de multiples plateformes. Ce guide décrit la marche à suivre pour effectuer cette intégration, ainsi que des stratégies visant à réduire l’impact sur le fonctionnement de l’instance de service Gestion des API.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide, vous devrez disposer d’une instance de Gestion des API Azure. Si vous n’en avez pas, suivez d’abord le [tutoriel](get-started-create-service-instance.md).

## <a name="create-an-application-insights-instance"></a>Créer une instance Application Insights

Pour pouvoir utiliser Application Insights, il faut commencer par créer une instance du service. Pour connaître les étapes de création d’une instance à l’aide du portail Azure, consultez [Ressources Application Insights basées sur l’espace de travail](../azure-monitor/app/create-workspace-resource.md).
## <a name="create-a-connection-between-application-insights-and-api-management"></a>Créer une connexion entre Application Insights et API Management

1. Accédez à votre **instance de service Gestion des API Azure** sur le **Portail Azure**.
1. Sélectionnez **Application Insights** dans le menu de gauche.
1. Cliquez sur **+ Ajouter**.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-1.png" alt-text="Capture d’écran montrant où ajouter une nouvelle connexion":::
1. Sélectionnez l’instance **Application Insights** créée et indiquez une courte description.
1. Cliquez sur **Créer**.
1. Vous venez de créer un enregistreur d’événements Application Insights avec une clé d’instrumentation. Il devrait maintenant apparaître dans la liste.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-2.png" alt-text="Capture d’écran montrant où afficher l’enregistreur d’événements Application Insights nouvellement créé avec la clé d’instrumentation":::

> [!NOTE]
> En arrière-plan, une entité [Enregistreur d’événements](/rest/api/apimanagement/2019-12-01/logger/createorupdate) est créée dans votre instance de gestion des API, qui contient la clé d’instrumentation de l’instance Application Insights.

## <a name="enable-application-insights-logging-for-your-api"></a>Activer la journalisation Application Insights pour l’API

1. Accédez à votre **instance de service Gestion des API Azure** sur le **Portail Azure**.
1. Sélectionnez **API** dans le menu de gauche.
1. Cliquez sur votre API, dans ce cas **Demo Conference API**. Si elle est configurée, sélectionnez une version.
1. Accédez à l’onglet **Paramètres** de la barre supérieure.
1. Descendez jusqu’à la section **Journaux de diagnostic**.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-api-1.png" alt-text="Enregistreur d'événements App Insights":::
1. Cochez la case **Activer**.
1. Sélectionnez l’enregistreur d'événements joint dans la liste déroulante **Destination**.
1. Entrez **100** pour **Échantillonnage (%)** et sélectionnez la case **Toujours consigner les erreurs**.
1. Sélectionnez **Enregistrer**.

> [!WARNING]
> Le remplacement de la valeur par défaut **0** dans le paramètre **Nombre d’octets de charge utile à consigner** peut réduire considérablement les performances de vos API.

> [!NOTE]
> En arrière-plan, une entité [Diagnostic](/rest/api/apimanagement/2019-12-01/diagnostic/createorupdate) nommée « applicationinsights » est créée au niveau de l’API.

| Nom du paramètre                        | Type de valeur                        | Description                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Activer                              | boolean                           | Indique si la journalisation de cette API est activée.                                                                                                                                                                                                                                                                                                |
| Destination                         | Enregistreur d'événements Azure Application Insights | Indique l’enregistreur d'événements Azure Application Insights utilisé.                                                                                                                                                                                                                                                                                           |
| Échantillonnage (%)                        | Décimal                           | Valeurs comprises entre 0 et 100 (%). <br/> Spécifie le pourcentage de requêtes qui seront consignées dans Application Insights. Un échantillonnage à 0 % signifie qu’aucune demande ne sera consignée, tandis qu’un échantillonnage à 100 % indique qu’elles le seront toutes. <br/> Utilisez ce paramètre pour réduire l’effet sur les performances lors de la journalisation des demandes sur Application Insights. Consultez [Conséquences sur les performances et échantillonnage des journaux](#performance-implications-and-log-sampling). |
| Toujours consigner les erreurs                   | boolean                           | Si ce paramètre est sélectionné, toutes les défaillances seront consignées dans Application Insights, quel que soit le paramètre **Échantillonnage**.   
| Adresse IP du client du journal | |  Si ce paramètre est sélectionné, l’adresse IP du client pour les requêtes d’API sera enregistrée dans Application Insights.                                         |
| Commentaires         |                                   | Spécifie le niveau de verbosité. Seules les traces personnalisées avec un niveau de gravité supérieur sont journalisées. Valeur par défaut : Information.      | 
| Protocole de corrélation |  |  Sélectionnez le protocole utilisé pour mettre en corrélation les données de télémétrie envoyées par plusieurs composants. Valeur par défaut : hérité <br/>Pour plus d’informations, consultez l’article [Corrélation de télémétrie dans Application Insights](../azure-monitor/app/correlation.md).  |
| Options de base : En-têtes à consigner              | list                              | Précise quels en-têtes seront consignés dans Application Insights pour les demandes et les réponses.  Par défaut, aucun en-tête n’est consigné.                                                                                                                                                                                                             |
| Options de base : nombre d’octets de charge utile à enregistrer dans le journal | entier                           | Indique combien de premiers octets du corps seront consignés dans Application Insights pour les demandes et les réponses.  Valeur par défaut : 0.                                                                                                                                                                                                    |
| Options avancées : Demande du serveur frontal  |                                   | Précise si des *demandes frontales* seront consignées dans Application Insights et, si oui, comment. Une *demande frontale* est une demande entrante dans le service Gestion des API Azure.                                                                                                                                                                        |
| Options avancées : Réponse du serveur frontal |                                   | Précise si des *réponses frontales* seront consignées dans Application Insights et, si oui, comment. Une *réponse frontale* est une réponse sortante du service Gestion des API Azure.                                                                                                                                                                   |
| Options avancées : Demande principale   |                                   | Précise si des *demandes principales* seront consignées dans Application Insights et, si oui, comment. Une *demande principale* est une demande sortante du service Gestion des API Azure.                                                                                                                                                                        |
| Options avancées : Réponse principale  |                                   | Précise si des *réponses principales* seront consignées dans Application Insights et, si oui, comment. Une *réponse principale* est une réponse entrante dans le service Gestion des API Azure.                                                                                                                                                                       |

> [!NOTE]
> Il est possible de spécifier des enregistreurs d'événements à différents niveaux : pour une unique API ou pour toutes les API.
>  
> Si vous spécifiez les deux :
> + S’ils sont différents, les deux seront utilisés (journaux d’activité de multiplexage).
> + Si ce sont les mêmes avec des paramètres différents, celui de l’API unique (niveau de détail supérieur) aura la priorité sur celui de l’ensemble des API.

## <a name="what-data-is-added-to-application-insights"></a>Données ajoutées à Application Insights

Application Insights reçoit :

+ un élément de télémétrie *Demande* pour chaque demande entrante (*demande frontale*, *réponse frontale*) ;
+ un élément de télémétrie *Dépendance* pour chaque demande transmise à un service principal (*demande principale*, *réponse principale*) ;
+ Un élément de télémétrie *Exception* pour chaque demande ayant échoué :
    + a échoué en raison d’une interruption de la connexion cliente
    + a déclenché une section *on-error* des stratégies d’API
    + a un code de statut HTTP de réponse de type 4xx ou 5xx.
+ *Tracez* l’élément de télémétrie, si vous configurez une stratégie [trace](api-management-advanced-policies.md#Trace). Le paramètre `severity` de la stratégie `trace` doit être supérieur ou égal au paramètre `verbosity` de journalisation d’Application Insights.

> [!NOTE]
> Consultez [Limites d’Application Insights](../azure-monitor/service-limits.md#application-insights) pour plus d’informations sur la taille maximale et le nombre d’événements et de métriques par instance d’Application Insights.

## <a name="performance-implications-and-log-sampling"></a>Conséquences sur les performances et échantillonnage des journaux

> [!WARNING]
> La journalisation de l’ensemble des événements peut avoir de grosses répercussions sur les performances en fonction du rythme des demandes entrantes.

Lors de tests de charge internes, cette fonctionnalité a provoqué une baisse du débit de 40 %-50 % lorsque la fréquence des demandes a dépassé 1 000 par seconde. Application Insights est conçu pour utiliser l’analyse statistique dans le but d’évaluer les performances des applications. Il n’est ni destiné à servir de système d’audit ni adapté à la journalisation de la totalité des demandes dans le cas d’API générant de gros volumes.

Pour modifier le nombre de demandes consignées, réglez le paramètre **Échantillonnage** (voir les étapes précédentes). Une valeur de 100 % signifie que toutes les demandes sont consignées, tandis que le 0 % indique une absence de journalisation. **L’échantillonnage** permet de réduire le volume de télémétrie, ce qui se révèle efficace en prévention d’une dégradation significative des performances tout en conservant les avantages de la journalisation.

Il est également utile d’ignorer la journalisation des en-têtes et du corps des demandes et des réponses pour limiter les problèmes de performances.

## <a name="video"></a>Vidéo

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Étapes suivantes

+ Découvrez plus en détail [Azure Application Insights](/azure/application-insights/).
+ Étudiez la possibilité de [journalisation avec Azure Event Hubs](api-management-howto-log-event-hubs.md).
