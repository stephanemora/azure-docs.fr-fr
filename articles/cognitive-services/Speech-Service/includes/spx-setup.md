---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: abfb4f6ba9452581811db1f462089cbafc771266
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544906"
---
## <a name="prerequisites"></a>Prérequis

Le seul prérequis est un abonnement Azure Speech. Si vous n’en avez pas, consultez le [guide](../get-started.md#new-resource) pour en créer un.

## <a name="download-and-install"></a>Télécharger et installer

#### <a name="windows-install"></a>[Installation Windows](#tab/windowsinstall)

Procédez ainsi pour installer l’interface CLI Speech sur Windows :

1. Installez [.NET Framework 4.7](https://dotnet.microsoft.com/download/dotnet-framework/net471) ou [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0).
2. Téléchargez [l’archive zip](https://aka.ms/speech/spx-zips.zip) de l’interface CLI Speech, puis extrayez-la.
3. Accédez au répertoire racine `spx-zips` que vous avez extrait du téléchargement, puis extrayez le sous-répertoire dont vous avez besoin (`spx-net471` pour .NET Framework 4.7 ou `spx-netcore-win-x64` pour .NET Core 3.0 sur un processeur x64).

Dans l’invite de commandes, accédez à l’emplacement de votre répertoire, puis tapez `spx` pour afficher l’aide de l’interface CLI Speech.

> [!NOTE]
> Sur Windows, l’interface CLI de Speech peut afficher uniquement les polices disponibles à l’invite de commandes sur l’ordinateur local.
> Le [Terminal Windows](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) prend en charge toutes les polices produites de manière interactive par l’interface CLI de Speech.
> Si la sortie est effectuée dans un fichier, un éditeur de texte tel que le Bloc-notes ou un navigateur web tel que Microsoft Edge peut également afficher toutes les polices.

> [!NOTE]
> PowerShell ne vérifie pas le répertoire local lors de la recherche d’une commande. Dans PowerShell, changez de répertoire en accédant à l’emplacement de `spx` et appelez l’outil en entrant `.\spx`.
> Si vous ajoutez ce répertoire à votre chemin, PowerShell et l’invite de commandes Windows recherchent `spx` à partir de n’importe quel répertoire sans inclure le préfixe `.\`.

#### <a name="linux-install"></a>[Installation Linux](#tab/linuxinstall)

Procédez ainsi pour installer l’interface CLI Speech sur Linux, sur un processeur x64 :

1. Installez [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0).
2. Téléchargez [l’archive zip](https://aka.ms/speech/spx-zips.zip) de l’interface CLI Speech, puis extrayez-la.
3. Accédez au répertoire racine `spx-zips` que vous avez extrait du téléchargement, puis extrayez `spx-netcore-30-linux-x64` dans un nouveau répertoire `~/spx`.
4. Dans un terminal, tapez les commandes suivantes :
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

Tapez `spx` pour afficher l’aide de l’interface CLI Speech.

***

## <a name="create-subscription-config"></a>Création d’une configuration d’abonnement

Pour pouvoir utiliser l’interface CLI Speech, vous devez entrer les informations relatives à votre clé d’abonnement et à votre région Speech. Consultez la page de [prise en charge des régions](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) pour rechercher l’identificateur de votre région. Une fois que vous avez récupéré votre clé d’abonnement et votre identificateur de la région (par exemple, `eastus`, `westus`), exécutez les commandes suivantes.

```shell
spx config @key --set YOUR-SUBSCRIPTION-KEY
spx config @region --set YOUR-REGION-ID
```

L’authentification de votre abonnement est maintenant stockée pour les futures demandes SPX. Si vous devez supprimer l’une de ces valeurs stockées, exécutez `spx config @region --clear` ou `spx config @key --clear`.
