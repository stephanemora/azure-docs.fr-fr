---
title: Surveillance des applications Python avec Azure Application Insights | Microsoft Docs
description: Fournit des instructions permettant de lier OpenCensus Python à Application Insights
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 07/02/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: 2c043ad793dcf5e59eaf460d1ec4aa7a3b48810d
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541432"
---
# <a name="collect-distributed-traces-from-python-preview"></a>Collecter les traces distribuées à partir de Python (préversion)

Application Insights prend désormais en charge le suivi distribué des applications Python grâce à une intégration dans [OpenCensus](https://opencensus.io). Cet article vous guide étape par étape dans le processus de configuration d’OpenCensus pour Python et dans la procédure d’obtention de vos données de surveillance dans Application Insights.

## <a name="prerequisites"></a>Prérequis

- Vous avez besoin d’un abonnement Azure.
- Python doit être installé. Cet article s’appuie sur [Python 3.7.0](https://www.python.org/downloads/), mais quelques ajustements mineurs permettent d’utiliser des versions antérieures.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Créer une ressource Application Insights

Vous devez tout d’abord créer une ressource Application Insights, qui génère une clé d’instrumentation (ikey). Cette clé est ensuite utilisée pour configurer le Kit de développement logiciel (SDK) OpenCensus de manière à envoyer des données de télémétrie à Application Insights.

1. Sélectionnez **Créer une ressource** > **Outils de développement** > **Application Insights**.

   ![Ajout d’une ressource Application Insights](./media/opencensus-python/0001-create-resource.png)

   Une boîte de configuration s’affiche. Utilisez le tableau suivant pour remplir les champs d’entrée.

    | Paramètres        | Valeur           | Description  |
   | ------------- |:-------------|:-----|
   | **Nom**      | Valeur globalement unique | Nom identifiant l’application que vous analysez |
   | **Groupe de ressources**     | myResourceGroup      | Nom du nouveau groupe de ressources pour héberger les données Application Insights |
   | **Lieu** | USA Est | Choisissez un emplacement près de chez vous ou proche de l’endroit où votre application est hébergée |

2. Cliquez sur **Créer**.

## <a name="install-opencensus-azure-monitor-exporters"></a>Installer les exportateurs OpenCensus Azure Monitor

1. Installez les exportateurs OpenCensus Azure Monitor en procédant comme suit :

    ```console
    python -m pip install opencensus-ext-azure
    ```

    > [!NOTE]
    > `python -m pip install opencensus-ext-azure` suppose qu’une variable d’environnement PATH est définie pour votre installation Python. Dans le cas contraire, vous devez fournir le chemin d’accès complet du répertoire contenant votre fichier exécutable Python, dans une commande comme celle-ci : `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

2. Nous allons d’abord générer des données de trace en local. Dans IDLE de Python ou dans l’éditeur de votre choix, saisissez le code suivant :

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

3. Lors de l’exécution du code, vous êtes systématiquement invité à saisir une valeur. À chaque saisie, la valeur est imprimée dans l’interpréteur de commandes et un élément **SpanData** correspondant est généré par le module OpenCensus Python. Le projet OpenCensus définit une [_trace comme une arborescence d’étendues_](https://opencensus.io/core-concepts/tracing/).
    
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

4. Cela s’avère utile à des fins de démonstration, mais nous voulons surtout émettre les données SpanData vers Application Insights. Modifiez le code de l’étape précédente sur la base de l’exemple de code suivant :

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            instrumentation_key='00000000-0000-0000-0000-000000000000',
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
5. Maintenant, lorsque vous exécutez le script Python, vous êtes à nouveau invité à saisir une valeur, mais cette valeur est la seule à être imprimée dans l’interpréteur de commandes.

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

## <a name="opencensus-for-python"></a>OpenCensus pour Python

* [Personnalisation](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Intégration de Flask](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Intégration de Django](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [Intégration de MySQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>Étapes suivantes

* [OpenCensus Python sur GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Plan de l’application](./../../azure-monitor/app/app-map.md)
* [Analyse des performances de bout en bout](./../../azure-monitor/learn/tutorial-performance.md)