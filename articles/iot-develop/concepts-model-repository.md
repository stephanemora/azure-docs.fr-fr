---
title: Comprendre les concepts du référentiel des modèles d’appareils | Microsoft Docs
description: En tant que développeur de solutions ou professionnel de l’informatique, découvrez les concepts de base du référentiel des modèles d’appareils.
author: rido-min
ms.author: rmpablos
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: 5a9a2126d8732a2923428efb7e58cc6ec45e9fa5
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2021
ms.locfileid: "114406351"
---
# <a name="device-models-repository"></a>Référentiel des modèles d’appareils

Le référentiel des modèles d’appareils (DMR) permet aux fabricants d’appareils de gérer et de partager les modèles d’appareil IoT Plug-and-Play. Les modèles d’appareil sont des documents LD JSON définis à l’aide du [langage DTDL (Digital Twins Modeling Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

Le système DMR définit un modèle pour stocker les interfaces DTDL dans une structure de dossiers en fonction de l’identificateur de modèle de jumeau d’appareil (DTMI). Vous pouvez localiser une interface dans le DMR en convertissant le DTMI en chemin d’accès relatif. Par exemple, le DTMI `dtmi:com:example:Thermostat;1` se traduit par `/dtmi/com/example/thermostat-1.json`.

## <a name="public-device-models-repository"></a>Référentiel des modèles d’appareils publics

Microsoft héberge un DMR public avec les caractéristiques suivantes :

- Modèles organisés. Microsoft révise et approuve toutes les interfaces disponibles à l’aide d’un workflow de validation des demandes de tirage (PR, pull request) GitHub.
- Immuabilité.  Une fois publiée, une interface ne peut pas être mise à jour.
- Hyperscale. Microsoft fournit l’infrastructure nécessaire pour créer un point de terminaison sécurisé et évolutif où vous pouvez publier et utiliser des modèles d’appareil.

## <a name="custom-device-models-repository"></a>Référentiel des modèles d’appareils personnalisés

Utilisez le même modèle DMR pour créer un DMR personnalisé dans n’importe quel support de stockage, comme un système de fichiers local ou des serveurs web HTTP personnalisés. Vous pouvez récupérer des modèles d’appareil à partir du DMR personnalisé de la même façon que vous le faites à partir du DRM public, en modifiant l’URL de base utilisée pour accéder au DMR.

> [!NOTE]
> Microsoft fournit des outils pour valider les modèles d’appareil dans le DMR public. Vous pouvez réutiliser ces outils dans des référentiels personnalisés.

## <a name="public-models"></a>Modèles publics

Les modèles d’appareils publics stockés dans le référentiel des modèles sont librement accessibles pour que tous les utilisent et les intègrent à leurs applications. Les modèles d’appareil publics permettent aux fabricants d’appareils et aux développeurs de solutions de partager et de réutiliser leurs modèles d’appareils IoT Plug-and-Play dans un écosystème ouvert.

Pour obtenir des instructions sur la publication d’un modèle dans le référentiel des modèles afin de le rendre public, reportez-vous à la section [Publier un modèle](#publish-a-model).

Les utilisateurs peuvent parcourir, rechercher et afficher des interfaces publiques à partir du [référentiel GitHub](https://github.com/Azure/iot-plugandplay-models) officiel.

Toutes les interfaces dans les dossiers `dtmi` sont également disponibles à partir du point de terminaison public [https://devicemodels.azure.com](https://devicemodels.azure.com)

### <a name="resolve-models"></a>Résoudre des modèles

Pour accéder à ces interfaces par programmation, vous pouvez utiliser le `ModelsRepositoryClient` disponible dans le package NuGet [Azure.IoT.ModelsRepository](https://www.nuget.org/packages/Azure.IoT.ModelsRepository). Ce client est configuré par défaut pour interroger le DMR public disponible sur [devicemodels.azure.com](https://devicemodels.azure.com/) et peut être configuré sur n’importe quel référentiel personnalisé.

Le client accepte un `DTMI` comme entrée et retourne un dictionnaire avec toutes les interfaces requises :

```cs
using Azure.IoT.ModelsRepository;

var client = new ModelsRepositoryClient();
IDictionary<string, string> models = client.GetModels("dtmi:com:example:TemperatureController;1");
models.Keys.ToList().ForEach(k => Console.WriteLine(k));
```

La sortie attendue doit afficher le `DTMI` des trois interfaces trouvées dans la chaîne de dépendance :

```txt
dtmi:com:example:TemperatureController;1
dtmi:com:example:Thermostat;1
dtmi:azure:DeviceManagement:DeviceInformation;1
```

Le `ModelsRepositoryClient` peut être configuré pour interroger un référentiel de modèles personnalisé, disponible via HTTP(S), et spécifier la résolution de dépendance à l’aide de l’un des `ModelDependencyResolution` disponibles :

- Désactivé. Retourne l’interface spécifiée uniquement, sans aucune dépendance.
- Activé. Retourne toutes les interfaces dans la chaîne de dépendance
- TryFromExpanded. Utilisez le fichier `.expanded.json` pour récupérer les dépendances précalculées 

> [!Tip] 
> Les référentiels personnalisés peuvent ne pas exposer le fichier `.expanded.json`. Si aucun n’est disponible, le client traitera chaque dépendance localement.

L’exemple de code suivant montre comment initialiser le `ModelsRepositoryClient` en utilisant une URL de base de référentiel personnalisé, en utilisant dans ce cas les URL `raw` de l’API GitHub sans utiliser le formulaire `expanded`, car il n’est pas disponible dans le point de terminaison `raw`. Le `AzureEventSourceListener` est initialisé pour inspecter la requête HTTP effectuée par le client :

```cs
using AzureEventSourceListener listener = AzureEventSourceListener.CreateConsoleLogger();

var client = new ModelsRepositoryClient(
    new Uri("https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main"),
    new ModelsRepositoryClientOptions(dependencyResolution: ModelDependencyResolution.Enabled));

IDictionary<string, string> models = client.GetModels("dtmi:com:example:TemperatureController;1");

models.Keys.ToList().ForEach(k => Console.WriteLine(k));
```

D’autres exemples sont disponibles dans le code source du référentiel GitHub du Kit de développement logiciel (SDK) Azure : [Azure.Iot.ModelsRepository/samples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/modelsrepository/Azure.IoT.ModelsRepository/samples)

## <a name="publish-a-model"></a>Publier un modèle

> [!Important]
> Vous devez disposer d’un compte GitHub pour pouvoir envoyer des modèles au DMR public.

1. Dupliquez le référentiel GitHub public : [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models).
1. Clonez le référentiel dupliqué. Facultativement, vous pouvez créer une branche pour que vos modifications restent isolées de la branche `main`.
1. Ajoutez les nouvelles interfaces au dossier `dtmi` à l’aide de la convention nom de dossier/nom de fichier. Pour plus d’informations, consultez [Importer un modèle dans le `dtmi/`dossier](#import-a-model-to-the-dtmi-folder).
1. Validez les modèles localement à l’aide de l’outil `dmr-client`. Pour plus d’informations, consultez [Valider les modèles](#validate-models).
1. Validez les modifications localement et envoyez vers votre duplication.
1. À partir de votre duplication (fork), créez une PR qui cible la branche `main`. Consultez les documents relatifs à la [création d’une requête de problème ou d’une demande de tirage (pull request)](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request).
1. Passez en revue les [conditions requises pour une demande de tirage](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md).

La PR déclenche un ensemble d’actions GitHub qui valident les interfaces envoyées et s’assurent que votre PR est conforme à toutes les exigences.

Microsoft répond à une PR avec toutes les vérifications dans les trois jours ouvrables.

### <a name="dmr-client-tools"></a>Outils `dmr-client`

Les outils utilisés pour valider les modèles lors des vérifications de PR permettent également d’ajouter et de valider les interfaces DTDL localement.

> [!NOTE]
> Cet outil requiert le [kit de développement logiciel (SDK) .NET](https://dotnet.microsoft.com/download) version 3.1 ou ultérieure.

### <a name="install-dmr-client"></a>Installer `dmr-client`

```bash
curl -L https://aka.ms/install-dmr-client-linux | bash
```

```powershell
iwr https://aka.ms/install-dmr-client-windows -UseBasicParsing | iex
```

### <a name="import-a-model-to-the-dtmi-folder"></a>Importer un modèle dans le dossier `dtmi/`

Si votre modèle est déjà stocké dans des fichiers JSON, vous pouvez utiliser la commande `dmr-client import` pour les ajouter au dossier `dtmi/` avec les noms de fichiers qui conviennent :

```bash
# from the local repo root folder
dmr-client import --model-file "MyThermostat.json"
```

> [!TIP]
> Vous pouvez utiliser l’argument `--local-repo` pour spécifier le dossier racine du référentiel local.

### <a name="validate-models"></a>Valider les modèles

Vous pouvez valider vos modèles à l’aide de la commande `dmr-client validate` :

```bash
dmr-client validate --model-file ./my/model/file.json
```

> [!NOTE]
> La validation utilise la dernière version de l’analyseur DTDL afin de garantir la compatibilité de toutes les interfaces avec la spécification du langage DTDL.

Pour valider les dépendances externes, celles-ci doivent être présentes dans le référentiel local. Pour valider les modèles, utilisez l’option `--repo` afin de spécifier un dossier `local` ou `remote` et résoudre les dépendances :

```bash
# from the repo root folder
dmr-client validate --model-file ./my/model/file.json --repo .
```

### <a name="strict-validation"></a>Validation stricte

Le DMR contient des [exigences](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md) supplémentaires. Utilisez l’indicateur `stict` pour valider votre modèle par rapport à celles-ci :

```bash
dmr-client validate --model-file ./my/model/file.json --repo . --strict true
```

Vérifiez la sortie de la console pour tout message d’erreur.

### <a name="export-models"></a>Exporter des modèles

Les modèles peuvent être exportés à partir d’un référentiel donné (local ou distant) vers un fichier unique à l’aide d’un tableau JSON :

```bash
dmr-client export --dtmi "dtmi:com:example:TemperatureController;1" -o TemperatureController.expanded.json
```

## <a name="next-steps"></a>Étapes suivantes

Nous vous recommandons maintenant de consulter [Architecture IoT Plug-and-Play](concepts-architecture.md).
