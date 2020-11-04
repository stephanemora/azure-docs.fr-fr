---
author: baanders
description: Inclure un fichier pour les tutoriels Azure Digital Twins - Configuration de l’exemple de projet
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: f5b332415f1abf7d4c1002bdd4f3bfcef12f1267
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93136118"
---
## <a name="configure-the-sample-project"></a>Configurer l’exemple de projet

Ensuite, configurez un exemple d’application cliente qui interagit avec votre instance Azure Digital Twins.

Sur votre ordinateur, accédez au fichier que vous avez téléchargé précédemment à partir des [*exemples Azure Digital Twins de bout en bout*](/samples/azure-samples/digital-twins-samples/digital-twins-samples) (et décompressez-le si vous ne l’avez pas déjà fait).

Une fois dans le dossier, accédez à _AdtSampleApp_. Ouvrez _**AdtE2ESample.sln**_ dans Visual Studio 2019. 

Dans Visual Studio, sélectionnez le fichier _SampleClientApp > **appsettings.json**_ pour l’ouvrir dans la fenêtre d’édition. Il s’agit d’un fichier JSON prédéfini avec les variables de configuration nécessaires pour exécuter le projet.

Dans le corps du fichier, remplacez `instanceUrl` par l’URL *hostName* de votre instance Azure Digital Twins (en plaçant *https://* devant, comme indiqué ci-dessous).

```json
{
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

Enregistrez et fermez le fichier. 

Ensuite, configurez le fichier *appsettings.json* à copier dans le répertoire de sortie lorsque vous créez *SampleClientApp*. Pour ce faire, sélectionnez avec le bouton droit le fichier *appsettings.json* , puis choisissez *Propriétés*. Dans l’inspecteur *Propriétés* , remplacez la valeur de la propriété *Copier dans le répertoire de sortie* par *Copier si plus récent*.

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Extrait de la fenêtre Visual Studio, qui affiche le volet Explorateur de solutions avec le fichier appsettings.json mis en évidence, ainsi que le volet Propriétés avec la propriété « Copier dans le répertoire de sortie » définie sur « Copier si plus récent »" border="false" lightbox="../articles/digital-twins/media/includes/copy-config.png":::

Gardez le projet _**AdtE2ESample**_ ouvert dans Visual Studio pour continuer à l’utiliser dans le tutoriel.

