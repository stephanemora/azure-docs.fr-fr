---
title: Superviser les applications Python avec Azure Monitor (préversion) | Microsoft Docs
description: Fournit des instructions permettant de connecter OpenCensus Python à Azure Monitor
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: b9d2bda1d3f01d2bf4bb152c0f62ade87bb61b4c
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368271"
---
# <a name="set-up-azure-monitor-for-your-python-application-preview"></a>Configurer Azure Monitor pour votre application Python (préversion)

Azure Monitor prend en charge le traçage distribué, la collecte de métriques et la journalisation des applications Python via l’intégration à [OpenCensus](https://opencensus.io). Cet article vous guidera dans le processus de configuration d’OpenCensus pour Python et d’envoi de vos données de supervision vers Azure Monitor.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
- Installation de Python. Cet article s’appuie sur [Python 3.7.0](https://www.python.org/downloads/), mais quelques changements mineurs permettent d’utiliser des versions antérieures.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>Créer une ressource Application Insights dans Azure Monitor

Vous devez d’abord créer une ressource Application Insights dans Azure Monitor pour générer une clé d’instrumentation (ikey). Cet ikey permet ensuite de configurer le kit SDK OpenCensus pour qu’il envoie les données de télémétrie à Azure Monitor.

1. Sélectionnez **Créer une ressource** > **Outils de développement** > **Application Insights**.

   ![Ajout d’une ressource Application Insights](./media/opencensus-python/0001-create-resource.png)

1. Une boîte de configuration apparaît. Utilisez le tableau suivant pour remplir les champs d’entrée.

   | Paramètre        | Valeur           | Description  |
   | ------------- |:-------------|:-----|
   | **Nom**      | Valeur globalement unique | Nom identifiant l’application que vous analysez |
   | **Groupe de ressources**     | myResourceGroup      | Nom du nouveau groupe de ressources pour héberger les données Application Insights |
   | **Lieu** | USA Est | Un emplacement près de chez vous ou proche de l’endroit où votre application est hébergée |

1. Sélectionnez **Create** (Créer).

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Instrumentez à l’aide du Kit de développement logiciel (SDK) OpenCensus Python pour Azure Monitor

Installez les exportateurs OpenCensus Azure Monitor comme suit :

```console
python -m pip install opencensus-ext-azure
```

Pour obtenir la liste complète des packages et intégrations, consultez [Packages OpenCensus](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus).

> [!NOTE]
> La commande `python -m pip install opencensus-ext-azure` suppose qu’une variable d’environnement `PATH` est définie pour votre installation Python. Dans le cas contraire, vous devez fournir le chemin d’accès complet du répertoire contenant votre fichier exécutable Python. Voici un exemple de commande qui en résulte : `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

Le Kit de développement logiciel (SDK) utilise trois exportateurs Azure Monitor pour envoyer différents types de télémétrie à Azure Monitor : traces, métriques et journaux. Pour plus d’informations sur ces types de télémétrie, consultez [la vue d’ensemble de la plateforme de données](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform). Utilisez les instructions suivantes pour envoyer ces types de télémétrie par le biais des trois exportateurs.

## <a name="telemetry-type-mappings"></a>Mappages des types de données de télémétrie

Voici les exportateurs fournis par OpenCensus, mappés aux types de données de télémétrie que vous verrez dans Azure Monitor.

![Capture d’écran du mappage des types de données de télémétrie d’OpenCensus à Azure Monitor](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>Trace

> [!NOTE]
> `Trace` dans OpenCensus fait référence au [traçage distribué](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing). `AzureExporter` envoie de la télémétrie pour `requests` et `dependency` à Azure Monitor.

1. Commençons par générer des données de trace en local. Dans IDLE pour Python ou l’éditeur de votre choix, entrez le code suivant.

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

2. Lors de l’exécution du code, vous êtes systématiquement invité à saisir une valeur. À chaque entrée, la valeur est imprimée dans l’interpréteur de commandes et le module OpenCensus Python générera un élément `SpanData` correspondant. Le projet OpenCensus définit une [trace comme une arborescence d’étendues](https://opencensus.io/core-concepts/tracing/).
    
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

3. Bien qu’entrer des valeurs soit utile à des fins de démonstration, nous souhaitons surtout émettre `SpanData` vers Azure Monitor. Transmettez votre chaîne de connexion directement à l’exportateur, ou spécifiez-la dans une variable d’environnement `APPLICATIONINSIGHTS_CONNECTION_STRING`. Modifiez le code de l’étape précédente sur la base de l’exemple de code suivant :

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
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

4. Maintenant, lorsque vous exécutez le script Python, vous êtes à nouveau invité à entrer une valeur, mais cette valeur est la seule à être imprimée dans l’interpréteur de commandes. Le `SpanData` créé sera envoyé à Azure Monitor. Vous pouvez trouver les données d’unités de travail émises sous `dependencies`.

5. Pour plus d’informations sur l’échantillonnage dans OpenCensus, consultez [Échantillonnage dans OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

6. Pour plus d’informations sur la corrélation de télémétrie dans vos données de trace, consultez la [corrélation de télémétrie](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python) OpenCensus.

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
2. Durant l’exécution du code, vous êtes invité à plusieurs reprises à appuyer sur Entrée. Une métrique est créée pour suivre le nombre de fois où vous avez appuyé sur Entrée. Avec chaque entrée, la valeur est incrémentée et les informations de la métrique s’affichent dans la console. Les informations incluent la valeur actuelle et l’horodatage en cours au moment de la mise à jour de la métrique.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Bien qu’entrer des valeurs soit utile à des fins de démonstration, nous souhaitons surtout émettre les données de métrique vers Azure Monitor. Transmettez votre chaîne de connexion directement à l’exportateur, ou spécifiez-la dans une variable d’environnement `APPLICATIONINSIGHTS_CONNECTION_STRING`. Modifiez le code de l’étape précédente sur la base de l’exemple de code suivant :

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

4. L’exportateur envoie les données de métrique vers Azure Monitor à un intervalle fixe. La valeur par défaut est toutes les 15 secondes. Nous effectuons le suivi d’une seule métrique, de sorte que ces données métriques, quelle que soit leur valeur et l’horodatage qu’elles contiennent, sont envoyés à chaque intervalle. Vous pouvez trouver les données sous `customMetrics`.

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

2.  Le code vous demandera continuellement d’entrer une valeur. Une entrée de journal est émise pour chaque valeur entrée.

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

3. Bien qu’entrer des valeurs soit utile à des fins de démonstration, nous voulons au final émettre les données du journal pour Azure Monitor. Transmettez votre chaîne de connexion directement à l’exportateur, ou spécifiez-la dans une variable d’environnement `APPLICATIONINSIGHTS_CONNECTION_STRING`. Modifiez le code de l’étape précédente sur la base de l’exemple de code suivant :

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

> [!NOTE]
> `traces` dans ce contexte n’est pas identique à `Tracing`. `traces` fait référence au type de données de télémétrie que vous verrez dans Azure Monitor lors de l’utilisation du `AzureLogHandler`. `Tracing` fait référence à un concept dans OpenCensus et est relatif au [traçage distribué](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing).

5. Pour mettre en forme vos messages de journalisation, vous pouvez utiliser des `formatters` dans l’[API de journalisation](https://docs.python.org/3/library/logging.html#formatter-objects) Python intégrée.

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    format_str = '%(asctime)s - %(levelname)-8s - %(message)s'
    date_format = '%Y-%m-%d %H:%M:%S'
    formatter = logging.Formatter(format_str, date_format)
    # TODO: replace the all-zero GUID with your instrumentation key.
    handler = AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    handler.setFormatter(formatter)
    logger.addHandler(handler)
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

6. Vous pouvez également ajouter des propriétés personnalisées à vos messages de journal dans l’argument de mot clé *supplémentaire* à l’aide du champ custom_dimensions. Celles-ci s’affichent sous forme de paires clé-valeur dans `customDimensions`, dans Azure Monitor.
> [!NOTE]
> Pour que cette fonctionnalité fonctionne, vous devez passer un dictionnaire au champ custom_dimensions. Si vous passez des arguments de tout autre type, l’enregistreur d’événements les ignorera.

    ```python
    import logging
    
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in logging statements
    logger.warning('action', extra=properties)

    # Use properties in exception logs
    try:
        result = 1 / 0  # generate a ZeroDivisionError
    except Exception:
    logger.exception('Captured an exception.', extra=properties)
    ```

7. Pour plus d’informations sur l’enrichissement de vos journaux avec les données de contexte de trace, consultez [Intégration des journaux](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation) OpenCensus Python.

## <a name="view-your-data-with-queries"></a>Voir vos données avec des requêtes

Vous pouvez voir les données de télémétrie envoyées à partir de votre application via l’onglet **Journaux (Analytics)** .

![Capture d’écran du volet de vue d’ensemble, où « Journaux (Analytics) » est sélectionné dans un encadré rouge](./media/opencensus-python/0010-logs-query.png)

Dans la liste sous **Activé** :

- Pour les données de télémétrie envoyées avec l’exportateur de traces Azure Monitor, les requêtes entrantes s’affichent sous `requests`. Les requêtes sortantes ou In-process s’affichent sous `dependencies`.
- Pour la télémétrie envoyée avec l’exportateur de métriques Azure Monitor, les métriques envoyées s’affichent sous `customMetrics`.
- Pour la télémétrie envoyée avec l’exportateur de journaux Azure Monitor, les journaux envoyés s’affichent sous `traces`. Les exceptions s’affichent sous `exceptions`.

Pour obtenir des informations plus détaillées sur l’utilisation des requêtes et des journaux, consultez [Journaux dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs).

## <a name="learn-more-about-opencensus-for-python"></a>En savoir plus sur OpenCensus pour Python

* [OpenCensus Python sur GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Personnalisation](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Intégration de Flask](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Intégration de Django](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [Intégration de MySQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>Étapes suivantes

* [Plan de l’application](./../../azure-monitor/app/app-map.md)
* [Analyse des performances de bout en bout](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Alertes

* [Tests de disponibilité](../../azure-monitor/app/monitor-web-app-availability.md) : créez des tests pour vous assurer que votre site est visible sur le web.
* [Diagnostics intelligents](../../azure-monitor/app/proactive-diagnostics.md) : ces tests s’exécutent automatiquement, sans que vous n’ayez rien à faire pour les configurer. Ils vous indiquent si votre application affiche un taux inhabituel de demandes ayant échoué.
* [Alertes de métriques](../../azure-monitor/app/alerts.md) : Définissez des alertes qui vous avertissent si un indicateur de performance dépasse un seuil. Vous pouvez définir des mesures personnalisées que vous codez dans votre application.
