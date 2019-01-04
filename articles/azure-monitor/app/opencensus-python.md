---
title: Suivi OpenCensus Python avec Azure Application Insights | Microsoft Docs
description: Fournit des instructions pour lier le suivi OpenCensus Python au redirecteur local et à Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/18/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: aa71d171bbb2125a61cc6fec6b07639a5c340796
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2019
ms.locfileid: "54004429"
---
# <a name="collect-distributed-traces-from-python-preview"></a>Collecter les traces distribuées à partir de Python (préversion)

Application Insights prend désormais en charge le suivi distribué des applications Python grâce à une intégration à [OpenCensus](https://opencensus.io) et à notre nouveau [redirecteur local](./../../azure-monitor/app/opencensus-local-forwarder.md). Cet article vous guide étape par étape dans le processus de configuration d’OpenCensus pour Python et d’obtention de vos données de trace dans Application Insights.

## <a name="prerequisites"></a>Prérequis

- Vous avez besoin d’un abonnement Azure.
- Python doit être installé. Cet article s’appuie sur [Python 3.7.0](https://www.python.org/downloads/), mais quelques ajustements mineurs permettent d’utiliser des versions antérieures.
- Suivez les instructions pour installer le [redirecteur local en tant que service Windows](./../../azure-monitor/app/opencensus-local-forwarder.md#windows-service).

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Créer une ressource Application Insights

Vous devez tout d’abord créer une ressource Application Insights qui génère une clé d’instrumentation (ikey). L’ikey est ensuite utilisée pour configurer le redirecteur local afin d’envoyer des traces distribuées de votre application instrumentée OpenCensus à Application Insights.   

1. Sélectionnez **Créer une ressource** > **Outils de développement** > **Application Insights**.

   ![Ajout d’une ressource Application Insights](./media/opencensus-python/0001-create-resource.png)

   Une boîte de configuration s’affiche. Utilisez le tableau suivant pour remplir les champs d’entrée.

    | Paramètres        | Valeur           | Description  |
   | ------------- |:-------------|:-----|
   | **Nom**      | Valeur globalement unique | Nom identifiant l’application que vous analysez |
   | **Type d’application** | Généralités | Type de l’application que vous analysez |
   | **Groupe de ressources**     | myResourceGroup      | Nom du nouveau groupe de ressources pour héberger les données Application Insights |
   | **Lieu** | USA Est | Choisissez un emplacement près de chez vous ou proche de l’endroit où votre application est hébergée |

2. Cliquez sur **Créer**.

## <a name="configure-local-forwarder"></a>Configurer le redirecteur local

1. Sélectionnez **Vue d’ensemble** > **Éléments principaux** > copiez la **clé d’instrumentation** de votre application.

   ![Capture d’écran de la clé d’instrumentation](./media/opencensus-python/0003-instrumentation-key.png)

2. Modifiez votre fichier `LocalForwarder.config` et ajoutez votre clé d’instrumentation. Si vous avez suivi les instructions fournies dans les [prérequis](./../../azure-monitor/app/opencensus-local-forwarder.md#windows-service), le fichier se trouve dans `C:\LF-WindowsServiceHost`

    ```xml
      <OpenCensusToApplicationInsights>
        <!--
          Instrumentation key to track telemetry to.
          -->
        <InstrumentationKey>{enter-instrumentation-key}</InstrumentationKey>
      </OpenCensusToApplicationInsights>
    
      <!-- Describes aspects of processing Application Insights telemetry-->
      <ApplicationInsights>
        <LiveMetricsStreamInstrumentationKey>{enter-instrumentation-key}</LiveMetricsStreamInstrumentationKey>
      </ApplicationInsights>
    </LocalForwarderConfiguration>
    ```

3. Redémarrez le service **Redirecteur local** de l’application.

## <a name="opencensus-python-package"></a>Package OpenCensus Python

1. Installez le package OpenCensus pour Python en saisissant pip ou pipenv dans la ligne de commande :

    ```python
    python -m pip install opencensus
    # pip env install opencensus
    ```

    > [!NOTE]
    > `python -m pip install opencensus` suppose qu’une variable d’environnement PATH est définie pour votre installation Python. Dans le cas contraire, vous devez fournir le chemin d’accès complet du répertoire contenant votre fichier exécutable Python, dans une commande comme celle-ci : `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus`.

2. Nous allons d’abord générer des données de trace en local. Dans IDLE de Python ou dans l’éditeur de votre choix, saisissez le code suivant :

    ```python
    from opencensus.trace.tracer import Tracer
    
    def main():
        while True:
            valuePrompt()
    
    def valuePrompt():
        tracer = Tracer()
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)
    
    if __name__ == "__main__":
        main()
    
    ```

3. Lors de l’exécution du code, vous êtes systématiquement invité à saisir une valeur. À chaque saisie, la valeur est imprimée dans l’interpréteur de commandes et un élément **SpanData** correspondant est généré par le module OpenCensus Python. Le projet OpenCensus définit une [_trace comme une arborescence d’étendues_](https://opencensus.io/core-concepts/tracing/).
    
    ```python
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=1f07f062ac394c50925f2ae61e635e14, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='5c17a4ad6ba14299', parent_span_id=None, attributes={}, start_time='2018-09-15T20:42:15.847292Z', end_time='2018-09-15T20:42:17.615664Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=c71b4e88a22a495da61df52ce3eee3e1, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='51547c0af5f046eb', parent_span_id=None, attributes={}, start_time='2018-09-15T20:42:17.615664Z', end_time='2018-09-15T20:48:11.160314Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=b4cdcc9e6df44a8fbb6e8ddeccc1351c, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f2caacf7892744d1', parent_span_id=None, attributes={}, start_time='2018-09-15T20:48:11.175931Z', end_time='2018-09-15T20:48:12.629178Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

4. Bien que cela soit utile à des fins de démonstration, nous voulons émettre SpanData de sorte qu’il puisse être collecté par notre **service redirecteur local** et envoyé sur Application Insights. Modifiez le code de l’étape précédente de la manière suivante :

    ```python
    from opencensus.trace.tracer import Tracer
    from opencensus.trace import config_integration
    from opencensus.trace.exporters.ocagent import trace_exporter
    from opencensus.trace import tracer as tracer_module
    
    import os
    
    def main():        
        while True:
            valuePrompt()
    
    def valuePrompt():
        export_LocalForwarder = trace_exporter.TraceExporter(
        service_name=os.getenv('SERVICE_NAME', 'python-service'),
        endpoint=os.getenv('OCAGENT_TRACE_EXPORTER_ENDPOINT'))
        
        tracer = Tracer(exporter=export_LocalForwarder)
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)
    
    if __name__ == "__main__":
        main()
    ```

5. Si vous enregistrez et essayez d’exécuter le module ci-dessus, vous recevrez peut-être une erreur `ModuleNotFoundError` pour `grpc`. Dans ce cas, exécutez la commande suivante pour installer le [package grpcio](https://pypi.org/project/grpcio/) avec :

    ```
    python -m pip install grpcio
    ```

6. Maintenant, lorsque vous exécutez le script Python ci-dessus, vous êtes à nouveau invité à saisir une valeur, mais cette valeur est la seule à être imprimée dans l’interpréteur de commandes.

7. Pour vérifier que le **redirecteur local** collecte les traces, consultez le fichier `LocalForwarder.config`. Si vous avez suivi les étapes décrites dans les [prérequis](https://docs.microsoft.com/azure/application-insights/local-forwarder#windows-service), il se trouve dans `C:\LF-WindowsServiceHost`.

    Dans l’image ci-dessous du fichier journal, vous voyez qu’avant d’exécuter le second script dans lequel nous avons ajouté un exportateur, la valeur de `OpenCensus input BatchesReceived` était 0. Une fois que nous avons commencé à exécuter le script mis à jour, la valeur de `BatchesReceived` a augmenté du nombre de valeurs saisies :
    
    ![Formulaire de nouvelle ressource Application Insights](./media/opencensus-python/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Démarrer l’analyse dans le portail Azure

1. Vous pouvez à présent rouvrir la page de **présentation** d’Application Insights dans le portail Azure afin d’afficher les détails sur votre application en cours d’exécution. Sélectionnez **Flux de métriques en temps réel**.

   ![Capture d’écran du volet Vue d’ensemble avec Flux de métriques en temps réel sélectionné dans l’encadré rouge](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. Si vous exécutez à nouveau le deuxième script Python et commencez à saisir des valeurs, vous voyez les données de trace s’afficher en temps réel à mesure qu’elles arrivent dans Application Insights à partir du service redirecteur local.

   ![Capture d’écran des flux de métriques en temps réel avec les données de performances affichées](./media/opencensus-python/0006-stream.png)

3. Revenez dans la page **Vue d’ensemble** et sélectionnez **Cartographie d’application** pour obtenir une présentation visuelle des relations de dépendance et de la durée des appels entre les composants de votre application.

    ![Capture d’écran d’une cartographie d’application de base](./media/opencensus-python/0007-application-map.png)

    Étant donné que nous n’avons suivi qu’un seul appel de méthode, notre cartographie d’application n’est pas aussi intéressante. Mais vous pouvez la mettre à l’échelle pour visualiser des applications beaucoup plus distribuées :

   ![Mise en correspondance d'applications](media/opencensus-python/application-map.png)

4. Sélectionnez **Examiner les performances** pour effectuer une analyse détaillée des performances et déterminer la cause racine du ralentissement des performances.

    ![Capture d’écran du volet des performances](./media/opencensus-python/0008-performance.png)

5. En sélectionnant **Exemples** et en cliquant sur l’un des exemples qui apparaît dans le volet de droite, vous lancez l’expérience Détails de la transaction de bout en bout. Notre exemple d’application ne montre qu’un seul événement, mais une application plus complexe vous permettrait d’explorer la transaction de bout en bout jusqu’au niveau de la pile des appels d’un événement individuel.

     ![Capture d’écran de l’interface de la transaction de bout en bout](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-python"></a>Arborescence des appels de procédure OpenCensus pour Python

Nous n’avons traité ici que les principes fondamentaux de la liaison d’OpenCensus pour Python avec le redirecteur local et Application Insights. Les conseils d’utilisation officielle traitent de sujets plus avancés comme :

* [Échantillonneurs](https://opencensus.io/api/python/trace/usage.html#samplers)
* [Intégration de Flask](https://opencensus.io/api/python/trace/usage.html#flask)
* [Intégration de Django](https://opencensus.io/api/python/trace/usage.html#django)
* [Intégration de MySQL](https://opencensus.io/api/python/trace/usage.html#service-integration)
* [PostgreSQL](https://opencensus.io/api/python/trace/usage.html#postgresql)
  
## <a name="next-steps"></a>Étapes suivantes

* [Guide d’utilisation Python et OpenCensus](https://opencensus.io/api/python/trace/usage.html)
* [Plan de l’application](./../../azure-monitor/app/app-map.md)
* [Analyse des performances de bout en bout](./../../application-insights/app-insights-tutorial-performance.md)
