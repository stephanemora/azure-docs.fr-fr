---
title: Suivi OpenCensus Go avec Azure Application Insights | Microsoft Docs
description: Fournit des instructions pour intégrer le suivi OpenCensus Go au redirecteur local et à Application Insights
ms.topic: conceptual
ms.date: 09/15/2018
ms.openlocfilehash: ddb8dfbf14f2c75e4d16693076e56a711d8861ad
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669979"
---
# <a name="collect-distributed-traces-from-go-preview"></a>Collecter les traces distribuées à partir de Go (préversion)

Application Insights prend désormais en charge le suivi distribué des applications Go grâce à une intégration à [OpenCensus](https://opencensus.io) et à notre nouveau [redirecteur local](./opencensus-local-forwarder.md). Cet article vous guide étape par étape dans le processus de configuration d’OpenCensus for Go et d’obtention de vos données de trace dans Application Insights.

## <a name="prerequisites"></a>Prérequis

- Vous avez besoin d’un abonnement Azure.
- Go doit être installé, cet article utilise la version 1.11, [Téléchargement de Go](https://golang.org/dl/).
- Suivez les instructions pour installer le [redirecteur local en tant que service Windows](./opencensus-local-forwarder.md).

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Créer une ressource Application Insights

Vous devez tout d’abord créer une ressource Application Insights qui génère une clé d’instrumentation (ikey). L’ikey est ensuite utilisée pour configurer le redirecteur local afin d’envoyer des traces distribuées de votre application instrumentée OpenCensus à Application Insights.   

1. Sélectionnez **Créer une ressource** > **Outils de développement** > **Application Insights**.

   ![Ajout d’une ressource Application Insights](./media/opencensus-Go/0001-create-resource.png)

 > [!NOTE]
   >Si c’est la première fois que vous créez une ressource Application Insights, vous pouvez en apprendre davantage en lisant l’article [Créer une ressource Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

   Une boîte de configuration s’affiche. Utilisez le tableau suivant pour remplir les champs d’entrée.

   | Paramètres        | Valeur           | Description  |
   | ------------- |:-------------|:-----|
   | **Nom**      | Valeur globalement unique | Nom identifiant l’application que vous analysez |
   | **Groupe de ressources**     | myResourceGroup      | Nom du nouveau groupe de ressources pour héberger les données Application Insights |
   | **Lieu** | USA Est | Choisissez un emplacement près de chez vous ou proche de l’endroit où votre application est hébergée |

2. Cliquez sur **Créer**.

## <a name="configure-local-forwarder"></a>Configurer le redirecteur local

1. Sélectionnez **Vue d’ensemble** > **Éléments principaux** > copiez la **clé d’instrumentation** de votre application.

   ![Capture d’écran de la clé d’instrumentation](./media/opencensus-Go/0003-instrumentation-key.png)

2. Modifiez votre fichier `LocalForwarder.config` et ajoutez votre clé d’instrumentation. Si vous avez suivi les instructions fournies dans les [prérequis](./opencensus-local-forwarder.md), le fichier se trouve dans `C:\LF-WindowsServiceHost`

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

## <a name="opencensus-go-packages"></a>Packages OpenCensus Go

1. Installez les packages OpenCensus pour Go depuis la ligne de commande :

    ```go
    go get -u go.opencensus.io
    go get -u contrib.go.opencensus.io/exporter/ocagent
    ```

2. Ajoutez le code suivant à un fichier .go, puis générez et exécutez. (Cet exemple provient de l’aide officielle d’OpenCensus avec le code ajouté qui simplifie l’intégration au redirecteur local.)

     ```go
        // Copyright 2018, OpenCensus Authors
        //
        // Licensed under the Apache License, Version 2.0 (the "License");
        // you may not use this file except in compliance with the License.
        // You may obtain a copy of the License at
        //
        //     https://www.apache.org/licenses/LICENSE-2.0
        //
        // Unless required by applicable law or agreed to in writing, software
        // distributed under the License is distributed on an "AS IS" BASIS,
        // WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
        // See the License for the specific language governing permissions and
        // limitations under the License.
        package main
        
        import (
        
            "bytes"
            "fmt"
            "log"
            "net/http"
            os "os"
            
            ocagent "contrib.go.opencensus.io/exporter/ocagent"
            "go.opencensus.io/plugin/ochttp"
            "go.opencensus.io/plugin/ochttp/propagation/tracecontext"
            "go.opencensus.io/trace"
        
        )
        
        func main() {
            // Register stats and trace exporters to export the collected data.
            serviceName := os.Getenv("SERVICE_NAME")
            if len(serviceName) == 0 {
                serviceName = "go-app"
            }
            fmt.Printf(serviceName)
            agentEndpoint := os.Getenv("OCAGENT_TRACE_EXPORTER_ENDPOINT")

            if len(agentEndpoint) == 0 {
                agentEndpoint = fmt.Sprintf("%s:%d", ocagent.DefaultAgentHost, ocagent.DefaultAgentPort)
            }
        
            fmt.Printf(agentEndpoint)
            exporter, err := ocagent.NewExporter(ocagent.WithInsecure(), ocagent.WithServiceName(serviceName), ocagent.WithAddress(agentEndpoint))
        
            if err != nil {
                log.Printf("Failed to create the agent exporter: %v", err)
            }
        
            trace.RegisterExporter(exporter)
        
            trace.ApplyConfig(trace.Config{DefaultSampler: trace.AlwaysSample()})
        
            client := &http.Client{Transport: &ochttp.Transport{Propagation: &tracecontext.HTTPFormat{}}}
        
            http.HandleFunc("/", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                var jsonStr = []byte(`[ { "url": "http://blank.org", "arguments": [] } ]`)
                r, _ := http.NewRequest("POST", "http://blank.org", bytes.NewBuffer(jsonStr))
                r.Header.Set("Content-Type", "application/json")
        
                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }
            })
        
            http.HandleFunc("/call_blank", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                r, _ := http.NewRequest("GET", "http://blank.org", nil)

                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
        
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }        
            })
        
            log.Fatal(http.ListenAndServe(":50030", &ochttp.Handler{Propagation: &tracecontext.HTTPFormat{}}))
        
        }
     ```

3. Une fois l’application Go simple en cours d’exécution, accédez à `http://localhost:50030`. Chaque actualisation du navigateur génère le texte « hello world » accompagné des données environnantes correspondantes récupérées par le redirecteur local.

4. Pour vérifier que le **redirecteur local** collecte les traces, consultez le fichier `LocalForwarder.config`. Si vous avez suivi les étapes décrites dans les [prérequis](https://docs.microsoft.com/azure/application-insights/local-forwarder), il se trouve dans `C:\LF-WindowsServiceHost`.

    Dans l’image ci-dessous du fichier journal, vous voyez qu’avant d’exécuter le second script dans lequel nous avons ajouté un exportateur, la valeur de `OpenCensus input BatchesReceived` était 0. Une fois que nous avons commencé à exécuter le script mis à jour, la valeur de `BatchesReceived` a augmenté du nombre de valeurs saisies :
    
    ![Formulaire de nouvelle ressource Application Insights](./media/opencensus-go/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Démarrer l’analyse dans le portail Azure

1. Vous pouvez à présent rouvrir la page de **présentation** d’Application Insights dans le portail Azure afin d’afficher les détails sur votre application en cours d’exécution. Sélectionnez **Flux de métriques en temps réel**.

   ![Capture d’écran du volet Vue d’ensemble avec Flux de métriques en temps réel sélectionné dans l’encadré rouge](./media/opencensus-go/0005-overview-live-metrics-stream.png)

2. Si vous réexécutez la deuxième application Go et commencez à actualiser le navigateur pour `http://localhost:50030`, vous voyez les données de trace s’afficher en temps réel à mesure qu’elles arrivent dans Application Insights à partir du service redirecteur local.

   ![Capture d’écran des flux de métriques en temps réel avec les données de performances affichées](./media/opencensus-go/0006-stream.png)

3. Revenez dans la page **Vue d’ensemble** et sélectionnez **Cartographie d’application** pour obtenir une présentation visuelle des relations de dépendance et de la durée des appels entre les composants de votre application.

    ![Capture d’écran d’une cartographie d’application de base](./media/opencensus-go/0007-application-map.png)

    Étant donné que nous n’avons suivi qu’un seul appel de méthode, notre cartographie d’application n’est pas aussi intéressante. Mais vous pouvez la mettre à l’échelle pour visualiser des applications beaucoup plus distribuées :

   ![Mise en correspondance d'applications](media/opencensus-go/application-map.png)

4. Sélectionnez **Examiner les performances** pour effectuer une analyse détaillée des performances et déterminer la cause racine du ralentissement des performances.

    ![Capture d’écran du volet des performances](./media/opencensus-go/0008-performance.png)

5. En sélectionnant **Exemples** et en cliquant sur l’un des exemples qui apparaît dans le volet de droite, vous lancez l’expérience Détails de la transaction de bout en bout. Notre exemple d’application ne montre qu’un seul événement, mais une application plus complexe vous permettrait d’explorer la transaction de bout en bout jusqu’au niveau de la pile des appels d’un événement individuel.

     ![Capture d’écran de l’interface de la transaction de bout en bout](./media/opencensus-go/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-go"></a>Arborescence des appels de procédure OpenCensus pour Go

Nous n’avons traité ici que les principes fondamentaux de l’intégration d’OpenCensus pour Go au redirecteur local et à Application Insights. Les [conseils d’utilisation officiels de Go pour OpenCensus](https://godoc.org/go.opencensus.io) traitent de sujets plus avancés.

## <a name="next-steps"></a>Étapes suivantes

* [Plan de l’application](./../../azure-monitor/app/app-map.md)
* [Analyse des performances de bout en bout](./../../azure-monitor/learn/tutorial-performance.md)
