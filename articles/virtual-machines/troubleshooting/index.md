---
layout: LandingPage
description: Découvrez comment résoudre les problèmes liés aux déploiements de machines virtuelles.
title: Résolution des problèmes pour la documentation Machines virtuelles Azure | Microsoft Docs
services: virtual-machines
author: genlin
manager: dcscontentpm
ms.assetid: ''
ms.service: virtual-machines
ms.tgt_pltfrm: na
ms.topic: landing-page
ms.date: 10/3/2018
ms.author: genli
ms.openlocfilehash: bdb459a69557269a20d36f05acc2da502064efb9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851347"
---
# <a name="troubleshooting-azure-virtual-machines"></a>Résolution des problèmes liés aux machines virtuelles Azure

## <a name="tools-for-troubleshooting"></a>Outils de résolution des problèmes

- [Console série](serial-console-overview.md)
- [Diagnostics de démarrage](boot-diagnostics.md)
- [Machine virtuelle Windows : Attacher le disque du système d’exploitation à une autre machine virtuelle pour la résolution de problèmes](troubleshoot-recovery-disks-portal-windows.md)
- [Machine virtuelle Linux : Attacher le disque du système d’exploitation à une autre machine virtuelle pour la résolution de problèmes](troubleshoot-recovery-disks-portal-linux.md)

## <a name="cant-connect-to-the-vm"></a>Impossible de se connecter à la machine virtuelle

### <a name="windows"></a>Windows

**Solution courante**

- [Réinitialiser RDP](reset-rdp.md)
- [Dépannage pour RDP](troubleshoot-rdp-connection.md)
- [Dépannage détaillé pour RDP](detailed-troubleshoot-rdp.md)

**Erreurs RDP**

- [Absence de serveur de licences](troubleshoot-rdp-no-license-server.md)
- [Une erreur interne ](Troubleshoot-rdp-internal-error.md)
- [erreurs d’authentification](troubleshoot-authentication-error-rdp-vm.md)
- [Dépannage d’erreurs spécifiques](troubleshoot-specific-rdp-errors.md)

**Erreurs de démarrage de machine virtuelle**

* [Erreurs de démarrage BitLocker](troubleshoot-bitlocker-boot-error.md)
* [Windows indique « Vérification du système de fichiers » pendant le démarrage](troubleshoot-check-disk-boot-error.md)
* [Erreurs liées à un écran bleu](troubleshoot-common-blue-screen-error.md)
* [Le démarrage de la machine virtuelle est bloqué sur « Préparation de Windows](troubleshoot-vm-boot-configure-update.md)
* [Erreur « Échec du service critique » sur écran bleu](troubleshoot-critical-service-failed-boot-error.md)
* [Problème de boucle de redémarrage](troubleshoot-reboot-loop.md)
* [Le démarrage de la machine virtuelle est bloqué au niveau de la mise à jour de Windows](troubleshoot-stuck-updating-boot-error.md)
* [La machine virtuelle démarre en mode sans échec](troubleshoot-rdp-safe-mode.md)

**Autres**
- [Réinitialiser le mot de passe de la machine virtuelle Windows en mode hors connexion](reset-local-password-without-agent.md)
- [Réinitialiser la carte réseau après une configuration incorrecte](reset-network-interface.md)

### <a name="linux"></a>Linux

- [Résolution des problèmes SSH](troubleshoot-ssh-connection.md)
- [Résolution des problèmes SSH détaillée](detailed-troubleshoot-ssh-connection.md)
- [Messages d’erreur courants](error-messages.md)
- [Réinitialiser le mot de passe de la machine virtuelle Linux en mode hors connexion](reset-password.md)

## <a name="vm-deployment-issues"></a>Problèmes de déploiement de machine virtuelle

- [Échecs d’allocation](allocation-failure.md)
- Redéploiement d’une machine virtuelle
    - [Linux](redeploy-to-new-node-linux.md)
    - [Windows](redeploy-to-new-node-windows.md)
- Dépanner les déploiements
    - [Linux](troubleshoot-deploy-vm-linux.md)
    - [Windows](troubleshoot-deploy-vm-windows.md)
- [Les noms d’appareil changent](troubleshoot-device-names-problems.md)
- [Installer l’agent de machine virtuelle Windows en mode hors connexion](install-vm-agent-offline.md)
- [Redémarrage ou redimensionnement d’une machine virtuelle](restart-resize-error-troubleshooting.md)

## <a name="vm-performance-issue"></a>Problème de performances de machine virtuelle
- [Problèmes de performances avec les machines virtuelles](performance-diagnostics.md)
- Windows
    - [Guide pratique pour utiliser PerfInsights](how-to-use-perfinsights.md)
    - [Extension de diagnostics de performance](performance-diagnostics-vm-extension.md)
- Linux
    - [Guide pratique pour utiliser PerfInsights](how-to-use-perfinsights-linux.md)