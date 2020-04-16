---
title: La machine virtuelle ne répond pas lors de l’application de la stratégie « Utilisateurs locaux et groupes de la stratégie de groupe »
description: Cet article décrit les étapes à suivre pour résoudre les problèmes de blocage de l’écran de chargement lors de l’application d’une stratégie pendant le démarrage sur une machine virtuelle Azure.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ff113268-f5bf-4e6a-986e-63b9b0ceff20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 04/02/2020
ms.author: v-mibufo
ms.openlocfilehash: 085880122e9a80e976cfe59686748b58aeba1922
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80620809"
---
# <a name="virtual-machine-is-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>La machine virtuelle ne répond pas lors de l’application de la stratégie « Utilisateurs locaux et groupes de la stratégie de groupe »

Cet article décrit les étapes à suivre pour résoudre les problèmes de blocage de l’écran de chargement lors de l’application d’une stratégie pendant le démarrage sur une machine virtuelle Azure.

## <a name="symptom"></a>Symptôme

Quand vous utilisez [Diagnostics de démarrage](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) pour visualiser la capture d’écran de la machine virtuelle, vous constatez que l’écran est bloqué et affiche le message : *Application d’une stratégie Utilisateurs locaux et groupes de stratégie de groupe*.

![Texte de remplacement : Écran montrant le message Application d’une stratégie Utilisateurs locaux et groupes de stratégie de groupe (Windows Server 2012).](media/troubleshoot-vm-unresponsive-group-policy-local-users/1.png)

Windows Server 2012

![Texte de remplacement : Écran montrant le message Application d’une stratégie Utilisateurs locaux et groupes de stratégie de groupe (Windows Server 2012 R2).](media/troubleshoot-vm-unresponsive-group-policy-local-users/2.png)

Windows Server 2012 R2

## <a name="cause"></a>Cause

Ce blocage est dû à une erreur de code dans la bibliothèque de liens dynamiques du service de profil Windows (*profsvc.dll*).

> [!NOTE]
> Ce défaut affecte uniquement Windows Server 2012 et Windows Server 2012 R2.

### <a name="the-policy-in-question"></a>La stratégie en question

La stratégie appliquée ne terminant pas ses processus est la suivante :

* *Configuration ordinateur\Stratégies\Modèles d’administration\Système/Profils utilisateur\Supprimer les profils utilisateur datant de plus d'un certain nombre de jours lors du redémarrage du système*

Cette stratégie ne se bloque que si les six conditions suivantes sont vraies :

* La stratégie *Supprimer les profils utilisateur datant de plus d'un certain nombre de jours lors du redémarrage du système* est activée.
* Vous avez des profils qui répondent aux exigences d’ancienneté pour exiger un nettoyage.
* Vous avez des composants qui ont été inscrits pour une notification de suppression des profils.
* Les composants effectuent des appels (directs ou indirects) qui doivent obtenir des données à partir des composants du Gestionnaire de contrôle des services (GCL) Windows, notamment les informations de démarrage, d’arrêt ou de requête concernant un service.
* Vous disposez d’un service configuré pour démarrer en mode *automatique*.
* Ce service est configuré pour s’exécuter dans le contexte d’un compte de domaine (par opposition à l’utilisation d’un compte intégré, tel qu’un système local).

### <a name="the-code-defect"></a>L’erreur de code

L’erreur de code est due au Gestionnaire de contrôle des services (GCL) et aux services de profil qui tentent d’appliquer des verrous l’un à l’autre, simultanément. Les verrous sont nécessaires pour empêcher plusieurs services de modifier simultanément les mêmes données, ce qui peut entraîner leur corruption. En règle générale, plusieurs requêtes de verrous n’entraînent aucun problème. Toutefois, dans la mesure où cela se produit pendant le démarrage, aucun service ne peut terminer ses processus car ils sont bloqués entre eux.

### <a name="os-bug-5880648---service-control-manager-deadlocks-with-the-delete-user-profiles-on-restart-policy"></a>Bogue du système d’exploitation 5880648 : blocage du Gestionnaire de contrôle des services avec la stratégie « Supprimer les profils utilisateur au redémarrage »

Deux actions se chevauchent afin que :

* L’action 1 acquiert le verrou de profil, mais n’a pas encore acquis le verrou GCL.

  **AND**

* L’action 2 acquiert le verrou GCL, mais n’a pas encore acquis le verrou de profil.

Une fois ce blocage effectué, la tentative d’acquisition du second verrou requis bloque l’action.

### <a name="action-1---old-profile-deletion-notification-has-profile-lock-needs-scm-lock"></a>Action 1 : notification de suppression de l’ancien profil (possède un **verrou de profil** et nécessite un **verrou GCL**)

1. Tout d’abord, la stratégie définie pour supprimer les anciens profils acquiert un verrou de service de profil interne.

   * Ce verrou empêche deux threads d’interagir avec les profils pendant l’*opération de suppression*.

2. La stratégie identifie des profils suffisamment anciens pour être supprimés.
3. Dans le cadre de la suppression du profil, un composant inscrit pour les notifications des suppressions d’un profil tente de **démarrer un service**.
4. Avant de démarrer le service, le Gestionnaire de contrôle des services (GCL) doit acquérir un **verrou GCL interne** conservé par les threads dans l’**action 2**.

### <a name="action-2---profile-loadcreation-for-user-specific-data-has-scm-lock-needs-profile-lock"></a>Action 2 : chargement/création d’un profil pour des données spécifiques à l’utilisateur (possède un **verrou GCL** et nécessite un **verrou de profil**)

1. Au démarrage, le GCL doit classer tous les services de *démarrage automatique* selon leur groupe, ainsi que tous les services dont ces services dépendent.

2. **Le GCL acquiert un verrou GCL interne** pour contrôler l’accès aux services de démarrage, d’arrêt ou de configuration lorsqu’il classe les services.

3. Une fois les services classés, le GCL parcourt et lance chaque service.

4. Si le service s’exécute dans le contexte d’un compte de domaine, un profil doit être chargé ou créé pour le compte de domaine afin de pouvoir stocker les données spécifiques à l’utilisateur.

5. Cette demande est envoyée au **service de profil**.

6. Le service de profil a besoin d’accéder au **verrou interne** acquis à l’**action 1**.

## <a name="solution"></a>Solution

### <a name="process-overview"></a>Vue d’ensemble du processus

1. Créer une machine virtuelle de réparation et y accéder
2. Activer la console série et la collecte de l’image mémoire
3. Regénérer la machine virtuelle
4. Collecter le fichier de l’image mémoire

   > [!NOTE]
   > Lorsque vous rencontrez cette erreur de démarrage, cela signifie que le système d’exploitation invité n’est pas opérationnel. Vous allez résoudre ce problème en mode hors connexion.

### <a name="create-and-access-a-repair-vm"></a>Créer une machine virtuelle de réparation et y accéder

1. Effectuez les [étapes 1 à 3 des commandes de réparation de machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) pour préparer une machine virtuelle de réparation.
2. Connectez-vous à la machine virtuelle de réparation à l’aide de la connexion Bureau à distance.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Activer la console série et la collecte de l’image mémoire

Pour activer la collecte de l’image mémoire et la console série, exécutez le script ci-dessous :

1. Ouvrez une session d’invite de commandes avec élévation de privilèges (Exécuter en tant qu’administrateur).
2. Exécutez les commandes suivantes :

   * Activer la console série :

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. Vérifiez que l’espace disponible sur le disque du système d’exploitation est égal à la taille de la mémoire (RAM) sur la machine virtuelle.

   * Si l’espace n’est pas suffisant, nous vous recommandons de changer l’emplacement où le fichier de l’image mémoire sera créé et de le référencer sur n’importe quel autre disque de données attaché à la machine virtuelle possédant suffisamment d’espace libre. Pour changer l’emplacement, remplacez `%SystemRoot%` par la lettre de lecteur (par exemple, « F: ») du disque de données dans les commandes ci-dessous.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Configuration suggérée pour activer l’image mémoire du système d’exploitation

**Charger le disque de système d’exploitation corrompu :**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Activer sur ControlSet001 :**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Activer sur ControlSet002 :**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

### <a name="rebuild-the-vm"></a>Regénérer la machine virtuelle

Suivez l’[étape 5 des commandes de réparation de machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) pour réassembler la machine virtuelle.

### <a name="collect-the-memory-dump-file"></a>Collecter le fichier de l’image mémoire

Pour résoudre ce problème, vous devez d’abord collecter le fichier de l’image mémoire du plantage et contacter le support technique muni de ce fichier d’image mémoire. Pour collecter le fichier d’image, effectuez les étapes suivantes :

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Attacher le disque du système d’exploitation à une machine virtuelle de récupération

1. Suivez les [étapes 1 à 3 des commandes de réparation de machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) pour préparer une nouvelle machine virtuelle de réparation.

2. Connectez-vous à la machine virtuelle de réparation à l’aide de la connexion Bureau à distance.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Rechercher le fichier de l’image mémoire et envoyer un ticket de support

1. Sur la machine virtuelle de réparation, accédez au dossier Windows sur le disque du système d’exploitation attaché. Si la lettre de lecteur qui est affectée au disque du système d’exploitation attaché est F, vous devez accéder à F:\Windows.

2. Recherchez le fichier memory.dmp, puis [envoyez un ticket de support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) avec le fichier de l’image mémoire.

3. Si vous rencontrez des difficultés pour localiser le fichier memory.dmp, vous pouvez utiliser à la place des [appels d’interruption non masquable (NMI) dans la console série](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls). Vous pouvez suivre le guide pour [générer un fichier en mode noyau ou de vidage sur incident complet](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump) à l’aide d’appels NMI.
