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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60517115"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Surveiller la connectivité des appareils à l’aide du IoT Central Explorer d’Azure

*Cette rubrique s’applique aux générateurs et aux administrateurs.*

Utilisez l’interface CLI d’IoT Central Explorer pour consulter les messages que vos appareils envoient à IoT Central et observez les modifications apportées au jumeau IoT Hub. Vous pouvez utiliser cet outil open source pour obtenir un meilleur insight de l’état de la connectivité des appareils. Vous pouvez aussi diagnostiquer les problèmes des messages d’appareil n’atteignant pas le cloud ou les appareils qui ne répondent pas aux modifications de jumeau.

[Visitez le dépôt iotc-explorer dans GitHub.](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Prérequis

+ Node.js version 8.x ou ultérieure - https://nodejs.org
+ Un administrateur de votre application doit générer un jeton d’accès que vous pouvez utiliser dans iotc-explorer

## <a name="install-iotc-explorer"></a>Installer iotc-explorer

Exécutez la commande suivante dans la console de ligne de commande pour installer :

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> Vous devez généralement exécuter la commande d’installation avec `sudo` dans des environnements de type Unix.

## <a name="run-iotc-explorer"></a>Exécuter iotc-explorer

Les sections suivantes décrivent les commandes et les options courantes que vous pouvez utiliser quand vous exécutez `iotc-explorer`. Pour voir l’ensemble complet des commandes et des options, passez `--help` à `iotc-explorer` ou à une de ses sous-commandes.

### <a name="login"></a>Connexion

Avant de continuer, vous devez demander à un administrateur de votre application IoT Central d’obtenir un jeton d’accès que vous pouvez utiliser. L’administrateur effectue les étapes suivantes :

1. Accédez à **Administration**, puis **Jetons d’accès**.
1. Sélectionnez **Générer un jeton**.
    ![Capture d’écran de la page Jeton accès](media/howto-use-iotc-explorer/accesstokenspage.png)

1. Entrez un nom de jeton, sélectionnez **Suivant**, puis **Copier**.
    > [!NOTE]
    > La valeur du jeton n’est montrée qu’une seule fois : vous devez donc la copier avant de fermer la boîte de dialogue. Une fois la boîte de dialogue fermée, elle n’est jamais plus montrée.

    ![Copier la capture d’écran de la boîte de dialogue du jeton accès](media/howto-use-iotc-explorer/copyaccesstoken.png)

Vous pouvez utiliser le jeton pour vous connecter à l’interface CLI comme suit :

```cmd/sh
iotc-explorer login "<Token value>"
```

Si vous préférez ne pas conserver le jeton dans l’historique de votre shell, vous pouvez omettre le jeton et le fournir quand vous y êtes invité :

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>Surveiller les messages des appareils

Vous pouvez regarder les messages provenant d’un appareil spécifique ou de tous les appareils dans votre application à l’aide de la commande `monitor-messages`. Cette commande démarre un observateur qui génère en continu de nouveaux messages dès leur arrivée :

Pour observer tous les appareils dans votre application, exécutez la commande suivante :

```cmd/sh
iotc-explorer monitor-messages
```

Sortie :

![sortie de la commande monitor-messages](media/howto-use-iotc-explorer/monitormessages.png)

Pour observer un appareil spécifique, ajoutez simplement l’ID d’appareil à la fin de la commande :

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

Vous pouvez générer une sortie dans un format plus adapté à la machine en ajoutant l’option `--raw` à la commande :

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

Maintenant que vous avez découvert comment utiliser IoT Central Explorer, l’étape suivante conseillée consiste à explorer la [Gestion des appareils IoT Central](howto-manage-devices.md).
