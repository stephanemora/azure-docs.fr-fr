---
title: Déclencheurs et actions intégrés pour Azure Logic Apps
description: Utilisez des déclencheurs et des actions intégrés pour créer des workflows automatisés qui intègrent des applications, des données, des services et des systèmes, pour contrôler des workflows et pour gérer des données à l’aide d’Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: bfeaf070268dde315858c045bd1b941dc1bdb26a
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108136154"
---
# <a name="built-in-triggers-and-actions-for-logic-apps"></a>Déclencheurs et actions intégrés pour Logic Apps


Les [déclencheurs et actions intégrés](apis-list.md) vous permettent de [contrôler la planification et la structure de votre workflow](#control-workflow), d’[exécuter votre propre code](#run-code-from-workflows), de [gérer ou manipuler des données](#manage-or-manipulate-data), et d’effectuer d’autres tâches dans vos workflows. Différents des [connecteurs managés](managed.md), de nombreuses opérations intégrées ne sont pas liées à un service, un système ou un protocole spécifique. Par exemple, vous pouvez démarrer presque n’importe quel workflow selon une planification à l’aide du déclencheur Récurrence. Vous pouvez aussi faire en sorte que votre workflow attende jusqu’à ce qu’il soit appelé à l’aide du déclencheur Requête. Toutes les opérations intégrées s’exécutent en mode natif dans le service Logic Apps, et la plupart ne nécessitent pas la création d’une connexion avant leur utilisation. 

Logic Apps fournit également des opérations intégrées pour certains services, systèmes et protocoles, comme Azure Functions, Azure App Services, Gestion des API Azure, AS2, etc. Cette sélection varie selon que vous créez une application logique multilocataire ou à locataire unique. Par exemple, si vous créez une application logique à locataire unique, les opérations intégrées sont disponibles pour Azure Service Bus, Azure Event Hubs, SQL Server et MQ. Dans certains cas, une version intégrée et une version de connecteur managé sont toutes deux disponibles. Dans la plupart des cas, la version intégrée offre de meilleures performances, fonctionnalités, conditions tarifaires, etc. Par exemple, pour [échanger des messages B2B à l’aide du protocole AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), sélectionnez la version intégrée, sauf si vous avez besoin de fonctionnalités de suivi, qui sont disponibles uniquement dans la version du connecteur managé (dépréciée).

La liste suivante décrit quelques-unes des tâches que vous pouvez effectuer avec des [déclencheurs et actions intégrés](#understand-triggers-and-actions) :

- Exécuter des workflows selon des planifications personnalisées et avancées. Pour plus d’informations sur la planification, consultez la [section sur le comportement lié à la périodicité dans la vue d’ensemble des connecteurs Logic Apps](apis-list.md#recurrence-behavior).
- Organiser et contrôler la structure de votre workflow, par exemple à l’aide de boucles et de conditions
- Utiliser des variables, des dates, des opérations de données, des transformations de contenu et des opérations par lots
- Communiquer avec d’autres points de terminaison à l’aide de déclencheurs et d’actions HTTP
- Recevoir des requêtes et y répondre
- Appeler vos propres fonctions (Azure Functions), applications web (Azure App Services), API (Gestion des API Azure), autres workflows Logic Apps qui peuvent recevoir des requêtes, et ainsi de suite.

## <a name="understand-triggers-and-actions"></a>Présentation des déclencheurs et actions

Logic Apps fournit les déclencheurs et actions intégrés suivants :

:::row:::
    :::column:::
        [![Icône de planification dans Logic Apps][schedule-icon]][schedule-doc]
        \
        \
        [**Planification**][schedule-doc]
        \
        \
        [**Récurrence**][schedule-recurrence-doc] : déclencher un workflow en fonction de la périodicité spécifiée.
        \
        \
        [**Fenêtre glissante**][schedule-sliding-window-doc] : déclencher un workflow qui doit gérer les données en blocs continus.
        \
        \
        [**Différer**][schedule-delay-doc] : suspendre votre workflow pendant la durée spécifiée.
        \
        \
        [**Différer jusqu’à**][schedule-delay-until-doc] : suspendre votre workflow jusqu’à la date et l’heure spécifiées.
    :::column-end:::
    :::column:::
        [![Icône de lot dans Logic Apps][batch-icon]][batch-doc]
        \
        \
        [**Traitement par lot**][batch-doc]
        \
        \
        [**Traiter les messages par lots**][batch-doc] : déclencher un workflow qui traite les messages par lots.
        \
        \
        [**Envoyer les messages au lot**][batch-doc] : appeler un workflow existant qui démarre actuellement avec un déclencheur **Traiter les messages par lots**.
    :::column-end:::
    :::column:::
        [![Icône HTTP dans Logic Apps][http-icon]][http-doc]
        \
        \
        [**HTTP**][http-doc]
        \
        \
        Appeler un point de terminaison HTTP ou HTTPS à l’aide du déclencheur ou de l’action HTTP. 
        \
        \
        Vous pouvez également utiliser ces autres déclencheurs et actions HTTP intégrés : 
        - [HTTP + Swagger][http-swagger-doc]
        - [HTTP + Webhook][http-webhook-doc]
    :::column-end:::
    :::column:::
        [![Icône de requête][http-request-icon]][http-request-doc]
        \
        \
        [**Requête**][http-request-doc]
        \
        \
        [**Lors de la réception d’une demande HTTP**][http-request-doc] : attendre une requête d’un autre workflow, application ou service. Ce déclencheur permet à votre workflow d’être appelé sans avoir à être vérifié ou interrogé selon une planification.
        \
        \
        [**Réponse**][http-request-doc] : répondre à une requête reçue par le déclencheur **Lors de la réception d’une demande HTTP** dans le même workflow.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icône Gestion des API Azure dans Logic Apps][azure-api-management-icon]][azure-api-management-doc]
        \
        \
        [**Gestion des API Azure**][azure-api-management-doc]
        \
        \
        Appelez vos propres déclencheurs et actions dans des API que vous définissez, gérez et publiez à l’aide de [Gestion des API Azure](../api-management/api-management-key-concepts.md). <p><p>**Remarque** : Ceci n’est pas pris en charge lors de l’utilisation du [niveau Consommation pour Gestion des API](../api-management/api-management-features.md).
    :::column-end:::
    :::column:::
        [![Icône Azure App Services dans Logic Apps][azure-app-services-icon]][azure-app-services-doc]
        \
        \
        [**Azure App Services**][azure-app-services-doc]
        \
        \
        Appelez des applications que vous créez et hébergez sur [Azure App Services](../app-service/overview.md), par exemple API Apps et Web Apps.
        \
        \
        Lorsque Swagger est inclus, les déclencheurs et les actions définis par ces applications apparaissent comme les autres déclencheurs et actions de première classe dans Azure Logic Apps.
    :::column-end:::
    :::column:::
        [![Icône Azure Logic Apps dans Logic Apps][azure-logic-apps-icon]][nested-logic-app-doc]
        \
        \
        [**Azure Logic Apps**][nested-logic-app-doc]
        \
        \
        Appelez d’autres workflows qui démarrent avec le déclencheur Requête nommé **Lors de la réception d’une demande HTTP**.
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="run-code-from-workflows"></a>Exécuter du code à partir de workflows

Logic Apps fournit des actions intégrées vous permettant d’exécuter votre propre code dans votre workflow :

:::row:::
    :::column:::
        [![Icône Azure Functions dans Logic Apps][azure-functions-icon]][azure-functions-doc]
        \
        \
        [**Azure Functions**][azure-functions-doc]
        \
        \
        Appelez des [fonctions hébergées par Azure](../azure-functions/functions-overview.md) pour exécuter vos propres *extraits de code* (C# ou Node.js) dans votre workflow.
    :::column-end:::
    :::column:::
        [![Icône de code inclus dans Logic Apps][inline-code-icon]][inline-code-doc]
        \
        \
        [**Code inclus**][inline-code-doc]
        \
        \
        [**Exécuter du code JavaScript**][inline-code-doc] : ajoutez et exécutez vos propres *extraits de code* JavaScript inline dans votre workflow.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="control-workflow"></a>Contrôler le flux de travail

Logic Apps fournit des actions intégrées qui permettent de structurer et de contrôler les actions de votre workflow :

:::row:::
    :::column:::
        [![Icône d’action de condition dans Logic Apps][condition-icon]][condition-doc]
        \
        \
        [**Condition**][condition-doc]
        \
        \
        Évaluez une condition et exécutez différentes actions selon que la condition est true ou false.
    :::column-end:::
    :::column:::
        [![Icône d’action For Each dans Logic Apps][for-each-icon]][for-each-doc]
        \
        \
        [**For Each**][for-each-doc]
        \
        \
        Effectuez les mêmes actions sur chaque élément dans un tableau.
    :::column-end:::
    :::column:::
        [![Icône d’action d’étendue dans Logic Apps][scope-icon]][scope-doc]
        \
        \
        [**Nom**][scope-doc]
        \
        \
        Regroupez les actions en *étendues*, qui obtiennent leur propre état à la fin de l’exécution des actions dans l’étendue.
    :::column-end:::
    :::column:::
        [![Icône d’action Switch dans Logic Apps][switch-icon]][switch-doc]
        \
        \
        [**Switch**][switch-doc]
        \
        \
        Regroupez les actions en *cas*, auxquels sont affectées des valeurs uniques à l’exception du cas par défaut. Exécutez uniquement le cas dont la valeur affectée correspond au résultat d’une expression, d’un objet ou d’un jeton. Si aucune correspondance n’existe, exécutez le cas par défaut.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icône d’action Terminate dans Logic Apps][terminate-icon]][terminate-doc]
        \
        \
        [**Terminate**][terminate-doc]
        \
        \
        Arrêtez un flux de travail d’application logique qui fonctionne activement. 
    :::column-end:::
    :::column:::
        [![Icône d’action Until dans Logic Apps][until-icon]][until-doc]
        \
        \
        [**Until**][until-doc]
        \
        \
        Répétez les actions jusqu’à ce que la condition spécifiée ait la valeur true ou qu’un état ait changé.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="manage-or-manipulate-data"></a>Gérer ou manipuler des données

Logic Apps fournit des actions intégrées qui permettent de manipuler les sorties de données et leur format :

:::row:::
    :::column:::
        [![Icône d’action Opérations de données dans Logic Apps][data-operations-icon]][data-operations-doc]
        \
        \
        [**Opérations de données**][data-operations-doc]
        \
        \
        Effectuez des opérations avec les données. 
        \
        \
        **Composer** : créez une sortie unique à partir de plusieurs entrées avec différents types. 
        \
        \
        **Créer un tableau CSV** : créez un tableau CSV (valeurs séparées par des virgules) à partir d’un tableau avec des objets JSON. 
        \
        \
        **Créer un tableau HTML** : créez un tableau HTML à partir d’un tableau avec des objets JSON. 
        \
        \
        **Filtrer un tableau** : créez un tableau à partir des éléments d’un autre tableau qui correspondent à vos critères. 
        \
        \
        **Joindre** : créez une chaîne à partir de tous les éléments d’un tableau et séparez ces éléments à l’aide du séparateur spécifié. 
        \
        \
        **Analyser JSON** : Créez des jetons conviviaux à partir des propriétés et de leurs valeurs dans le contenu JSON afin de pouvoir utiliser ces propriétés dans votre workflow. 
        \
        \
        **Select** : créez un tableau avec des objets JSON en transformant les éléments ou les valeurs d’un autre tableau et en mappant ces éléments sur les propriétés spécifiées.
    :::column-end:::
    :::column:::
        ![Icône d’action Date et heure dans Logic Apps][date-time-icon]
        \
        \
        **Date et heure**
        \
        \
        Effectuez des opérations avec des horodatages.
        \
        \
        **Ajouter à l’heure** : ajoutez le nombre spécifié d’unités à un timestamp. 
        \
        \
        **Convertir le fuseau horaire** : Convertit un horodatage du fuseau horaire source au fuseau horaire cible. 
        \
        \
        **Heure actuelle** : Renvoyer le timestamp actuel sous forme de chaîne. 
        \
        \
        **Obtenir l’heure future** : Retourne l’horodatage actuel plus les unités de temps spécifiées. 
        \
        \
        **Obtenir l’heure passée** : Retourne l’horodatage actuel moins les unités de temps spécifiées. 
        \
        \
        **Soustraire de l’heure** : soustrayez un nombre d’unités de temps d’un horodatage.
    :::column-end:::
    :::column:::
        [![Icône d’action Variables dans Logic Apps][variables-icon]][variables-doc]
        \
        \
        [**Variables**][variables-doc]
        \
        \
        Effectuez des opérations avec des variables.
        \
        \
        **Ajouter à une variable tableau** : insérez une valeur en tant que dernier élément dans un tableau stocké par une variable. 
        \
        \
        **Ajouter à une variable chaîne** : insérez une valeur en tant que dernier caractère dans une chaîne stockée par une variable.
        \
        \
        **Décrémenter une variable** : diminuez une variable d’une valeur constante.
        \
        \
        **Incrémenter une variable** : augmentez une variable d’une valeur constante. 
        \
        \
        **Initialiser la variable** : créez une variable et déclarez son type de données et sa valeur initiale. 
        \
        \
        **Définir une variable** : attribuez une autre valeur à une variable existante. 
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer des API personnalisées que vous pouvez appeler à partir de Logic Apps](/logic-apps/logic-apps-create-api-app)

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png


<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Créer une instance du service Gestion des API Azure pour gérer et publier vos API"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-api-host-deploy-call.md "Permet d’intégrer des applications logiques à App Service API Apps"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Permet d’intégrer des applications logiques à Azure Functions"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Traiter les messages en groupes ou sous forme de lots"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Évaluer une condition et exécuter différentes actions selon que la condition est true ou false"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Effectuer les mêmes actions sur chaque élément dans un tableau"
[http-doc]: ./connectors-native-http.md "Appeler des points de terminaison HTTP ou HTTPS à partir de vos applications logiques"
[http-request-doc]: ./connectors-native-reqres.md "Recevoir des requêtes HTTP dans vos applications logiques"
[http-response-doc]: ./connectors-native-reqres.md "Répondre aux requêtes HTTP à partir de vos applications logiques"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Appeler des points de terminaison REST à partir de vos applications logiques"
[http-webhook-doc]: ./connectors-native-webhook.md "Attendre des événements à partir de points de terminaison HTTP ou HTTPS"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md "Ajouter et exécuter des extraits de code JavaScript à partir de vos applications logiques"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Intégrer des applications logiques à des flux de travail imbriqués"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Sélectionner et filtrer des tableaux avec l’action de requête"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Exécuter des applications logiques selon une planification"
[schedule-delay-doc]: ./connectors-native-delay.md "Retarder l’exécution de l’action suivante"
[schedule-delay-until-doc]: ./connectors-native-delay.md "Retarder l’exécution de l’action suivante"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Exécuter des applications logiques selon une planification périodique"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Exécuter des applications logiques qui doivent gérer des données en blocs contigus"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organiser les actions en groupes, qui obtiennent leur propre état à la fin de l’exécution des actions dans le groupe"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organiser les actions en cas, auxquels sont affectées des valeurs uniques. Exécuter uniquement le cas dont la valeur correspond au résultat d’une expression, d’un objet ou d’un jeton. Si aucune correspondance n’existe, exécuter le cas par défaut"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Arrêter ou annuler un flux de travail qu fonctionne activement pour votre application logique"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Répéter les actions jusqu’à ce que la condition spécifiée ait la valeur true ou qu’un état ait changé"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Effectuer des opérations avec les données telles que le filtrage de tableaux ou la création de tableaux CSV et HTML"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Effectuer des opérations avec des variables, comme initialiser, définir, incrémenter, décrémenter et ajouter à une variable de chaîne ou de tableau"
