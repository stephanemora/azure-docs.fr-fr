---
title: Superviser les applications Python avec Azure Monitor (préversion) | Microsoft Docs
description: Fournit des instructions permettant de connecter OpenCensus Python à Azure Monitor
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: ed61cb1bc88c48fe89c4a9390f04747749bd48c5
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329475"
---
# <a name="set-up-azure-monitor-for-your-python-application-preview"></a>Configurer Azure Monitor pour votre application Python (préversion)

Azure Monitor prend en charge le traçage distribué, la collecte de métriques et la journalisation des applications Python via l’intégration à [OpenCensus](https://opencensus.io). Cet article va vous guider pas à pas dans le processus de configuration d’OpenCensus pour Python, et l’envoi de vos données de supervision vers Azure Monitor.

## <a name="prerequisites"></a>Prérequis

- Vous avez besoin d’un abonnement Azure.
- Python doit être installé. Cet article s’appuie sur [Python 3.7.0](https://www.python.org/downloads/), mais les versions antérieures fonctionnent également avec quelques ajustements mineurs.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="create-application-insights-resource-in-azure-monitor"></a>Créer une ressource Application Insights dans Azure Monitor

Vous devez d’abord créer une ressource Application Insights dans Azure Monitor pour générer une clé d’instrumentation (ikey). Cet ikey permet ensuite de configurer le kit SDK OpenCensus pour qu’il envoie les données de télémétrie à Azure Monitor.

1. Sélectionnez **Créer une ressource** > **Outils de développement** > **Application Insights**.

   ![Ajout d’une ressource Application Insights](./media/opencensus-python/0001-create-resource.png)

   Une boîte de configuration s’affiche. Utilisez le tableau suivant pour remplir les champs d’entrée.

    | Paramètres        | Valeur           | Description  |
   | ------------- |:-------------|:-----|
   | **Nom**      | Valeur globalement unique | Nom identifiant l’application que vous analysez |
   | **Groupe de ressources**     | myResourceGroup      | Nom du nouveau groupe de ressources pour héberger les données Application Insights |
   | **Lieu** | USA Est | Choisissez un emplacement près de chez vous ou proche de l’endroit où votre application est hébergée |

2. Cliquez sur **Créer**.

## <a name="instrumenting-with-opencensus-python-sdk-for-azure-monitor"></a>Instrumentation avec le kit SDK OpenCensus Python pour Azure Monitor

1. Installez les exportateurs OpenCensus Azure Monitor en procédant comme suit :

    ```console
    python -m pip install opencensus-ext-azure
    ```

    > [!NOTE]
    > `python -m pip install opencensus-ext-azure` suppose qu’une variable d’environnement PATH est définie pour votre installation Python. Dans le cas contraire, vous devez fournir le chemin d’accès complet du répertoire contenant votre fichier exécutable Python, dans une commande comme celle-ci : `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

2. Le kit SDK utilise trois exportateurs Azure Monitor pour envoyer différents types de télémétrie à Azure Monitor : traces, métriques et journaux. Pour plus d’informations sur ces différents types, consultez la [vue d’ensemble de la plateforme de données](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform). Suivez les instructions ci-dessous pour voir comment envoyer ces différents types via les trois exportateurs.

### <a name="trace"></a>Trace

1. Nous allons d’abord générer des données de trace en local. Dans IDLE pour Python ou l’éditeur de votre choix, entrez le code suivant.

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2. Lors de l’exécution du code, vous êtes systématiquement invité à saisir une valeur. À chaque saisie, la valeur est imprimée dans l’interpréteur de commandes et un élément **SpanData** correspondant est généré par le module OpenCensus Python. Le projet OpenCensus définit une [_trace comme une arborescence d’étendues_](https://opencensus.io/core-concepts/tracing/).
    
    ```
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

3. Bien que cela s’avère utile à des fins de démonstration, nous souhaitons surtout émettre `SpanData` vers Azure Monitor. Modifiez le code de l’étape précédente sur la base de l’exemple de code suivant :

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        ),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

4. Maintenant, lorsque vous exécutez le script Python, vous êtes à nouveau invité à saisir une valeur, mais cette valeur est la seule à être imprimée dans l’interpréteur de commandes. Le `SpanData` créé va être envoyé à Azure Monitor. Vous pouvez trouver les données d’unités de travail émises sous `dependencies`.

### <a name="metrics"></a>Mesures

1. Commençons par générer des données de métrique locales. Nous allons créer une métrique simple pour suivre le nombre de fois où l’utilisateur appuie sur la touche Entrée.

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
2. Durant l’exécution du code, vous êtes invité à plusieurs reprises à appuyer sur Entrée. Une métrique est créée pour suivre le nombre de fois où vous avez appuyé sur Entrée. À chaque fois que vous appuyez sur Entrée, la valeur est incrémentée et les informations de métrique sont affichées dans la console, avec la valeur actuelle et l’horodatage actuel au moment de la mise à jour de la métrique.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Bien que cela s’avère utile à des fins de démonstration, nous souhaitons surtout émettre les données de métrique vers Azure Monitor. Modifiez le code de l’étape précédente sur la base de l’exemple de code suivant :

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

4. L’exportateur envoie les données de métrique vers Azure Monitor à un intervalle fixe, la valeur par défaut étant toutes les 15 secondes. Nous effectuons le suivi d’une seule métrique. Ainsi, les données de métrique ainsi que leurs valeurs et leurs horodatages sont envoyés à chaque intervalle. Vous pouvez trouver les données sous `customMetrics`.

### <a name="logs"></a>Journaux d’activité

1. Commençons par générer des données de journal locales.

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2.  Le code va vous demander continuellement d’entrer une valeur. Une entrée de journal est émise pour chaque valeur entrée contenant la valeur en question.

    ```
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

3. Bien que cela s’avère utile à des fins de démonstration, nous souhaitons surtout émettre les données de métrique vers Azure Monitor. Modifiez le code de l’étape précédente sur la base de l’exemple de code suivant :

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

4. L’exportateur envoie les données de journal à Azure Monitor. Vous pouvez trouver les données sous `traces`.

## <a name="start-monitoring-in-the-azure-portal"></a>Démarrer l’analyse dans le portail Azure

1. Vous pouvez à présent rouvrir la page de **présentation** d’Application Insights dans le portail Azure afin d’afficher les détails sur votre application en cours d’exécution. Sélectionnez **Flux de métriques en temps réel**.

   ![Capture d’écran du volet Vue d’ensemble avec Flux de métriques en temps réel sélectionné dans l’encadré rouge](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. Revenez dans la page **Vue d’ensemble** et sélectionnez **Cartographie d’application** pour obtenir une présentation visuelle des relations de dépendance et de la durée des appels entre les composants de votre application.

    ![Capture d’écran d’une cartographie d’application de base](./media/opencensus-python/0007-application-map.png)

    Étant donné que nous n’avons suivi qu’un seul appel de méthode, notre cartographie d’application n’est pas aussi intéressante. Mais vous pouvez la mettre à l’échelle pour visualiser des applications beaucoup plus distribuées :

   ![Mise en correspondance d'applications](media/opencensus-python/application-map.png)

3. Sélectionnez **Examiner les performances** pour effectuer une analyse détaillée des performances et déterminer la cause racine du ralentissement des performances.

    ![Capture d’écran du volet des performances](./media/opencensus-python/0008-performance.png)

4. En sélectionnant **Exemples** et en cliquant sur l’un des exemples qui apparaît dans le volet de droite, vous lancez l’expérience Détails de la transaction de bout en bout. Notre exemple d’application ne montre qu’un seul événement, mais une application plus complexe vous permettrait d’explorer la transaction de bout en bout jusqu’au niveau de la pile des appels d’un événement individuel.

     ![Capture d’écran de l’interface de la transaction de bout en bout](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="view-your-data-with-queries"></a>Voir vos données avec des requêtes

1. Vous pouvez voir les données de télémétrie envoyées à partir de votre application via l’onglet Journaux (Analytics).

    ![Capture d’écran du volet de vue d’ensemble, où Journaux (Analytics) est sélectionné dans l’encadré rouge](./media/opencensus-python/0010-logs-query.png)

2. Pour la télémétrie envoyée avec l’exportateur de traces Azure Monitor, les demandes entrantes s’affichent sous `requests`, et les demandes sortantes/en cours s’affichent sous `dependencies`.

3. Pour la télémétrie envoyée avec l’exportateur de métriques Azure Monitor, les métriques envoyées s’affichent sous `customMetrics`.

4. Pour la télémétrie envoyée avec l’exportateur de journaux Azure Monitor, les journaux s’affichent sous `traces`, et les exceptions s’affichent sous `exceptions`.

5. Consultez [Journaux dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) pour obtenir des informations plus détaillées sur l’utilisation des requêtes et des journaux.

## <a name="opencensus-for-python"></a>OpenCensus pour Python

* [OpenCensus Python sur GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Personnalisation](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Intégration de Flask](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Intégration de Django](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [Intégration de MySQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>Étapes suivantes

* [Récapitulatif de l’API](./../../azure-monitor/app/api-custom-events-metrics.md)
* [Plan de l’application](./../../azure-monitor/app/app-map.md)
* [Analyse des performances de bout en bout](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Alertes

* [Tests de disponibilité](../../azure-monitor/app/monitor-web-app-availability.md) : créez des tests pour vous assurer que votre site est visible sur le web.
* [Diagnostics intelligents](../../azure-monitor/app/proactive-diagnostics.md) : ces tests s’exécutent automatiquement, sans que vous n’ayez rien à faire pour les configurer. Ils vous indiquent si votre application affiche un taux inhabituel de demandes ayant échoué.
* [Alertes de métriques](../../azure-monitor/app/alerts.md) : Définissez des alertes qui vous avertissent si un indicateur de performance dépasse un seuil. Vous pouvez définir des mesures personnalisées que vous codez dans votre application.