---
title: Script pour activer kdump dans SAP HANA (grandes instances) | Microsoft Docs
description: Découvrez comment activer le service kdump sur les grandes instances Azure HANA, Type I et Type II.
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/22/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 272ac309629c62ee9fc7d12236aac4b2c3106b8a
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112540921"
---
# <a name="kdump-for-sap-hana-on-azure-large-instances"></a>kdump pour SAP HANA sur Azure (grandes instances)

Dans cet article, nous allons activer le service kdump sur les grandes instances Azure HANA (HLI) **Type I et Type II**.

La configuration et l’activation de kdump est nécessaire pour résoudre les incidents système qui n’ont pas de cause évidente. Parfois, une panne de système ne peut pas être expliquée par un problème de matériel ou d’infrastructure. Dans ce cas, un système d’exploitation ou une application peut avoir provoqué le problème. kdump permettra à SUSE de déterminer la raison de l’incident système.

## <a name="supported-skus"></a>Références prises en charge

|  Type de grande instance Hana   |  Fournisseur du système d’exploitation   |  Version du package du système d’exploitation   |  SKU |
|-----------------------------|--------------|-----------------------|-------------|
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   Type I                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S72        |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S192       |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S192       |
|   Type I                    |  SuSE        |   SLES 12 SP4         |  S192       |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S192m      |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S192m      |
|   Type I                    |  SuSE        |   SLES 12 SP4         |  S192m      |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S144       |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S144       |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S144m      |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S144m      |
|   Type II                   |  SuSE        |   SLES 12 SP2         |  S384       |
|   Type II                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   Type II                   |  SuSE        |   SLES 12 SP4         |  S384       |
|   Type II                   |  SuSE        |   SLES 12 SP2         |  S384xm     |
|   Type II                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   Type II                   |  SuSE        |   SLES 12 SP4         |  S384xm     |
|   Type II                   |  SuSE        |   SLES 12 SP2         |  S576m      |
|   Type II                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   Type II                   |  SuSE        |   SLES 12 SP4         |  S576m      |

## <a name="prerequisites"></a>Prérequis

- Le service kdump utilise le répertoire `/var/crash` pour écrire les images mémoire. Assurez-vous que la partition correspondant à ce répertoire dispose d’un espace suffisant pour accueillir les images.

## <a name="setup-details"></a>Détails de la configuration

- Le script pour activer kdump se trouve dans [Azure sap-hana-tools sur GitHub](https://github.com/Azure/sap-hana-tools/blob/master/tools/enable-kdump.sh).

> [!NOTE]
> Ce script est créé sur la base de la configuration de notre laboratoire. Vous devrez contacter votre fournisseur de système d’exploitation pour tout réglage supplémentaire.
> Un numéro d’unité logique (LUN) distinct sera configuré pour les serveurs nouveaux et existants pour l’enregistrement des images. Un script s’occupera de la configuration du système de fichiers à partir du numéro d’unité logique.
> Microsoft n’est pas responsable de l’analyse du vidage sur incident. Vous devez ouvrir un ticket avec votre fournisseur de système d’exploitation pour l’analyser.

- Exécutez ce script sur votre grand instance HANA en utilisant la commande suivante :

    > [!NOTE]
    > Les privilèges sudo sont requis pour exécuter cette commande.

    ```bash
    sudo bash enable-kdump.sh
    ```

- Si la sortie de la commande indique que kdump est correctement activé, redémarrez le système pour appliquer les modifications.

- Si la sortie de la commande indique que l’opération a échoué, le service kdump n’est pas activé. Reportez-vous à la section suivante, [Problèmes de prise en charge](#support-issues).

## <a name="test-kdump"></a>Tester kdump

> [!NOTE]
>  L’opération suivante déclenche un plantage du noyau et un redémarrage du système.

- Déclenchez un plantage du noyau

    ```bash
    echo c > /proc/sysrq-trigger
    ```

- Une fois le système redémarré, recherchez les journaux de plantages du noyau dans le répertoire `/var/crash`.

- Si `/var/crash` possède un répertoire avec la date du jour, le service kdump est correctement activé.

## <a name="support-issues"></a>Problèmes de prise en charge

Si le script échoue avec une erreur ou que Kdump n’est pas activé, envoyez une demande de service à l’équipe du support technique Microsoft. Précisez les informations suivantes :

* ID d’abonnement HLI

* Nom du serveur

* Fournisseur du système d’exploitation

* Version du SE

* Version du noyau

Pour plus d’informations, consultez [Configuration du kdump](https://www.suse.com/support/kb/doc/?id=3374462).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les mises à niveau du système d’exploitation sur les grandes instances HANA.

> [!div class="nextstepaction"]
> [Mises à niveau des systèmes d'exploitation](os-upgrade-hana-large-instance.md)
