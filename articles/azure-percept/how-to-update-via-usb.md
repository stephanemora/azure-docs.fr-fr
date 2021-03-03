---
title: Mettre à jour votre DK Azure Percept via une connexion USB
description: Découvrez comment mettre à jour le DK Azure Percept via une connexion USB
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 7f5e5e4da9fea671fc85a55fc8cc191fa14b720f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101660259"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-connection"></a>Comment mettre à jour le DK Azure Percept via une connexion USB

Suivez ce guide pour découvrir comment effectuer une mise à jour via USB de la carte mère de votre DK Azure Percept.

## <a name="prerequisites"></a>Prérequis
- Ordinateur hôte avec un port USB-C ou USB-A disponible.
- Carte mère de DK (kit de développement) Azure Percept et câble USB-C vers USB-C fourni. Si votre ordinateur hôte est équipé d’un port USB-A, mais pas d’un port USB-C, vous pouvez utiliser un câble USB-C vers USB-A (vendu séparément).
- Installez [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) (accès administrateur requis).
- Installez l’outil NXP UUU. [Téléchargez la dernière version](https://github.com/NXPmicro/mfgtools/releases) du fichier uuu.exe (pour Windows) ou du fichier uuu (pour Linux) sous l’onglet Ressources.
- [Installez 7-Zip](https://www.7-zip.org/). Ce logiciel sera utilisé pour extraire le fichier image RAW de son fichier compressé XZ. Téléchargez et installez le fichier .exe approprié.

## <a name="steps"></a>Étapes
1.  Téléchargez les [trois fichiers de mise à jour USB](https://go.microsoft.com/fwlink/?linkid=2155734) suivants :
    - pe101-uefi-***&lt;numéro de version&gt;***.raw.xz
    - emmc_full.txt
    - fast-hab-fw.raw
 
1. Procédez à l’extraction vers pe101-uefi-* **&lt;numéro de version&gt;** _.raw à partir du fichier compressé pe101-uefi_* _&lt;numéro de version&gt;_ **.raw.xz. Vous ne savez pas comment procéder à l’extraction ? Téléchargez et installez 7-Zip, cliquez avec le bouton droit sur le fichier image **.xz**, puis sélectionnez 7-Zip &gt; Extraire ici.

1. Copiez les trois fichiers suivants dans le dossier qui contient l’outil UUU :
    - Fichier pe101-uefi-***&lt;numéro de version&gt;***.raw extrait (à l’étape 2).
    - emmc_full.txt (de l’étape 1).
    - fast-hab-fw.raw (de l’étape 1).
 
1. Mettez le kit de développement sous tension.
1. [Établir la connexion au kit de développement via SSH](./how-to-ssh-into-percept-dk.md)
1. Ouvrez une invite de commandes Windows (Démarrer &gt; cmd) ou un terminal Linux, puis accédez au dossier où sont stockés les fichiers de mise à jour. Exécutez la commande suivante pour lancer la mise à jour :
    - Windows : ```uuu -b emmc_full.txt fast-hab-fw.raw pe101-uefi-<version number>.raw```
    - Linux : ```sudo ./uuu -b emmc_full.txt fast-hab-fw.raw pe101-uefi-<version number>.raw```
    
Après l’exécution de ces commandes, vous pouvez voir un message indiquant « En attente de l’appareil... » dans l’invite de commandes. Ceci est normal et vous devez passer à l’étape suivante.
    
1. Connectez la carte mère de kit de développement à l’ordinateur hôte via un câble USB. Effectuez toujours la connexion depuis le port USB-C des cartes mères vers le port USB-C ou USB-A de l’ordinateur hôte (câble USB-C vers USB-A vendu séparément), en fonction des ports disponibles. 
 
1. Dans le terminal SSH/PuTTY, entrez les commandes suivantes pour définir le kit de développement en mode USB, puis pour le redémarrer.
    - ```flagutil    -wBfRequestUsbFlash    -v1```
    - ```reboot -f```
 
1. Vous pouvez recevoir un signal indiquant que l’ordinateur hôte reconnaît l’appareil et que le processus de mise à jour démarre automatiquement. Revenez à l’invite de commandes pour afficher l’état. Le processus prend jusqu’à dix minutes et, lorsque la mise à jour réussit, un message indiquant « Réussite 1 Échec 0 » s’affiche
 
1. Une fois la mise à jour terminée, mettez la carte mère hors tension. Débranchez le câble USB du PC.  Rebranchez le module Azure Percept Vision à la carte mère à l’aide du câble USB.

1. Remettez la carte mère sous tension.

## <a name="next-steps"></a>Étapes suivantes

Votre kit de développement est maintenant mis à jour. Vous pouvez continuer le développement et l’exploitation avec votre kit de développement.