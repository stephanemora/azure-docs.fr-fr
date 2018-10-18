---
title: Surveiller la connectivité des appareils à l’aide du IoT Central Explorer d’Azure
description: Surveillez les messages de l’appareil et observez les modifications de jumeau d’appareil via l’interface CLI d’IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 962f394607d20869bf00db624533996b0060eaf2
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45987240"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Surveiller la connectivité des appareils à l’aide du IoT Central Explorer d’Azure

*Cette rubrique s’applique aux générateurs et aux administrateurs.*

Utilisez l’interface CLI d’IoT Central Explorer pour consulter les messages que vos appareils envoient à IoT Central et observez les modifications apportées au jumeau IoT Hub. Vous pouvez utiliser cet outil open source pour obtenir un meilleur insight de l’état de la connectivité des appareils. Vous pouvez aussi diagnostiquer les problèmes des messages d’appareil n’atteignant pas le cloud ou les appareils qui ne répondent pas aux modifications de jumeau.

## <a name="visit-the-iotc-explorer-repo-in-githubhttpsakamsiotciotcexplorercligithub"></a>[Visitez le référentiel iotc-explorer dans GitHub](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Prérequis
+ Node.js version 8.x ou ultérieure - https://nodejs.org
+ Vous devrez demander à un administrateur de votre application de générer un jeton d’accès que vous pouvez utiliser dans iotc-explorer

## <a name="installing-iotc-explorer"></a>Installer iotc-explorer

Exécutez la commande suivante dans la console de ligne de commande pour installer :

```
npm install -g iotc-explorer
```

> [!NOTE]
> Vous devrez généralement exécuter la commande d’installation avec `sudo` dans des environnements de type Unix.

## <a name="running-iotc-explorer"></a>Exécuter iotc-explorer

Vous trouverez ci-dessous des commandes et des options courantes que vous pouvez exécuter lorsque vous utilisez `iotc-explorer`. Pour afficher l’ensemble complet des commandes et options, vous pouvez passer `--help` à `iotc-explorer` ou une de ses sous-commandes.

### <a name="login"></a>Connexion

Avant de continuer, vous devez demander à un administrateur de votre application IoT Central d’obtenir un jeton d’accès que vous pouvez utiliser. L’administrateur effectue les étapes suivantes :
1. Accédez à **Administration/Jetons d’accès**. 
1. Cliquez sur **Générer**.
![Capture d’écran de la page Jeton accès](media/howto-use-iotc-explorer/accesstokenspage.png)

1. Entrez un nom de jeton, cliquez sur **Suivant**, puis **copiez la valeur du jeton**.
    > [!NOTE]
    > La valeur du jeton ne s’affichera qu’une fois, donc elle doit être copiée avant de fermer la boîte de dialogue. Après avoir fermé la boîte de dialogue, elle n’apparaîtra plus.

    ![Copier la capture d’écran de la boîte de dialogue du jeton accès](media/howto-use-iotc-explorer/copyaccesstoken.png)

Vous pouvez ensuite utiliser ce jeton pour vous connecter à l’interface CLI en exécutant :

```sh
iotc-explorer login "<Token value>"
```

Si vous préférez ne pas conserver le jeton dans l’historique de votre interpréteur de commandes, vous pouvez omettre le jeton et le fournir uniquement lorsque vous y êtes invité :

```
iotc-explorer login
```

### <a name="monitor-device-messages"></a>Surveiller les messages des appareils

Vous pouvez regarder les messages provenant d’un appareil spécifique ou de tous les appareils dans votre application à l’aide de la commande `monitor-messages`. Ceci démarrera un observateur qui traitera en permanence les nouveaux messages au fur et à mesure qu’ils vous parviennent.

Pour observer tous les appareils dans votre application, exécutez la commande suivante :

```
iotc-explorer monitor-messages
```
Sortie : ![sortie de la commande monitor-messages](media/howto-use-iotc-explorer/monitormessages.PNG)

Pour visionner un appareil spécifique, ajoutez simplement l’ID d’appareil à la fin de la commande :

```
iotc-explorer monitor-messages <your-device-id>
```

Vous pouvez également faire en sorte que la commande génère la sortie dans un format plus adapté à la machine en ajoutant l’option `--raw` à la commande :

```
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Obtenir un jumeau d’appareil

Vous pouvez utiliser la commande `get-twin` pour obtenir le contenu du jumeau d’un appareil IoT Central. Pour ce faire, exécutez la commande suivante :

```
iotc-explorer get-twin <your-device-id>
```

Sortie : ![sortie de la commande get-twin](media/howto-use-iotc-explorer/getdevicetwin.PNG)

Comme avec `monitor-messages`, vous pouvez obtenir une sortie plus adaptée à la machine en passant l’option `--raw` :

```
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris à utiliser IoT Central Explorer, l’étape suivante conseillée consiste à explorer la [Gestion des appareils IoT Central](howto-manage-devices.md).
