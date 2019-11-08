---
title: Installer et exécuter des conteneurs – Traduction de texte Translator Text
titleSuffix: Azure Cognitive Services
description: Ce tutoriel pas à pas décrit comment télécharger, installer et exécuter des conteneurs pour Analyse de Traduction de texte Translator Text.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 7b2b07f71d00e6da66062d1826f8c5c88bb6be7a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506820"
---
# <a name="install-and-run-translator-text-containers"></a>Installer et exécuter des conteneurs Traduction de texte Translator Text

<!--
    ACOM forward link:
    https://docs.microsoft.com/azure/cognitive-services/translator/howto-install-containers
-->

Les conteneurs vous permettent d’exécuter les API de Traduction de texte Translator Text dans votre propre environnement et sont très utiles pour répondre aux besoins spécifiques en matière de sécurité et de gouvernance des données.

## <a name="prerequisites"></a>Prérequis

L’utilisation des conteneurs Traduction de texte Translator Text est soumise aux prérequis suivants :

|Obligatoire|Objectif|
|--|--|
|Moteur Docker| Vous avez besoin d’un moteur Docker installé sur un [ordinateur hôte](#the-host-computer). Docker fournit des packages qui configurent l’environnement Docker sur [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) et [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Pour apprendre les principes de base de Docker et des conteneurs, consultez la [vue d’ensemble de Docker](https://docs.docker.com/engine/docker-overview/).<br><br> **Sur Windows**, vous devez également configurer Docker pour prendre en charge les conteneurs Linux.<br><br>|
|Bonne connaissance de Docker | Vous devez avoir une compréhension élémentaire des concepts Docker, notamment les registres, référentiels, conteneurs et images conteneurs, ainsi qu’une maîtrise des commandes `docker` de base.|

## <a name="request-access-to-the-container-registry"></a>Demander l’accès au registre de conteneurs

Vous devez d’abord compléter et envoyer le [formulaire de demande de conteneurs Traduction de texte Translator Text](https://aka.ms/translatorcontainerform) pour demander l’accès au conteneur.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

### <a name="the-host-computer"></a>L’ordinateur hôte

L’hôte est un ordinateur x64 avec un SE Linux qui exécute le conteneur Docker. Il peut s’agir d’un ordinateur local ou d’un service d’hébergement Docker dans Azure, comme :

* Azure Kubernetes Service.
* Azure Container Instances.
* Un cluster [Kubernetes](https://kubernetes.io/) déployé sur Azure Stack.

### <a name="container-requirements-and-recommendations"></a>Exigences et suggestions relatives au conteneur

Le tableau suivant décrit les cœurs de processeur minimum et recommandés, d’au moins 2,6 gigahertz (GHz) ou plus rapides, et la mémoire, en gigaoctets (Go), à allouer pour chaque conteneur Traduction de texte Translator Text.

| Conteneur | Minimale | Paire de langues |
|-----------|---------|---------------|
| Translator Text | 4 cœurs, 4 Go de mémoire | 4 |

Pour chaque paire de langues, il est recommandé d’avoir 1 Go de mémoire. Par défaut, le conteneur Traduction de texte Translator Text possède 3 ou 4 paires de langues, en fonction de `<image-tag>` que vous exécutez. Pour plus d’informations, consultez [les langues et la traduction prises en charge](#supported-languages-and-translation). Le nombre de cœurs et la quantité de mémoire correspondent aux paramètres `--cpus` et `--memory` qui sont utilisés dans le cadre de la commande `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenir l’image conteneur avec `docker pull`

Les images conteneur pour Traduction de texte Translator Text sont disponibles dans le référentiel de conteneur suivant. Le tableau mappe également les balises d’image de conteneur et les langues prises en charge correspondantes.

| Conteneur | Balise d'image | Langues prises en charge |
|-----------|-----------|---------------------|
| `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` | `ar_de_en_ru_zh_1.0.0` | `ar-SA`, `zh-CN`, `de-DE` et `ru-RU` |
| `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` | `de_en_es_fr_1.0.0` | `de-DE`, `fr-FR` et `es-ES` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-translator-text-container"></a>Commande docker pull du conteneur Traduction de texte Translator Text

Pour exécuter la commande [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/), vous devez d’abord accéder au registre de conteneurs. Depuis l’interface de ligne de commande Azure vous pouvez vous connecter à Azure Container Registry à l’aide de la commande [`az acr login`](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-login).

```azureinteractive
az acr login --name containerpreview.azurecr.io
```

Cette commande authentifiera l’utilisateur actuel avec l’Azure Container Registry correspondant. Maintenant, vous êtes libre d’exécuter la commande `docker pull`.

> [!NOTE]
> Selon la prise en charge de langue dont vous avez besoin, fournissez le `<image-tag>`correspondant.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-translator-text:<image-tag>
```

## <a name="how-to-use-the-container"></a>Comment utiliser le conteneur

Une fois que le conteneur est sur l’[ordinateur hôte](#the-host-computer), appliquez la procédure suivante pour travailler avec le conteneur.

1. [Exécuter le conteneur](#run-the-container-with-docker-run). D’autres [exemples](translator-text-container-config.md#example-docker-run-commands) de commande `docker run` sont disponibles.
1. [Interrogez le point de terminaison de traduction du conteneur](#query-the-containers-translate-endpoint).

## <a name="run-the-container-with-docker-run"></a>Exécuter le conteneur avec `docker run`

Utilisez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) pour exécuter l’un des trois conteneurs. [Exemples ](translator-text-container-config.md#example-docker-run-commands) de la commande `docker run` sont disponibles.

### <a name="run-container-example-of-docker-run-command"></a>Exemple de conteneur d’exécution de la commande d’exécution du docker

```Docker
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text:ar_de_en_ru_zh_1.0.0 \
Eula=accept
```

Cette commande :

* Exécute un conteneur Traduction de texte Translator Text à partir de l’image conteneur
* Alloue 4 cœurs de processeur et 4 gigaoctets (Go) de mémoire
* Expose le port TCP 5000 et alloue un pseudo-TTY pour le conteneur
* Accepte le contrat de licence utilisateur final (CLUF)
* Supprime automatiquement le conteneur après sa fermeture. L’image conteneur est toujours disponible sur l’ordinateur hôte

### <a name="how-to-collect-docker-logs"></a>Comment collecter les journaux du docker

À des fins de résolution des problèmes, il peut être utile d’afficher les journaux du docker à partir de l’exécution du conteneur. Tout d’abord, exécutez la commande [docker ps](https://docs.docker.com/engine/reference/commandline/ps/) avec l’indicateur de mise en forme pour limiter les détails affichés pour tous les conteneurs.

```Docker
docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.Status}}"

CONTAINER ID    IMAGE                                                                       STATUS
ed6f115697f3    containerpreview.azurecr.io/microsoft/cognitive-services-translator-text    Up 4 minutes
```

Ensuite, exécutez la commande [docker logs](https://docs.docker.com/engine/reference/commandline/logs/) avec le `<Container ID>` pour le conteneur correspondant en question afin d’afficher ses journaux.

```Docker
docker logs <Container ID> --timestamps --since=4h | grep Error
```

La commande docker logs ci-dessus collecte les journaux d’erreur pour les quatre dernières heures.

## <a name="supported-languages-and-translation"></a>Langues et traduction prises en charge

La méthode `POST /translate` prend en charge les conversions de langues suivantes, en passant de l’*anglais* à une langue cible et vice versa. Notez que bien que vous puissiez aller de et vers l’anglais avec l’une des langues listées, vous *ne pouvez pas* passer d’une *langue non anglaise* à une autre *langue non anglaise*.

> [!NOTE]
> Pour une qualité optimale, les consommateurs doivent envoyer une seule phrase par demande.

| Conversion de langue | Conversion ISO de langue | Balises d’image |
|--|--|:--|
| Anglais : left_right_arrow : Chinois | `en-US` :left_right_arrow : `zh-CN` | `ar_de_en_ru_zh_1.0.0` |
| Anglais : left_right_arrow : Russe | `en-US` :left_right_arrow : `ru-RU` | `ar_de_en_ru_zh_1.0.0` |
| Anglais : left_right_arrow : Arabe | `en-US` :left_right_arrow : `ar-SA` | `ar_de_en_ru_zh_1.0.0` |
| Anglais : left_right_arrow : Allemand | `en-US` :left_right_arrow : `de-DE` | `ar_de_en_ru_zh_1.0.0` et `de_en_es_fr_1.0.0` |
| Anglais : left_right_arrow : Espagnol | `en-US` :left_right_arrow : `es-ES` | `de_en_es_fr_1.0.0` |
| Anglais : left_right_arrow : Français | `en-US` :left_right_arrow : `fr-FR` | `de_en_es_fr_1.0.0` |

> [!IMPORTANT]
> Vous devez spécifier `Eula` pour exécuter le conteneur, sinon il ne démarrera pas.

## <a name="query-the-containers-translate-endpoint"></a>Interroger le point de terminaison de traduction du conteneur

Le conteneur fournit des API de point de terminaison basées sur REST. Voici plusieurs exemples d’utilisation de ce point de terminaison :

# <a name="curltabcurl"></a>[cURL](#tab/curl)


Exécutez la commande cURL suivante à partir de l’interface CLI de votre choix.

```curl
curl -X POST "http://localhost:5000/translate?api-version=3.0&from=en-US&to=de-DE"
    -H "Content-Type: application/json" -d "[{'Text':'hello, how are you'}]"
```

> [!TIP]
> Si vous tentez cette opération avant que le conteneur ne soit prêt, vous obtenez un message « le service est temporairement indisponible ». Attendez simplement que le conteneur soit prêt, puis réessayez.

La sortie suivante sera imprimée sur la console.

```console
"translations": [
    {
        "text": "hallo, wie geht es dir",
        "to": "de-DE"
    }
]
```

# <a name="swaggertabswagger"></a>[Swagger](#tab/Swagger)

Accédez à la page de swagger, http://localhost:5000/swagger/index.html

1. Sélectionnez **POST /translate**
1. Sélectionnez **Faites un essai**.
1. Entrez le paramètre **De** comme `en-US`
1. Entrez le paramètre **À** comme `de-DE`
1. Entrez le paramètre **api-version** comme `3.0`
1. Sous **texts**, remplacez `string` par le code JSON suivant
    ```json
    [
        {
            "text": "hello, how are you"
        }
    ]
    ```
1. Sélectionnez **Exécuter**, les traductions obtenues sont générées dans le **corps de la réponse**. Vous devez voir un texte similaire à ce qui suit :
    ```json
    "translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de-DE"
      }
    ]
    ```

# <a name="net-coretabnetcore"></a>[.NET Core](#tab/netcore)

1. Ouvrez Visual Studio et créez une nouvelle application de console.
1. Modifiez le fichier `*.csproj` pour ajouter le nœud `<LangVersion>7.1</LangVersion>` : spécifie C# 7.1.
1. Ajoutez le package NuGet [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/), version 11.0.2.
1. Dans `Program.cs`, remplacez tout le code existant par ce qui suit :
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;

    namespace TranslateContainer
    {
        class Program
        {
            const string ApiHostEndpoint = "http://localhost:5000";
            const string TranslateApi = "/translate?api-version=3.0&from=en-US&to=de-DE";

            static async Task Main(string[] args)
            {
                var textToTranslate = "hello, how are you";
                var result = await TranslateTextAsync(textToTranslate);

                Console.WriteLine(result);
                Console.ReadLine();
            }

            static async Task<string> TranslateTextAsync(string textToTranslate)
            {
                var body = new object[] { new { Text = textToTranslate } };
                var requestBody = JsonConvert.SerializeObject(body);

                var client = new HttpClient();
                using (var request =
                    new HttpRequestMessage
                    {
                        Method = HttpMethod.Post,
                        RequestUri = new Uri($"{ApiHostEndpoint}{TranslateApi}"),
                        Content = new StringContent(requestBody, Encoding.UTF8, "application/json")
                    })
                {
                    // Send the request and await a response.
                    var response = await client.SendAsync(request);

                    return await response.Content.ReadAsStringAsync();
                }
            }
        }
    }
    ```
1. Appuyez sur **F5** pour exécuter le programme.
1. La sortie suivante sera imprimée sur la console.
    ```console
    "translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de-DE"
      }
    ]
    ```

***

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Arrêter le conteneur

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Résolution de problèmes

Si vous exécutez le conteneur avec un [montage](translator-text-container-config.md#mount-settings) de sortie et la journalisation activée, il génère des fichiers journaux qui sont utiles pour résoudre les problèmes qui se produisent lors du démarrage ou de l’exécution du conteneur.

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Résumé

Dans cet article, vous avez découvert des concepts et le flux de travail pour le téléchargement, l’installation et l’exécution des conteneurs Traduction de texte Translator Text. En résumé :

* Traduction de texte Translator Text fournit plusieurs conteneurs Linux pour Docker, en encapsulant différentes paires de langues.
* Les images conteneur sont téléchargées à partir du registre de conteneurs « conteneur (préversion) ».
* Les images conteneurs s’exécutent dans Docker.
* Vous pouvez utiliser l’API REST ou le Kit de développement logiciel (SDK) pour appeler des opérations dans des conteneurs Traduction de texte Translator Text en spécifiant l’URI hôte du conteneur.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir les paramètres de configuration, passez en revue [Configurer des conteneurs](translator-text-container-config.md).
* Reportez-vous à [Forum aux questions (FAQ) de conteneur](../containers/container-faq.md) pour résoudre les problèmes liés à la fonctionnalité.
