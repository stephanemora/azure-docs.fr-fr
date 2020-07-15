---
author: baanders
description: Inclure un fichier pour les tutoriels Azure Digital Twins - Configuration de l’exemple de projet
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 6edc6facc884fc6469b75b63216d6e5036321ea8
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277921"
---
## <a name="configure-the-sample-project"></a>Configurer l’exemple de projet

Ensuite, configurez un exemple d’application cliente qui interagit avec votre instance Azure Digital Twins. Si vous n’avez pas encore téléchargé l’exemple de projet, récupérez-le maintenant dans la page d’arrivée [Exemples Azure Digital Twins](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples), en sélectionnant le bouton *Télécharger le fichier ZIP* sous le titre.

Accédez au fichier téléchargé sur votre ordinateur et décompressez-le.

Dans le dossier décompressé, accédez à _AdtSampleApp/_ . Ouvrez _**AdtE2ESample.sln**_ dans Visual Studio 2019. 

Dans Visual Studio, utilisez le volet *Explorateur de solutions* pour créer une copie du fichier _SampleClientApp > **serviceConfig.json.TEMPLATE**_ (vous pouvez utiliser les menus contextuels pour copier et coller). Renommez la copie *serviceConfig.json*. Il s’agit d’un fichier JSON prédéfini avec les variables de configuration nécessaires pour exécuter le projet.

Sélectionnez le fichier *serviceConfig.json* pour l’ouvrir dans la fenêtre d’édition. Remplacez `tenantId` par votre *ID de répertoire*, `clientId` par votre *ID d’application* et `instanceUrl` par votre URL *hostName* d’instance Azure Digital Twins (en entrant *https://* devant, comme indiqué ci-dessous).

```json
{
  "tenantId": "<your-directory-ID>",
  "clientId": "<your-application-ID>",
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

Enregistrez et fermez le fichier. 

Ensuite, configurez le fichier *serviceConfig.json* à copier dans le répertoire de sortie lorsque vous créez *SampleClientApp*. Pour ce faire, sélectionnez avec le bouton droit le fichier *serviceConfig.json*, puis choisissez *Propriétés*. Dans l’inspecteur *Propriétés*, remplacez la valeur de la propriété *Copier dans le répertoire de sortie* par *Copier si plus récent*.

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Extrait de la fenêtre Visual Studio, qui affiche le volet Explorateur de solutions avec le fichier serviceConfig.json mis en surbrillance, ainsi que le volet Propriétés avec la propriété « Copier dans le répertoire de sortie » définie sur « Copier si plus récent »" border="false":::

Gardez le projet _**AdtE2ESample**_ ouvert dans Visual Studio pour continuer à l’utiliser dans le tutoriel.

