---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 05/25/2021
ms.openlocfilehash: 29b1bbfab92ffbb97086f01c23913f01aaac1d05
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110369106"
---
Dans Visual Studio Code, votre projet d’application logique a l’un des types suivants :

* Extension basée sur un bundle (Node.js), qui est le type par défaut
* NuGet basé sur un package (.NET), que vous pouvez convertir à partir du type par défaut

En fonction de ces types, votre projet comprend des dossiers et des fichiers légèrement différents. Un projet NuGet inclut un dossier .bin qui contient des packages et d’autres fichiers de bibliothèque. Un projet basé sur un bundle n’inclut pas le dossier .bin ni d’autres fichiers. Certains scénarios exigent un projet NuGet pour que votre application s’exécute, par exemple quand vous voulez développer et exécuter des opérations intégrées personnalisées. Pour plus d’informations sur la conversion de votre projet pour utiliser NuGet, consultez [Activer la création de connecteurs intégrés](../articles/logic-apps/create-single-tenant-workflows-visual-studio-code.md#enable-built-in-connector-authoring).

Pour le projet basé sur un bundle par défaut, votre projet a une structure de dossiers et de fichiers similaire à celle de l’exemple suivant :

```text
MyBundleBasedLogicAppProjectName
| .vscode
| Artifacts
  || Maps 
     ||| MapName1
     ||| ...
  || Schemas
     ||| SchemaName1
     ||| ...
| WorkflowName1
  || workflow.json
  || ...
| WorkflowName2
  || workflow.json
  || ...
| workflow-designtime
| .funcignore
| connections.json
| host.json
| local.settings.json
```

Au niveau racine de votre projet se trouvent les fichiers et dossiers suivants avec d’autres éléments :

| Nom | Fichier ou dossier | Description |
|------|----------------|-------------|
| **.vscode** | Dossier | Contient les fichiers de paramètres liés à Visual Studio Code, comme les fichiers extensions.json, launch.json, settings.json, et tasks.json. |
| **Artefacts** | Dossier | Contient les artefacts de compte d’intégration que vous définissez et utilisez dans des workflows qui prennent en charge les scénarios interentreprises (B2B, Business-to-Business). Par exemple, l’exemple de structure comprend des mappages et des schémas pour les opérations de transformation et de validation XML. |
| **<WorkflowName>** | Dossier | Pour chaque workflow, le dossier <WorkflowName> inclut un fichier workflow.json, qui contient la définition JSON sous-jacente de ce workflow. |
| **workflow-designtime** | Dossier | Contient les fichiers de paramètres liés à l’environnement de développement. |
| **.funcignore** | Fichier | Contient des informations relatives à votre ensemble d’outils [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) installé. |
| **connections.json** | Fichier | Contient les métadonnées, les points de terminaison et les clés de toutes les connexions managées et fonctions Azure utilisées par vos workflows. <p><p>**Important** : Pour utiliser des connexions et des fonctions différentes pour chaque environnement, veillez à paramétrer ce fichier **connections.json** et mettre à jour les points de terminaison. |
| **host.json** | Fichier | Contient des paramètres de configuration et des valeurs spécifiques au runtime, par exemple les limites par défaut pour la plateforme, les applications logiques, les workflows, les déclencheurs et les actions Azure Logic Apps à locataire unique. Au niveau racine de votre projet d’application logique, le fichier de métadonnées **host.json** contient les paramètres de configuration et les valeurs par défaut que *tous les workflows* d’une même application logique utilisent lors de l’exécution, que ce soit localement ou dans Azure. |
| **local.settings.json** | Fichier | Contient les paramètres d’application, les chaînes de connexion et d’autres paramètres utilisés par vos workflows lors d’une exécution locale. En d’autres termes, ces paramètres et valeurs s’appliquent *uniquement* quand vous exécutez vos projets dans votre environnement de développement local. Lors d’un déploiement sur Azure, le fichier et les paramètres sont ignorés et ne sont pas inclus dans votre déploiement. <p><p>Ce fichier stocke les paramètres et les valeurs sous la forme de *variables d’environnement local* utilisées par vos outils de développement locaux comme valeurs `appSettings`. Vous pouvez appeler et référencer ces variables d’environnement au moment de l’exécution et au moment du déploiement à l’aide des *paramètres d’application* et des *configurations*. <p><p>**Important** : Le fichier **local.settings.json** peut contenir des secrets. Par conséquent, veillez à exclure également ce fichier du contrôle de code source de votre projet. |
||||