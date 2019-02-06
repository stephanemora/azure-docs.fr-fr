---
title: Fichier Include
description: Fichier Include
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/25/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: b335cf996de41f4eea15af1899a0c6654c2f679f
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104981"
---
## <a name="open-cli-shell"></a>Interpréteur de commandes CLI ouvert

Il est recommandé d’utiliser [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) pour exécuter des commandes CLI. **Cloud Shell** est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour effectuer les étapes mentionnées dans cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. Cliquez simplement sur le bouton Copier pour copier le code, collez-le dans Cloud Shell, puis appuyez sur Entrée pour l’exécuter. Cloud Shell peut être ouvert de plusieurs façons :

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

### <a name="login"></a>Connexion

Pour commencer à utiliser l’interpréteur de commandes CLI (dans le cloud ou localement), exécutez `az login` pour créer une connexion avec Azure.

Si l’interface CLI peut ouvrir votre navigateur par défaut, elle le fera et chargera une page de connexion par la même occasion. Sinon, vous devez ouvrir une page de navigateur et suivre les instructions sur la ligne de commande pour entrer un code d’autorisation après avoir accédé à https://aka.ms/devicelogin dans votre navigateur.

### <a name="specify-location-of-files"></a>Spécifier l’emplacement des fichiers

De nombreuses commandes CLI Media Services vous permettent de passer un paramètre avec un nom de fichier. 

Si vous utilisez **Azure Cloud Shell**, chargez votre fichier dans **Azure Cloud Shell**. Le bouton de chargement/téléchargement des fichiers se trouve en haut de la fenêtre de l’interpréteur de commandes. Ensuite, référencez le fichier comme ceci : `@{FileName}.` 

![Charger des fichiers]

Si vous utilisez l’interface de ligne de commande Azure en local, spécifiez le chemin complet du fichier. Par exemple : `@c:\tracks.json`.


[Charger des fichiers]: ./media/media-services-cli/upload-download-files.png
