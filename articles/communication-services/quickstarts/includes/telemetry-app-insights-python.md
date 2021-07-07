---
title: Fichier include
description: Fichier include
services: azure-communication-services
author: jbeauregardb
manager: vravikumar
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/01/2021
ms.topic: include
ms.custom: include file
ms.author: jbeauregardb
ms.openlocfilehash: f473b3dbc898a19ad9ef3118f86c7e0ab0e23229
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2021
ms.locfileid: "111593089"
---
## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2.7 ou 3.6+.
- Une ressource Communication Services active et la chaîne de connexion. [Créez une ressource Communication Services](../create-communication-resource.md).
- Créez une [ressource Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) dans le portail Azure.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-python-application"></a>Créer une application Python

1. Ouvrez votre fenêtre de terminal ou de commande, créez un répertoire pour votre application, puis accédez-y.

   ```console
   mkdir application-insights-quickstart && cd application-insights-quickstart
   ```

1. Utilisez un éditeur de texte pour créer un fichier appelé **application-insights-quickstart.py** dans le répertoire racine du projet, puis ajoutez la structure du programme, notamment la gestion des exceptions de base. Dans les sections suivantes, vous ajouterez l’ensemble du code source de ce guide de démarrage rapide dans ce fichier.

   ```python
    import os
    from opentelemetry import trace
    from opentelemetry.sdk.trace import TracerProvider
    from opentelemetry.sdk.trace.export import BatchSpanProcessor
    from azure.communication.identity import CommunicationIdentityClient, CommunicationUserIdentifier

    from azure.monitor.opentelemetry.exporter import AzureMonitorTraceExporter

   try:
      print("Azure Communication Services - Access Tokens Quickstart")
      # Quickstart code goes here
   except Exception as ex:
      print("Exception:")
      print(ex)
   ```

### <a name="install-the-package"></a>Installer le package

Dans le répertoire de l’application, installez le Kit de développement logiciel (SDK) Azure Communication Services Identity pour le package Python et l’exportateur Microsoft Opentelemetry pour Azure Monitor.

```console
pip install azure-communication-identity
pip install azure-monitor-opentelemetry-exporter --pre
```

## <a name="setting-up-the-telemetry-tracer-with-communication-identity-sdk-calls"></a>Configuration du traceur de télémétrie avec des appels au Kit de développement logiciel (SDK) d’identité de communication

Instanciez un `CommunicationIdentityClient` avec votre chaîne de connexion. Le code ci-dessous récupère la chaîne de connexion de la ressource à partir d’une variable d’environnement nommée `COMMUNICATION_SERVICES_CONNECTION_STRING`. Découvrez comment [gérer la chaîne de connexion de la ressource](../create-communication-resource.md#store-your-connection-string).

Ajoutez ce code dans le bloc `try` :

```python
connection_string = os.environ["COMMUNICATION_SERVICES_CONNECTION_STRING"]
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

Tout d’abord, pour créer l’étendue permettant de suivre les demandes sur la plateforme Azure Monitor, vous devez créer une instance d’un objet `AzureMonitorTraceExporter`. Vous devrez fournir la chaîne de connexion à partir de votre ressource Application Insights.

```python
exporter = AzureMonitorTraceExporter.from_connection_string(
    "<APPLICATION-INSIGHTS-RESOURCE-CONNECTION-STRING>"
)
```

Cet exportateur vous permettra ensuite de créer les instances suivantes pour pouvoir effectuer le suivi des demandes. Une fois l’`AzureMonitorTraceExporter` créé, ajoutez le code suivant :

```python
    trace.set_tracer_provider(TracerProvider())
    tracer = trace.get_tracer(__name__)
    span_processor = BatchSpanProcessor(exporter)
    trace.get_tracer_provider().add_span_processor(span_processor)
```
Une fois le traceur initialisé, vous pouvez créer l’étendue chargée du suivi de vos demandes.

```python
with tracer.start_as_current_span(name="MyIdentityApplication"):
    user = identity_client.create_user()
```

## <a name="run-the-code"></a>Exécuter le code

À partir de l’invite de console, accédez au répertoire contenant le fichier **application-insights-quickstart.py**, puis exécutez la commande `python` suivante pour exécuter l’application.

```console
python application-insights-quickstart.py
```
