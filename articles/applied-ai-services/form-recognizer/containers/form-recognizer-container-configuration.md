---
title: Configurer des conteneurs Form Recognizer
titleSuffix: Azure Applied AI Services
description: Découvrez comment configurer le conteneur Form Recognizer pour analyser des données de formulaire et de table.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: lajanuar
ms.openlocfilehash: a7a42f9a2078a3384949704a6319c8acbedcb17d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122563899"
---
# <a name="configure-form-recognizer-containers"></a>Configurer des conteneurs Form Recognizer

> [!IMPORTANT]
>
> Les conteneurs Form Recognizer sont en préversion contrôlée. Pour les utiliser, vous devez envoyer une [demande en ligne](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu) et recevoir une approbation. Pour plus d’informations, consultez la section [**Demande d’approbation pour l’exécution d’un conteneur**](form-recognizer-container-install-run.md#request-approval-to-run-the-container), plus loin dans cet article.

Avec les conteneurs Azure Form Recognizer, vous pouvez créer une architecture d’application optimisée pour tirer parti des fonctionnalités robustes du cloud et de la localité en périphérie. Les conteneurs fournissent un environnement minimaliste, isolé virtuellement, qui peut être facilement déployé en local et dans le cloud. Dans cet article, vous allez apprendre à configurer l’environnement d’exécution du conteneur Form Recognizer à l’aide des arguments de la commande `docker compose`. Les fonctionnalités de Form Recognizer sont prises en charge par six conteneurs de fonctionnalités Form Recognizer : **Disposition**, **Carte de visite**, **Document d’ID**, **Reçu**, **Facture**, **Personnalisé**. Ces conteneurs ont plusieurs paramètres obligatoires et quelques paramètres facultatifs. Pour obtenir des exemples, consultez la section [Exemple de fichier docker-compose.yml](#example-docker-composeyml-file).

## <a name="configuration-settings"></a>Paramètres de configuration

Chaque conteneur a les paramètres de configuration suivants :

|Obligatoire|Paramètre|Objectif|
|--|--|--|
|Oui|[ApiKey](#apikey-and-billing-configuration-setting)|Assure le suivi des informations de facturation.|
|Oui|[Billing](#apikey-and-billing-configuration-setting)|Spécifie l’URI de point de terminaison de la ressource de service sur Azure. Pour plus d’informations sur l’obtention, _consultez_ [Billing]](form-recognizer-container-install-run.md#billing). Pour obtenir plus d’informations et une liste complète des points de terminaison régionaux, _consultez_ [Sous-domaines personnalisés pour Cognitive Services](../../../cognitive-services/cognitive-services-custom-subdomains.md).|
|Oui|[Eula](#eula-setting)| Indique que vous avez accepté la licence pour le conteneur.|
|Non|[ApplicationInsights](#applicationinsights-setting)|Permet d’ajouter la prise en charge de la télémétrie [Azure Application Insights](/azure/application-insights) à votre conteneur.|
|Non|[Fluentd](#fluentd-settings)|Écrit les données des journaux et, éventuellement, des métriques, sur un serveur Fluentd.|
|Non|Proxy HTTP|Configure un proxy HTTP pour effectuer des requêtes sortantes.|
|Non|[Logging](#logging-settings)|Fournit la prise en charge de la journalisation ASP.NET Core pour votre conteneur. |

> [!IMPORTANT]
> Les paramètres [`ApiKey`](#apikey-and-billing-configuration-setting), [`Billing`](#apikey-and-billing-configuration-setting) et [`Eula`](#eula-setting) sont utilisés ensemble. Vous devez fournir des valeurs valides pour les trois paramètres ; à défaut, vos conteneurs ne démarreront pas. Pour plus d’informations sur l’instanciation d’un conteneur à l’aide de ces paramètres de configuration, consultez [Facturation](form-recognizer-container-install-run.md#billing).

## <a name="apikey-and-billing-configuration-setting"></a>Paramètres de configuration ApiKey et Billing

Le paramètre `ApiKey` spécifie la clé de ressource Azure utilisée pour effectuer le suivi des informations de facturation pour le conteneur. La valeur pour ApiKey doit être une clé valide pour la ressource spécifiée pour `Billing` dans la section « Paramètre de configuration Billing ».

Le paramètre `Billing` spécifie l’URI de point de terminaison de la ressource sur Azure servant à mesurer les informations de facturation du conteneur. La valeur de ce paramètre de configuration doit être un URI de point de terminaison valide pour une ressource sur Azure. Le conteneur crée des rapports sur l’utilisation toutes les 10 à 15 minutes.

 Vous pouvez trouver ces paramètres dans le portail Azure dans la page **Clés et points de terminaison**.

   :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="Capture d’écran : Page des clés et des points de terminaison du portail Azure":::

## <a name="eula-setting"></a>Paramètre Eula

[!INCLUDE [Container shared configuration eula settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="applicationinsights-setting"></a>Paramètre ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="fluentd-settings"></a>Paramètres Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Paramètres des informations d’identification du proxy HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Paramètres de journalisation

[!INCLUDE [Container shared configuration logging settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="volume-settings"></a>Paramètres des volumes

Utilisez des [**volumes**](https://docs.docker.com/storage/volumes/) pour lire et écrire des données vers et à partir du conteneur. Les volumes sont ceux préférés pour conserver les données générées et utilisées par les conteneurs Docker. Vous pouvez spécifier un montage d’entrée ou un montage de sortie en incluant l’option `volumes` et en spécifiant `type` (liaison), `source` (chemin du dossier) et `target` (paramètre de chemin de fichier).

Le conteneur Form Recognizer nécessite un volume d’entrée et un volume de sortie. Le volume d’entrée peut être en lecture seule (`ro`) et il est indispensable pour accéder aux données qui sont utilisées pour l’entraînement et le scoring. Le volume de sortie doit être accessible en écriture. Il sert à stocker les modèles et les données temporaires.

La syntaxe exacte de l’emplacement du volume hôte varie en fonction du système d’exploitation hôte. De plus, l’emplacement du volume de l’[ordinateur hôte](form-recognizer-container-install-run.md#host-computer-requirements) peut ne pas être accessible en raison d’un conflit entre les autorisations de compte de service Docker et les autorisations d’emplacement du montage d’hôte.

## <a name="example-docker-composeyml-file"></a>Exemple de fichier docker-compose.yml

La méthode **dockr compose** comprend trois étapes :

 1. Créez un Dockerfile.
 1. Définissez les services dans un fichier **docker-compose.yml** de sorte qu’ils puissent être exécutés ensemble dans un environnement isolé.
 1. Exécutez `docker-compose up` pour démarrer et exécuter vos services.

### <a name="single-container-example"></a>Exemple avec un seul conteneur

Dans cet exemple, entrez les valeurs {FORM_RECOGNIZER_ENDPOINT_URI} et {FORM_RECOGNIZER_API_KEY} pour votre instance de conteneur de disposition.

#### <a name="layout-container"></a>**Conteneur de disposition**

```yml
version: "3.9"
services:
azure-cognitive-service-layout:
    container_name: azure-cognitive-service-layout
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}

    ports:
      - "5000"
    networks:
      - ocrvnet
networks:
  ocrvnet:
    driver: bridge
```

### <a name="multiple-containers-example"></a>Exemple avec plusieurs conteneurs

#### <a name="receipt-and-ocr-read-containers"></a>**Conteneurs de reçus et de lecture OCR**

Dans cet exemple, entrez les valeurs {FORM_RECOGNIZER_ENDPOINT_URI} et {FORM_RECOGNIZER_API_KEY} pour votre conteneur de reçus et les valeurs {COMPUTER_VISION_ENDPOINT_URI} et {COMPUTER_VISION_API_KEY} pour votre conteneur de lecture Vision par ordinateur.

```yml
version: "3"
services:
  azure-cognitive-service-receipt:
    container_name: azure-cognitive-service-receipt
    image: cognitiveservicespreview.azurecr.io/microsoft/cognitive-services-form-recognizer-receipt:2.1
    environment:
      - EULA=accept 
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceReadHost=http://azure-cognitive-service-read:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-read:
    container_name: azure-cognitive-service-read
    image: mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
    environment:
      - EULA=accept 
      - billing={COMPUTER_VISION_ENDPOINT_URI}
      - apikey={COMPUTER_VISION_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur l’exécution de plusieurs conteneurs et la commande docker compose](form-recognizer-container-install-run.md)
