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
ms.openlocfilehash: 089ad704a466590a9649107fef245a88d6a4d6e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244770"
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

[Instructions supplémentaires sur l’installation de l’interface Azure CLI](/cli/azure/install-azure-cli-apt)