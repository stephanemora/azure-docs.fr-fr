---
title: Didacticiel sur l’installation de l’appareil physique Azure Data Box Edge | Microsoft Docs
description: Le deuxième didacticiel d’installation de l’Azure Data Box Edge fournit des explications sur le déballage, le montage sur rack et le câblage de l’appareil physique.
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/08/2018
ms.author: alkohli
ms.custom: ''
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 17602abd9dab590c806c33779cf127f9a5305f64
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2018
ms.locfileid: "48832902"
---
# <a name="tutorial-install-azure-data-box-edge-preview"></a>Didacticiel : Installer Azure Data Box Edge (préversion)

Ce tutoriel explique comment installer un appareil physique Data Box Edge. La procédure d’installation comprend le déballage, le montage sur rack et le câblage de l’appareil. 

L’installation dure environ 2 heures.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Déballage de l’appareil
> * Montage de l’appareil sur rack
> * Câblage de l’appareil

> [!IMPORTANT]
> Data Box Edge est en préversion. Veuillez lire les [conditions d’utilisation de la préversion Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) avant de commander et déployer cette solution.

## <a name="prerequisites"></a>Prérequis

Voici les conditions préalables à l’installation d’un appareil physique.

### <a name="for-the-data-box-edge-resource"></a>Pour la ressource Data Box Edge

Avant de commencer, assurez-vous que :

* Vous avez terminé toutes les étapes de la rubrique [Préparer le portail pour Data Box Edge](data-box-edge-deploy-prep.md).
    * Vous avez créé la ressource Data Box Edge pour déployer votre appareil.
    * Vous avez créé la clé d’activation pour activer votre appareil avec la ressource Data Box Edge.

 
### <a name="for-the-data-box-edge-physical-device"></a>Pour l’appareil physique Data Box Edge

Avant de déployer un appareil :

- Vérifiez que l’appareil est posé en toute sécurité sur une surface de travail plate, stable et de niveau (ou similaire).
- Vérifiez que le lieu prévu pour l’installation dispose des éléments suivants :
    - Alimentation secteur standard provenant d’une source indépendante, ou
    - Une unité de distribution d’énergie (PDU) en rack équipée d’un onduleur (UPS).
- Assurez-vous qu’un emplacement de 1U est disponible sur le rack dans lequel vous avez l’intention de monter l’appareil.

### <a name="for-the-network-in-the-datacenter"></a>Pour le réseau dans le centre de données

Avant de commencer :

- Passez en revue les exigences de mise en réseau pour déployer Data Box Edge et configurer le réseau du centre de données conformément à la configuration requise. Pour plus d’informations, consultez la [Configuration requise du réseau pour Data Box Edge](data-box-gateway-system-requirements.md#networking-requirements).
- Assurez-vous que la bande passante Internet minimale est de 20 Mbits/s pour un fonctionnement optimal de l’appareil.


## <a name="unpack-the-device"></a>Déballez l’appareil

Cet appareil est livré dans un seul carton. Procédez comme suit pour déballer votre appareil. 

1. Placez le carton sur une surface plane et droite.
2. Vérifiez que le carton et le polystyrène ne comportent pas de trace d’impacts, de coupures, d’infiltrations d’eau ou tout autre type de dégâts. Si le carton ou le reste de l’emballage vous semble trop endommagé, ne l’ouvrez pas. Contactez le Support Microsoft pour savoir si l’appareil est en état de marche.
3. Déballez le carton. Une fois le carton déballé, vous devez disposer des éléments suivants :
    - 1 appareil Edge dans son boîtier
    - 2 câbles d’alimentation
    - 1 kit de montage en rack coulissant sans outil (deux rails latéraux et matériel de montage inclus)
4. Si vous n’avez reçu aucun des éléments énumérés ci-dessus, contactez le support technique Data Box Edge. L’étape suivante consiste à monter votre appareil en rack.


## <a name="rack-the-device"></a>Montage de l’appareil en rack

L’appareil doit être installé dans un rack standard de 19 pouces. Suivez la procédure suivante pour monter votre appareil dans un rack standard de 19 pouces équipé de montants avant et arrière.

> [!IMPORTANT]
> Les appareils Data Box Edge doivent être montés en rack pour fonctionner correctement.


1. Tirez sur le système de déverrouillage frontal pour détacher le rail intérieur de l’ensemble coulissant. Déverrouillez le verrou à cran et enfoncez le rail central vers l’intérieur pour retirer le rail. Les rails intérieur et extérieur doivent désormais être séparés.

    ![Installation des rails de montage en rack](./media/data-box-edge-deploy-install/rack-mount-rail-1.png)

2. Installez maintenant les rails extérieurs sur les montants verticaux de l’armoire à rack. Pour faciliter l’orientation, la mention Avant figure sur les glissières de rail pour indiquer que cette extrémité se positionne à l’avant du boîtier. 
    
    1. Recherchez les ergots à l’avant et à l’arrière du rail. Allongez le rail pour qu’il s’insère entre les montants du rack. Fixez d’abord le rail extérieur à l’arrière du rack. Ajustez le support de montage arrière de façon à le positionner à l’intérieur des trous de montage du rack arrière.   
    2. Maintenez le déclencheur du support arrière enfoncé afin de dégager les crochets métalliques. Alignez et insérez-les dans les trous de montage, puis relâchez le déclencheur.
    3. Alignez le support avant avec le trou de montage.
    4. Le support avant doit désormais être fixé sur le rack. Si nécessaire, vous pouvez utiliser des vis M5 X 10L pour fixer les rails sur les montants. 

    ![Installation des rails de montage en rack](./media/data-box-edge-deploy-install/rack-mount-rail-2.png)

3. Pour fixer le rail intérieur sur le châssis, assurez-vous que les trous de fixation du rail intérieur sont alignés avec les goupilles de positionnement situées sur le côté du châssis. Veillez à ce que les têtes des goupilles de positionnement du châssis dépassent à travers les trous de fixation du rail intérieur. Tirez le rail vers l’avant du châssis jusqu’à ce qu’il se mette en place avec un clic audible. Répétez l’opération avec l’autre rail intérieur. Poussez le châssis avec le rail intérieur dans la glissière pour terminer le montage du rack.

    ![Installation des rails de montage en rack](./media/data-box-edge-deploy-install/rack-mount-rail-3.png)

## <a name="cable-the-device"></a>Câblage de l’appareil

Les procédures suivantes expliquent comment effectuer le câblage d’alimentation et de réseau de votre appareil Edge.

## <a name="prerequisites"></a>Prérequis

Avant de commencer le câblage de votre appareil, vous devez disposer des éléments suivants :

- Votre appareil physique Edge, déballé et monté en rack.
- 2 câbles d’alimentation. 
- 2 câbles réseau RJ-45 de 1 GbE et 4 câbles cuivre SFP+ de 25 GbE.
- Accès à deux unités de distribution d’énergie (recommandé).

Votre appareil Edge est équipé de 8 disques SSD NVMe. Le panneau avant comporte également des LED d’état et des boutons d’alimentation. L’appareil comprend des unités d’alimentation (PSU) redondantes à l’arrière. Votre appareil comporte six interfaces réseau : deux interfaces 1 Gbit/s et quatre interfaces 25 Gbit/s. Votre appareil est équipé d’un contrôleur de gestion de la carte de base (BMC). Identifiez les différents ports sur le fond de panier de votre appareil.
 
  ![Fond de panier d’un appareil câblé](./media/data-box-edge-deploy-install/backplane-cabled.png)
 
Procédez comme suit pour brancher l’alimentation et le réseau de votre appareil.

1. Branchez les câbles d’alimentation à chacune des unités d’alimentation dans le boîtier. Pour assurer une haute disponibilité, installez et connectez les deux unités d’alimentation à des sources d’alimentation différentes.
2. Fixez les câbles d’alimentation aux unités de distribution d’alimentation (PDU) du rack. Assurez-vous que les deux unités d’alimentation utilisent des sources d’alimentation distinctes.
3. Connectez l’interface réseau 1-GbE PORT 1 à l’ordinateur utilisé pour configurer l’appareil physique. Le PORT 1 constitue l’interface de la gestion dédiée.
4. Connectez l’interface réseau 1-GbE PORT 2 au réseau du centre de données/Internet à l’aide des câbles réseau RJ-45. 
5. Connectez les quatre interfaces réseau 25 GbE PORT 3, PORT 4, PORT 5 et PORT 6 au réseau du centre de données/Internet à l’aide des câbles de cuivre SFP+. 

> [!NOTE]
> - Au moins une interface de réseau de données - PORT 2, PORT 3, PORT 4, PORT 5 ou PORT 6 doit être connectée à Internet (connectivité à Azure). 
> - Pour envoyer des données à Azure, nous vous recommandons d’utiliser une interface réseau 25 GbE telle que PORT 3, PORT 4, PORT 5 ou PORT 6. 
> - L’appareil Edge doit être connecté au réseau du centre de données pour pouvoir recevoir les informations provenant des serveurs des sources de données.  


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez approfondi vos connaissances sur Data Box Edge et avez notamment appris à :

> [!div class="checklist"]
> * Déballez l’appareil
> * Montage de l’appareil en rack
> * Câblage de l’appareil

Passez au didacticiel suivant pour savoir comment connecter, configurer et activer votre appareil.

> [!div class="nextstepaction"]
> [Connecter et configurer votre Data Box Edge](./data-box-edge-deploy-connect-setup-activate.md)


