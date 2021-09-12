---
title: Mettre à jour Azure Percept DK via une connexion par câble USB-C
description: Découvrez comment mettre à jour Azure Percept DK via une connexion par câble USB-C
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: e40a75f91ebf371d27b541b74ba427f42bc518f3
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114652903"
---
# <a name="update-the-azure-percept-dk-over-a-usb-c-cable-connection"></a>Mettre à jour Azure Percept DK via une connexion par câble USB-C

Ce guide explique comment mettre à jour correctement le système d’exploitation et le microprogramme de votre kit de développement via une connexion USB. Voici une vue d’ensemble de ce que vous allez faire dans le cadre de cette procédure.
1. Télécharger le package de mise à jour sur un ordinateur hôte
1. Exécuter la commande qui transfère le package de mise à jour vers le kit de développement
1. Mettre le kit de développement en mode USB à l’aide d’une connexion SSH ou des commutateurs DIP
1. Connecter le kit de développement à l’ordinateur hôte via un câble USB-C
1. Attendre la fin de la mise à jour

> [!WARNING]
> La mise à jour de votre kit de développement via une connexion USB aura pour effet de supprimer toutes les données existantes sur l’appareil, y compris les modèles et conteneurs d’IA.
>
> Suivez toutes les instructions dans l’ordre. Sauter des étapes pourrait avoir pour effet de mettre votre kit de développement dans un état inutilisable.


## <a name="prerequisites"></a>Prérequis

- Un Azure Percept DK.
- Un ordinateur hôte Windows ou Linux disposant de la fonctionnalité Wi-Fi et d’un port USB-C ou USB-A
- Un câble de conversion USB-C/USB-A (facultatif, vendu séparément).
- Un compte de connexion SSH créé dans le cadre de l’[expérience de configuration d’Azure Percept DK](./quickstart-percept-dk-set-up.md)
- Clé hexagonale livrée avec le kit de développement, pour retirer les vis situées à l’arrière du kit de développement (si vous utilisez la méthode des commutateur DIP)

> [!NOTE]
> **Utilisateurs Mac** : la mise à jour d’Azure Percept DK via une connexion USB ne fonctionne avec un ordinateur hôte Mac. 

## <a name="download-software-tools-and-update-files"></a>Télécharger les outils logiciels et les fichiers de mise à jour

1. [Outil NXP UUU](https://github.com/NXPmicro/mfgtools/releases). Téléchargez la **version la plus récente** du fichier uuu.exe (pour Windows) ou du fichier uuu (pour Linux) sous l’onglet **Ressources**. UUU est un outil créé par NXP et utilisé pour mettre à jour les cartes de développement NXP.

1. [Téléchargez les fichiers de mise à jour](https://go.microsoft.com/fwlink/?linkid=2155734). Ils sont tous contenus dans un fichier zip que vous allez extraire dans le cadre de la section suivante.

1. Vérifiez que les trois artefacts de la build sont présents :
    - Azure-Percept-DK- *&lt;numéro de version&gt;* .raw
    - fast-hab-fw.raw
    - emmc_full.txt

## <a name="set-up-your-environment"></a>Configurer votre environnement

1. Créez un dossier/répertoire sur l’ordinateur hôte à un emplacement aisément accessibles via une ligne de commande.

1. Copiez l’outil UUU (**uuu.exe** ou **uuu**) dans le nouveau dossier.

1. Extrayez les fichiers de mise à jour précédemment téléchargés dans le nouveau dossier contenant l'outil UUU.

## <a name="run-the-command-that-transfers-the-update-package-to-the-dev-kit"></a>Exécuter la commande qui transfère le package de mise à jour vers le kit de développement

1. Ouvrez une invite de commandes Windows (Démarrer > cmd) ou un terminal Linux, puis **accédez au dossier où sont stockés les fichiers de mise à jour et l’outil UUU**. 

1. Exécutez la commande suivante dans l’invite de commande ou le terminal.

    - Windows :

        ```console
        uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw 
        ```

    - Linux :

        ```bash
        sudo ./uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw
        ```

1. La fenêtre d’invite de commandes affiche un message indiquant **En attente de l’apparition d’un périphérique USB connu…** . L’outil UUU attend maintenant que le kit de développement soit détecté par l’ordinateur hôte. **Passez aux étapes suivantes et mettez le kit de développement en mode USB.**

## <a name="set-the-dev-kit-into-usb-mode"></a>Mettre le kit de développement en mode USB
Il existe deux façons de mettre le kit de développement en « mode USB », via une connexion SSH ou à l’aide des commutateurs DIP sur le kit de développement. Choisissez la méthode qui vous convient le mieux.

### <a name="using-ssh"></a>Utilisation de SSH
La connexion SSH est la méthode préconisée, et la plus sûre, pour mettre le kit de développement en mode USB. Toutefois, vous devez impérativement pouvoir vous connecter au point d’accès Wi-Fi du kit de développement. Si vous ne parvenez pas à vous connecter au point d’accès Wi-Fi du kit de développement, vous devez utiliser la méthode des commutateurs DIP.

1. Connectez le câble USB-C fourni au port USB-C du kit de développement et au port USB-C de l’ordinateur hôte. Si votre ordinateur ne dispose que d’un port USB-A, connectez un câble USB-C vers USB-A (vendu séparément) au kit de développement et à l’ordinateur hôte.

1. Connectez-vous à votre kit de développement via SSH. Si vous avez besoin d’aide pour vous connecter par protocole SSH, [suivez ces instructions](./how-to-ssh-into-percept-dk.md).

1. Dans le terminal SSH, entrez les commandes suivantes :

    1. Mettez l’appareil en mode de mise à jour USB :

        ```bash
        sudo flagutil    -wBfRequestUsbFlash    -v1
        ```

    1. Redémarrez l’appareil. L’installation de la mise à jour va commencer.

        ```bash
        sudo reboot -f
        ```

### <a name="using-the-dip-switch-method"></a>Utilisation de la méthode des commutateurs DIP
Utilisez la méthode des commutateurs DIP lorsque vous ne pouvez pas utiliser une connexion SSH pour vous connecter à l’appareil.

1. Débranchez la carte de développement si elle est connectée au câble d’alimentation.
1. Retirez les quatre vis situées à l’arrière de la carte de développement à l’aide de la clé hexagonale fournie avec le kit de développement.

    :::image type="content" source="media/how-to-usb-update/dip-switch-01.jpg" alt-text="Retirez les quatre vis situées à l’arrière de la carte de développement":::

1. Faites coulisser délicatement la carte de développement en direction des LED. Le dissipateur thermique reste attaché au-dessus de la carte de développement. Ne faites coulisser la carte de développement que de 2 à 3 centimètres pour éviter de déconnecter des câbles.

    :::image type="content" source="media/how-to-usb-update/dip-switch-02.jpg" alt-text="Faites coulisser la carte de quelques centimètres":::

1. Les interrupteurs DIP se trouvent sur le coin de la carte. Quatre commutateurs sont disponibles, dont chacun offre deux positions : vers le haut (1) ou vers le bas (0). Les positions par défaut des commutateurs sont haut-bas-bas-haut (1001). À l’aide d’un trombone ou d’un autre instrument à pointe fine, modifiez les positions des commutateurs sur bas-haut-bas-haut (0101).

    :::image type="content" source="media/how-to-usb-update/dip-switch-03.jpg" alt-text="Trouvez les commutateurs sur le coin inférieur de la carte":::

1. Le kit de développement est maintenant en mode USB et vous pouvez passer aux étapes suivantes. **Une fois la mise à jour terminée, remettez les commutateurs DIP dans leur position par défaut haut-bas-bas-haut (1001).** Ensuite, faites coulisser la carte de développement pour la remettre dans sa position d’origine, puis refixez les quatre vis à l’arrière.

## <a name="connect-the-dev-kit-to-the-host-computer-via-a-usb-c-cable"></a>Connecter le kit de développement à l’ordinateur hôte via un câble USB-C
Cette procédure utilise l’unique port USB-C du kit de développement pour la mise à jour.  Si votre ordinateur dispose d’un port USB-C, vous pouvez utiliser le câble USB-C vers USB-C fourni avec le kit de développement.  Si votre ordinateur ne dispose que d’un port USB-A, connectez un câble USB-C vers USB-A (vendu séparément) au kit de développement et à l’ordinateur hôte.

1. Connectez le kit de développement à l’ordinateur hôte via le câble USB-C approprié.
1. L’ordinateur hôte doit maintenant détecter le kit de développement en tant que périphérique USB. Si vous avez exécuté avec succès la commande qui transfère le package de mise à jour vers le kit de développement, et si l’invite de commandes indique que le système attend l’affichage d’un périphérique USB connu**, la mise à jour devrait démarrer automatiquement après environ 10 secondes.

## <a name="wait-for-the-update-to-complete"></a>Attendre la fin de la mise à jour

1. Revenez à l’autre invite de commandes ou terminal. Une fois la mise à jour terminée, un message contenant ```Success 1    Failure 0``` s’affiche :

    > [!NOTE]
    > Après la mise à jour, votre appareil sera réinitialisé aux paramètres usine et vous perdrez votre connexion Wi-Fi et votre session SSH.

1. Une fois la mise à jour terminée, mettez le kit de développement hors tension. Débranchez le câble USB du PC.
1. Si vous avez utilisé la méthode des commutateurs DIP pour mettre le kit de développement en mode USB, veillez à remettre les commutateurs DIP dans leurs positions par défaut. Ensuite, faites coulisser la carte de développement pour la remettre dans sa position d’origine, puis refixez les quatre vis à l’arrière.   

## <a name="next-steps"></a>Étapes suivantes

Exécutez l’[expérience de configuration d’Azure Percept DK](./quickstart-percept-dk-set-up.md) pour reconfigurer votre appareil.
