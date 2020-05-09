---
title: Script pour activer Kdump dans SAP HANA (grandes instances) | Microsoft Docs
description: Script pour activer Kdump dans SAP HANA (grandes instances), type HLI I, type HLI II
services: virtual-machines-linux
documentationcenter: ''
author: prtyag
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/30/2020
ms.author: prtyag
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 16dc15b4369904643d0138a4b8e5b94c47868d31
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204778"
---
# <a name="enable-kdump-service"></a>Activer le service Kdump

Ce document décrit en détail comment activer le service Kdump sur une grande instance Azure HANA (**type I et type II**)

## <a name="supported-skus"></a>Références prises en charge

|  Type de grande instance Hana   |  Fournisseur du système d’exploitation   |  Version du package du système d’exploitation   |  SKU        |
|-----------------------------|--------------|-----------------------|-------------|
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   Type I                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   Type II                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   Type II                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   Type II                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   Type II                   |  SuSE        |   SLES 12 SP4         |  S384xm     |

## <a name="prerequisites"></a>Prérequis

- Comme le service Kdump utilise le répertoire `/var/crash` pour écrire des vidages, vérifiez que la partition qui correspond à ce répertoire a suffisamment d’espace pour accueillir les vidages.

## <a name="setup-details"></a>Détails de la configuration

- Le script pour activer Kdump est disponible [ici](https://github.com/Azure/sap-hana/blob/master/tools/enable-kdump.sh)

- Exécutez ce script sur une grande instance HANA à l’aide de la commande ci-dessous

    > [!NOTE]
    > Privilège sudo requis pour exécuter cette commande.

    ```bash
    sudo bash enable-kdump.sh
    ```

- Si la commande génère l’activation de Kdump, redémarrez le système pour appliquer la modification et pour que le service Kdump soit ainsi correctement activé. Redémarrez le système pour appliquer les modifications.

- Si la sortie de la commande est un message de type « Échec de l’exécution d’une opération donnée, sortie », le service Kdump n’est pas activé. Reportez-vous à la section [Problème de support](#support-issue).

## <a name="test-kdump"></a>Tester Kdump

> [!NOTE]
>  L’opération ci-dessous déclenche un plantage du noyau et un redémarrage du système.

- Déclenchez un plantage du noyau

    ```bash
    echo 1 > /proc/sys/kernel/sysrq
    echo c > /proc/sysrq-trigger
    ```

- Une fois le système redémarré, recherchez les journaux de plantages du noyau dans le répertoire `/var/crash`.

- Si le répertoire `/var/crash` possède un répertoire avec la date du jour, le service Kdump est correctement activé.

## <a name="support-issue"></a>Problème de support

Si le script échoue avec une erreur ou que Kdump n’est pas activé, envoyez une demande de service à l’équipe du support technique Microsoft avec les informations suivantes.

* ID d’abonnement HLI

* Nom du serveur

* Fournisseur du système d’exploitation

* Version du SE

* Version du noyau
