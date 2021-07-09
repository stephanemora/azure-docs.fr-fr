---
author: baanders
description: Inclure un fichier pour les tutoriels Azure Digital Twins - Configuration de l’exemple de projet
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 60d65e530c9d98ac86f8d958072d2f26a68836fe
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111350632"
---
## <a name="configure-the-sample-project"></a>Configurer l’exemple de projet

Ensuite, configurez un exemple d’application cliente qui interagit avec votre instance Azure Digital Twins.

Sur votre ordinateur, accédez au fichier que vous avez téléchargé précédemment à partir des [Exemples Azure Digital Twins de bout en bout](/samples/azure-samples/digital-twins-samples/digital-twins-samples) (et décompressez-le si vous ne l’avez pas déjà fait).

Une fois dans le dossier, accédez à _AdtSampleApp_. Ouvrez _**AdtE2ESample.sln**_ dans Visual Studio 2019. 

Dans Visual Studio, sélectionnez le fichier _SampleClientApp > **appsettings.json**_ pour l’ouvrir dans la fenêtre d’édition. Il s’agit d’un fichier JSON prédéfini avec les variables de configuration nécessaires pour exécuter le projet.

Dans le corps du fichier, remplacez `instanceUrl` par l’*URL de nom d’hôte* de votre instance d’Azure Digital Twins (en ajoutant **_https://_** devant le *nom d’hôte*, comme indiqué ci-dessous).

```json
{
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-host-name>"
}
```

Enregistrez et fermez le fichier. 

Ensuite, configurez le fichier *appsettings.json* à copier dans le répertoire de sortie lorsque vous créez *SampleClientApp*. Pour ce faire, sélectionnez avec le bouton droit le fichier *appsettings.json*, puis choisissez **Propriétés**. Dans l’inspecteur **Propriétés**, recherchez la propriété *Copier dans le répertoire de sortie*. Remplacez la valeur par **Copier si plus récent** si ce n’est pas déjà le cas.

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Capture d’écran de l’Explorateur de solutions dans Visual Studio avec appsettings.jset la propriété « Copier dans le répertoire de sortie » mise en évidence dans Propriétés." border="false" lightbox="../articles/digital-twins/media/includes/copy-config.png":::

Gardez le projet _**AdtE2ESample**_ ouvert dans Visual Studio pour continuer à l’utiliser dans le tutoriel.

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](digital-twins-local-credentials-outer.md)]
