---
title: Surveiller la connectivité des appareils à l’aide du IoT Central Explorer d’Azure
description: Surveillez les messages de l’appareil et observez les modifications de jumeau d’appareil via l’interface CLI d’IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 16cb27ab330118d1bb59cf4f3d782bf55fa28d43
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60517115"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Surveiller la connectivité des appareils à l’aide du IoT Central Explorer d’Azure

*Cette rubrique s’applique aux générateurs et aux administrateurs.*

Utilisez l’interface CLI d’IoT Central Explorer pour consulter les messages que vos appareils envoient à IoT Central et observez les modifications apportées au jumeau IoT Hub. Vous pouvez utiliser cet outil open source pour obtenir un meilleur insight de l’état de la connectivité des appareils. Vous pouvez aussi diagnostiquer les problèmes des messages d’appareil n’atteignant pas le cloud ou les appareils qui ne répondent pas aux modifications de jumeau.

[Visitez le référentiel CTOI-explorer dans GitHub.](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Prérequis

+ Node.js version 8.x ou ultérieure - https://nodejs.org
+ Un administrateur de votre application doit générer un jeton d’accès que vous pouvez utiliser dans l’Explorateur de CTOI

## <a name="install-iotc-explorer"></a>Installer CTOI-explorer

Exécutez la commande suivante dans la console de ligne de commande pour installer :

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> Vous devez généralement exécuter la commande d’installation avec `sudo` dans les environnements de type Unix.

## <a name="run-iotc-explorer"></a>Exécuter l’Explorateur de CTOI

Les sections suivantes décrivent les commandes et options que vous pouvez utiliser lorsque vous exécutez courants `iotc-explorer`. Pour afficher l’ensemble complet des commandes et options, passer `--help` à `iotc-explorer` ou l’un de ses sous-commandes.

### <a name="login"></a>Connexion

Avant de continuer, vous devez demander à un administrateur de votre application IoT Central d’obtenir un jeton d’accès que vous pouvez utiliser. L’administrateur effectue les étapes suivantes :

1. Accédez à **Administration** puis **de jetons d’accès**.
1. Sélectionnez **générer le jeton**.
    ![Capture d’écran de la page Jeton accès](media/howto-use-iotc-explorer/accesstokenspage.png)

1. Entrez un nom de jeton, sélectionnez **suivant**, puis **copie**.
    > [!NOTE]
    > La valeur du jeton ne s’affiche qu’une seule fois, donc il doit être copié avant de fermer la boîte de dialogue. Après avoir fermé la boîte de dialogue, il n’est jamais affiché à nouveau.

    ![Copier la capture d’écran de la boîte de dialogue du jeton accès](media/howto-use-iotc-explorer/copyaccesstoken.png)

Vous pouvez utiliser le jeton pour vous connecter à l’interface CLI comme suit :

```cmd/sh
iotc-explorer login "<Token value>"
```

Si vous préférez ne pas le jeton persistante dans votre historique de l’interpréteur de commandes, vous pouvez laisser le jeton out, à la place de la fournir lorsque vous y êtes invité :

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>Surveiller les messages des appareils

Vous pouvez regarder les messages provenant d’un appareil spécifique ou de tous les appareils dans votre application à l’aide de la commande `monitor-messages`. Cette commande démarre un observateur qui génère en permanence de nouveaux messages dès leur arrivée :

Pour observer tous les appareils dans votre application, exécutez la commande suivante :

```cmd/sh
iotc-explorer monitor-messages
```

Sortie :

![sortie de la commande monitor-messages](media/howto-use-iotc-explorer/monitormessages.png)

Pour visionner un périphérique spécifique, ajoutez simplement l’id d’appareil à la fin de la commande :

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

Vous pouvez également générer un format plus adapté à l’ordinateur en ajoutant le `--raw` option à la commande :

```
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Obtenir un jumeau d’appareil

Vous pouvez utiliser la commande `get-twin` pour obtenir le contenu du jumeau d’un appareil IoT Central. Pour ce faire, exécutez la commande suivante :

```cmd/sh
iotc-explorer get-twin <your-device-id>
```

Sortie :

![sortie de la commande get-twin](media/howto-use-iotc-explorer/getdevicetwin.png)

Comme avec `monitor-messages`, vous pouvez obtenir une sortie plus adaptée à la machine en passant l’option `--raw` :

```cmd/sh
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris comment utiliser l’Explorateur de IoT Central, l’étape suivante suggérée consiste à Explorer [la gestion des appareils IoT Central](howto-manage-devices.md).
