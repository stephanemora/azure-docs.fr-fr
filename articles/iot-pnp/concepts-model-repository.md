---
title: Comprendre les concepts du référentiel de modèles d’appareil | Microsoft Docs
description: En tant que développeur de solutions ou professionnel de l’informatique, découvrez les concepts de base du référentiel de modèles d’appareil.
author: rido-min
ms.author: rmpablos
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4e15ef5256c1552fc8ab7fb9bd84f15bb3433834
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131358"
---
# <a name="device-model-repository"></a>Référentiel de modèles d’appareil

Le référentiel de modèles d’appareil (DMR) permet aux fabricants d’appareils de gérer et de partager les modèles d’appareil IoT Plug-and-Play. Les modèles d’appareil sont des documents LD JSON définis à l’aide du [langage DTDL (Digital Twins Modeling Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

Le système DMR définit un modèle pour stocker les interfaces DTDL dans une structure de dossiers en fonction de l’identificateur de modèle de jumeau d’appareil (DTMI). Vous pouvez localiser une interface dans le DMR en convertissant le DTMI en chemin d’accès relatif. Par exemple, le DTMI `dtmi:com:example:Thermostat;1` se traduit par `/dtmi/com/example/thermostat-1.json`.

## <a name="public-device-model-repository"></a>Référentiel de modèles d’appareil public

Microsoft héberge un DMR public avec les caractéristiques suivantes :

- Modèles organisés. Microsoft révise et approuve toutes les interfaces disponibles à l’aide d’un workflow de validation des demandes de tirage (PR, pull request) GitHub.
- Immuabilité.  Une fois publiée, une interface ne peut pas être mise à jour.
- Hyperscale. Microsoft fournit l’infrastructure nécessaire pour créer un point de terminaison sécurisé et évolutif où vous pouvez publier et utiliser des modèles d’appareil.

## <a name="custom-device-model-repository"></a>Référentiel de modèles d’appareil personnalisé

Vous pouvez utiliser le même modèle DMR dans n’importe quel support de stockage, comme un système de fichiers local ou des serveurs web HTTP personnalisés, pour créer un DMR personnalisé. Vous pouvez récupérer des modèles d’appareil à partir du DMR personnalisé de la même façon que vous le faites à partir du DRM public, en modifiant simplement l’URL de base utilisée pour accéder au DMR.

> [!NOTE]
> Microsoft fournit des outils pour valider les modèles d’appareil dans le DMR public. Vous pouvez réutiliser ces outils dans des référentiels personnalisés.

## <a name="public-models"></a>Modèles publics

Les modèles d’appareil publics stockés dans le référentiel de modèles sont librement accessibles pour que tous les utilisent et les intègrent à leurs applications. Les modèles d’appareil publics permettent aux fabricants d’appareils et aux développeurs de solutions de partager et de réutiliser leurs modèles d’appareils IoT Plug-and-Play dans un écosystème ouvert.

Pour obtenir des instructions sur la publication d’un modèle dans le référentiel de modèles afin de le rendre public, reportez-vous à la section [Publier un modèle](#publish-a-model).

Les utilisateurs peuvent parcourir, rechercher et afficher des interfaces publiques à partir du [référentiel GitHub](https://github.com/Azure/iot-plugandplay-models) officiel.

Toutes les interfaces dans les dossiers `dtmi` sont également disponibles à partir du point de terminaison public [https://devicemodels.azure.com](https://devicemodels.azure.com)

### <a name="resolve-models"></a>Résoudre des modèles

Pour accéder à ces interfaces par programmation, vous devez convertir un DTMI en un chemin d’accès relatif que vous pouvez utiliser pour interroger le point de terminaison public. L’exemple de code suivant montre comment procéder :

Pour convertir un DTMI en chemin d’accès absolu, nous utilisons la fonction `DtmiToPath`, avec `IsValidDtmi` :

```cs
static string DtmiToPath(string dtmi)
{
    if (!IsValidDtmi(dtmi))
    {
        return null;
    }
    // dtmi:com:example:Thermostat;1 -> dtmi/com/example/thermostat-1.json
    return $"/{dtmi.ToLowerInvariant().Replace(":", "/").Replace(";", "-")}.json";
}

static bool IsValidDtmi(string dtmi)
{
    // Regex defined at https://github.com/Azure/digital-twin-model-identifier#validation-regular-expressions
    Regex rx = new Regex(@"^dtmi:[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?(?::[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?)*;[1-9][0-9]{0,8}$");
    return rx.IsMatch(dtmi);
}
```

Avec le chemin d’accès obtenu et l’URL de base du référentiel, nous pouvons obtenir l’interface :

```cs
const string _repositoryEndpoint = "https://devicemodels.azure.com";

string dtmiPath = DtmiToPath(dtmi.ToString());
string fullyQualifiedPath = $"{_repositoryEndpoint}{dtmiPath}";
string modelContent = await _httpClient.GetStringAsync(fullyQualifiedPath);
```

## <a name="publish-a-model"></a>Publier un modèle

> [!Important]
> Vous devez disposer d’un compte GitHub pour pouvoir envoyer des modèles au DMR public.

1. Dupliquez le référentiel GitHub public : [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models).
1. Clonez le référentiel dupliqué. Facultativement, vous pouvez créer une branche pour que vos modifications restent isolées de la branche `main`.
1. Ajoutez les nouvelles interfaces au dossier `dtmi` à l’aide de la convention nom de dossier/nom de fichier. Consultez l’outil [add-model](#add-model).
1. Validez les modèles d’appareil localement à l’aide de la section relative aux [scripts de validation des modifications](#validate-files).
1. Validez les modifications localement et envoyez vers votre duplication.
1. À partir de votre duplication (fork), créez une PR qui cible la branche `main`. Consultez les documents relatifs à la [création d’une requête de problème ou d’une demande de tirage (pull request)](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request).
1. Passez en revue les [conditions requises pour une demande de tirage](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md).

La PR déclenche une série d’actions GitHub qui valident les nouvelles interfaces envoyées et s’assurent que votre PR est conforme à toutes les vérifications.

Microsoft répond à une PR avec toutes les vérifications dans les trois jours ouvrables.

### <a name="add-model"></a>add-model

Les étapes suivantes vous montrent comment le script add-model.js vous aide à ajouter une nouvelle interface. Ce script nécessite l’exécution de Node.js :

1. À partir d’une invite de commandes, accédez au référentiel Git local
1. Exécutez `npm install`
1. Exécutez `npm run add-model <path-to-file-to-add>`

Vérifiez la sortie de la console pour tout message d’erreur.

### <a name="local-validation"></a>Validation locale

Vous pouvez exécuter les mêmes vérifications de validation localement avant d’envoyer la PR pour aider à diagnostiquer les problèmes à l’avance.

#### <a name="validate-files"></a>validate-files

`npm run validate-files <file1.json> <file2.json>` vérifie que le chemin d’accès au fichier correspond aux noms de dossier et de fichier attendus.

#### <a name="validate-ids"></a>validate-ids

`npm run validate-ids <file1.json> <file2.json>` vérifie que tous les ID définis dans le document utilisent la même racine que l’ID principal.

#### <a name="validate-deps"></a>validate-deps

`npm run validate-deps <file1.json> <file2.json>` vérifie que toutes les dépendances sont disponibles dans le dossier `dtmi`.

#### <a name="validate-models"></a>validate-models

Vous pouvez exécuter l’[exemple de validation DTDL](https://github.com/Azure-Samples/DTDL-Validator) pour valider vos modèles d’appareil localement.

## <a name="next-steps"></a>Étapes suivantes

Nous vous recommandons maintenant de consulter [Architecture IoT Plug-and-Play](concepts-architecture.md).
