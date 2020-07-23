---
title: Fichier include
description: Fichier include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 2d0e171807985deaebe8faa625d6b767c2a3efd7
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86218230"
---
La configuration suivante a été utilisée pour les étapes ci-dessous :

- Ordinateur : Ubuntu Server 18.04
- Dépendances : strongSwan


Utilisez les commandes suivantes pour installer la configuration strongSwan requise :

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

Utilisez la commande suivante pour installer l’interface de ligne de commande Azure :

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Instructions supplémentaires sur l’installation de l’interface Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)
