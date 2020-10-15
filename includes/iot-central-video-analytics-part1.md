---
title: Fichier include
description: Fichier include
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 7246375468b3419c3d52ee3d5a51a95aa20050a9
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876662"
---
L’exemple d’application comprend deux appareils simulés et une passerelle IoT Edge. Les tutoriels suivants présentent deux approches permettant d’expérimenter et de comprendre les fonctionnalités de la passerelle :

* Créer la passerelle IoT Edge sur une machine virtuelle Azure et connecter une caméra simulée.
* Créer la passerelle IoT Edge sur un appareil réel, tel qu’un processeur Intel NUC, et connecter une caméra réelle.

Dans ce tutoriel, vous allez apprendre à :
> [!div class="checklist"]
> * Utiliser le modèle d’application d’analytique vidéo Azure IoT Central pour créer une application de magasin de vente au détail
> * Vérifier les paramètres d’application
> * Créer un modèle d’appareil pour un appareil de passerelle IoT Edge
> * Ajouter un appareil de passerelle à votre application IoT Central

## <a name="prerequisites"></a>Prérequis

Pour réaliser cette série de tutoriels, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, vous pouvez en créer un sur la [page d’inscription à Azure](https://aka.ms/createazuresubscription).
* Si vous utilisez une caméra réelle, il vous faut une connectivité entre l’appareil IoT Edge et la caméra, de même que le canal **Real Time Streaming Protocol**.

## <a name="initial-setup"></a>Configuration initiale

Dans ces tutoriels, vous mettez à jour et utilisez plusieurs fichiers de configuration. Les versions initiales de ces fichiers sont disponibles dans le référentiel GitHub [LVA-gateway](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway). Ce référentiel comprend également un fichier texte de bloc-notes que vous pouvez télécharger et utiliser pour enregistrer les valeurs de configuration des services que vous déployez.

Créez un dossier appelé *lva-configuration* sur votre ordinateur local pour enregistrer les copies de ces fichiers. Cliquez ensuite avec le bouton droit sur chacun des liens suivants, puis sélectionnez **Enregistrer sous** pour enregistrer le fichier dans le dossier *lva-configuration* :
