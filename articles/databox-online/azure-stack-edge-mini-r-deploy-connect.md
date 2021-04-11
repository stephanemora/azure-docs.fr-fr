---
title: Tutoriel pour se connecter à Azure Stack Edge Mini R dans le portail Azure
description: Découvrez comment vous connecter à votre appareil Azure Stack Edge Mini R en utilisant l’interface utilisateur web locale.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 57d11e1c4109caded4287592a6d71a803e44b68e
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061602"
---
# <a name="tutorial-connect-to-azure-stack-edge-mini-r"></a>Tutoriel : Se connecter à Azure Stack Edge Mini R

Ce tutoriel décrit la manière de vous connecter à votre appareil Azure Stack Edge Mini R en utilisant l’interface utilisateur web locale.

Le processus de connexion peut prendre environ 5 minutes.

Ce tutoriel vous fournira des informations sur :

> [!div class="checklist"]
>
> * Prérequis
> * Se connecter à un appareil physique



## <a name="prerequisites"></a>Prérequis

Avant de configurer et d’activer votre appareil Azure Stack Edge, vérifiez que :

* Vous avez installé l’appareil physique, comme indiqué dans [Installer Azure Stack Edge](azure-stack-edge-mini-r-deploy-install.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Se connecter à la configuration de l’interface utilisateur web locale

1. Configurez l'adaptateur Ethernet sur votre ordinateur pour vous connecter à l'appareil Azure Stack Edge Pro avec l'adresse IP statique 192.168.100.5 et le sous-réseau 255.255.255.0.

2. Connectez l’ordinateur au PORT 1 de votre appareil. Si vous connectez l’ordinateur directement à l’appareil (sans commutateur), utilisez un câble croisé ou une carte Ethernet USB. Utilisez l’illustration ci-dessous pour identifier le PORT 1 sur votre appareil.

    ![Câblage pour le Wi-Fi](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)

[!INCLUDE [azure-stack-edge-gateway-delpoy-connect](../../includes/azure-stack-edge-gateway-deploy-connect.md)]


## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous à présenté les point suivants :

> [!div class="checklist"]
> * Prérequis
> * Se connecter à un appareil physique


Pour apprendre à configurer les paramètres réseau sur votre appareil, consultez :

> [!div class="nextstepaction"]
> [Configurer le réseau](./azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)
