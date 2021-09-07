---
title: Virtualisation imbriquée pour Azure IoT Edge pour Linux sur Windows | Microsoft Docs
description: Découvrez comment naviguer dans la virtualisation imbriquée dans Azure IoT Edge pour Linux sur Windows.
author: fcabrera
manager: kgremban
ms.author: fcabrera
ms.date: 2/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: e583808b0be0ff4105abc438ace1b52b9d3317eb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562257"
---
# <a name="nested-virtualization-for-azure-iot-edge-for-linux-on-windows"></a>Virtualisation imbriquée pour Azure IoT Edge pour Linux sur Windows
Deux formes de virtualisation imbriquée sont compatibles avec Azure IoT Edge pour Linux sur Windows. Les utilisateurs peuvent choisir de déployer par le biais d’une machine virtuelle locale ou d’une machine virtuelle Azure. Cet article permet aux utilisateurs de déterminer l’option la mieux adaptée à leur scénario et fournit un aperçu des exigences de configuration.

> [!NOTE]
>
> Veillez à activer une [option de réseau](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization#networking-options) pour la virtualisation imbriquée. Si vous ne le faites pas, vous rencontrerez des erreurs d’installation d’EFLOW. 

## <a name="deployment-on-local-vm"></a>Déploiement sur une machine virtuelle locale

Il s’agit de l’approche de base pour toutes les machines virtuelles Windows hébergeant Azure IoT Edge pour Linux sur Windows. Dans ce cas, la virtualisation imbriquée doit être activée avant de commencer le déploiement. Pour plus d’informations sur la configuration de ce scénario, consultez [Exécuter Hyper-V sur une machine virtuelle avec virtualisation imbriquée](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization).

Si vous utilisez Windows Server, veillez à [installer le rôle Hyper-V](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

## <a name="deployment-on-azure-vms"></a>Déploiement sur des machines virtuelles Azure

Azure IoT Edge pour Linux sur Windows n’est pas compatible sur une machine virtuelle Azure exécutant la référence SKU de serveur sauf si un script est exécuté et affiche un commutateur par défaut. Pour plus d’informations sur la façon d’afficher un commutateur par défaut, consultez [Créer un commutateur virtuel pour Linux sur Windows](how-to-create-virtual-switch.md).

> [!NOTE]
>
> Toutes les machines virtuelles Azure supposées héberger EFLOW doivent être des machines virtuelles qui [prennent en charge la virtualisation imbriquée](../virtual-machines/acu.md)
