---
title: Mettre à jour votre DK Azure Percept via une connexion USB
description: Découvrez comment mettre à jour le DK Azure Percept via une connexion USB
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: cd6e4e62123b4d4b927cf385aaf64a066eecc1e0
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104887748"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-connection"></a>Comment mettre à jour le DK Azure Percept via une connexion USB

Bien que l’utilisation de mises à jour par voie hertzienne (OTA) soit la meilleure méthode pour tenir à jour le système d’exploitation et le microprogramme de votre kit de développement, il existe des scénarios dans lesquels la mise à jour (ou le « flashage ») du kit de développement via une connexion USB s’impose :

- Une mise à jour OTA n’est pas possible en raison de problèmes de connectivité ou autres.
- L’appareil doit être réinitialisé à son état usine.

Ce guide explique comment mettre à jour correctement le système d’exploitation et le microprogramme de votre kit de développement via une connexion USB.

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

1. [Outil NXP UUU](https://github.com/NXPmicro/mfgtools/releases). Téléchargez la **dernière version** du fichier uuu.exe (pour Windows) ou du fichier uuu (pour Linux) à partir de l’onglet **Ressources**.

1. [7-Zip](https://www.7-zip.org/). Ce logiciel sera utilisé pour extraire le fichier image RAW de son fichier compressé XZ. Téléchargez et installez le fichier .exe approprié.

1. [Téléchargez les fichiers de mise à jour](https://go.microsoft.com/fwlink/?linkid=2155734).

1. Vérifiez que les trois artefacts de la build sont présents :
    - Azure-Percept-DK- *&lt;numéro de version&gt;* .raw.xz
    - fast-hab-fw.raw
    - emmc_full.txt

## <a name="set-up-your-environment"></a>Configurer votre environnement

1. Créez un dossier/répertoire sur l’ordinateur hôte à un emplacement aisément accessibles via une ligne de commande.

1. Copiez l’outil UUU (**uuu.exe** ou **uuu**) dans le nouveau dossier.

1. Extrayez le fichier **Azure-Percept-DK- *&lt;&gt;numéro de version*.raw** du fichier compressé en cliquant avec le bouton droit sur **Azure-Percept-DK- *&lt;numéro de version&gt;* .rax.zx**, puis en sélectionnant **7-Zip** &gt; **Extraire ici**.

1. Déplacez le fichier extrait **Azure-Percept-DK- *&lt;&gt;numéro de version*.raw**, **fast-hab-fw.raw** et **emmc_full.txt** vers le dossier contenant l’outil UUU.

## <a name="update-your-device"></a>Mise à jour de votre appareil

1. [Connectez-vous avec SSH à votre kit de développement](./how-to-ssh-into-percept-dk.md).

1. Ouvrez une invite de commandes Windows (**Démarrer** > **cmd**) ou un terminal Linux, puis accédez au dossier où sont stockés les fichiers de mise à jour et l’outil UUU. Entrez la commande suivante dans l’invite de commandes ou le terminal pour préparer votre ordinateur à recevoir un appareil pouvant être flashé :

    - Windows :

        ```console
        uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw 
        ```

    - Linux :

        ```bash
        sudo ./uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw
        ```

1. Déconnectez l’appareil Azure Percept Vision du port USB-C de la carte porteuse.

1. Connectez le câble USB-C fourni au port USB-C de la carte porteuse et au port USB-C de l’ordinateur hôte. Si votre ordinateur ne dispose que d’un port USB-A, connectez un câble de conversion USB-C vers USB-A (vendu séparément) à la carte porteuse et à l’ordinateur hôte.

1. Dans l’invite du client SSH, entrez les commandes suivantes :

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

1. Une fois la mise à jour terminée, mettez la carte mère hors tension. Débranchez le câble USB du PC.  

## <a name="next-steps"></a>Étapes suivantes

Exécutez l’[expérience de configuration d’Azure Percept DK](./quickstart-percept-dk-set-up.md) pour reconfigurer votre appareil.