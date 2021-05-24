---
title: Mettre à jour Azure Percept DK via une connexion par câble USB-C
description: Découvrez comment mettre à jour Azure Percept DK via une connexion par câble USB-C
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 39889455cd41883f5782f9fb140b400d6ed09ed7
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109786732"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-c-cable-connection"></a>Comment mettre à jour Azure Percept DK via une connexion par câble USB-C

Ce guide explique comment mettre à jour correctement le système d’exploitation et le microprogramme de votre kit de développement via une connexion USB. Voici une vue d’ensemble de ce que vous allez effectuer pendant cette procédure.
1. Télécharger le package de mise à jour sur un ordinateur hôte
1. Exécuter la commande qui transfère le package de mise à jour vers le kit de développement
1. Configurer le kit de développement en « mode USB » (à l’aide de SSH) afin qu’il puisse être détecté par l’ordinateur hôte et recevoir le package de mise à jour
1. Connecter le kit de développement à l’ordinateur hôte via un câble USB-C
1. Attendre la fin de la mise à jour

> [!WARNING]
> La mise à jour de votre kit de développement via une connexion USB aura pour effet de supprimer toutes les données existantes sur l’appareil, y compris les modèles et conteneurs d’IA.
>
> Suivez toutes les instructions dans l’ordre. Sauter des étapes pourrait avoir pour effet de mettre votre kit de développement dans un état inutilisable.


## <a name="prerequisites"></a>Prérequis

- Un Azure Percept DK.
- Un ordinateur hôte Windows, Linux ou OS X avec la fonctionnalité Wi-Fi et un port USB-C ou USB-A disponible.
- Un câble de conversion USB-C/USB-A (facultatif, vendu séparément).
- Une connexion SSH, créée dans le cadre de la [configuration d’Azure Percept DK](./quickstart-percept-dk-set-up.md).

## <a name="download-software-tools-and-update-files"></a>Télécharger les outils logiciels et les fichiers de mise à jour

1. [Outil NXP UUU](https://github.com/NXPmicro/mfgtools/releases). Téléchargez la **version la plus récente** du fichier uuu.exe (pour Windows) ou du fichier uuu (pour Linux) sous l’onglet **Ressources**. UUU est un outil créé par NXP et utilisé pour mettre à jour les cartes de développement NXP.

1. [Téléchargez les fichiers de mise à jour](https://go.microsoft.com/fwlink/?linkid=2155734). Ils sont tous contenus dans un fichier zip que vous extrairez dans la section suivante.

1. Vérifiez que les trois artefacts de la build sont présents :
    - Azure-Percept-DK- *&lt;numéro de version&gt;* .raw
    - fast-hab-fw.raw
    - emmc_full.txt

## <a name="set-up-your-environment"></a>Configurer votre environnement

1. Créez un dossier/répertoire sur l’ordinateur hôte à un emplacement aisément accessibles via une ligne de commande.

1. Copiez l’outil UUU (**uuu.exe** ou **uuu**) dans le nouveau dossier.

1. Extrayez les fichiers de mise à jour précédemment téléchargés dans le nouveau dossier contenant l'outil UUU.

## <a name="update-your-device"></a>Mise à jour de votre appareil

Cette procédure utilise l’unique port USB-C du kit de développement pour la mise à jour.  Si votre ordinateur dispose d’un port USB-C, vous pouvez déconnecter l’appareil Azure Percept Vision et utiliser ce câble.  Si votre ordinateur ne dispose que d’un port USB-A, déconnectez l’appareil Azure Percept Vision du port USB-C du kit de développement et connectez un câble USB-C vers USB-A (vendu séparément) au kit de développement et à l’ordinateur hôte.

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

1. La fenêtre d’invite de commandes affiche un message indiquant « **En attente de l’apparition d’un périphérique USB connu…**  ». L’outil UUU attend maintenant que le kit de développement soit détecté par l’ordinateur hôte. Vous pouvez maintenant passer aux étapes suivantes.

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

1. Revenez à l’autre invite de commandes ou terminal. Une fois la mise à jour terminée, un message contenant ```Success 1    Failure 0``` s’affiche :

    > [!NOTE]
    > Après la mise à jour, votre appareil sera réinitialisé aux paramètres usine et vous perdrez votre connexion Wi-Fi et votre session SSH.

1. Une fois la mise à jour terminée, mettez le kit de développement hors tension. Débranchez le câble USB du PC.  

## <a name="next-steps"></a>Étapes suivantes

Exécutez l’[expérience de configuration d’Azure Percept DK](./quickstart-percept-dk-set-up.md) pour reconfigurer votre appareil.
