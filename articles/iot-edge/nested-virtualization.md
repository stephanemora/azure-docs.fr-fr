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
ms.openlocfilehash: 7c50e813655603101da15931a6b3056d431f47b8
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108129528"
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
Si vous choisissez de déployer sur des machines virtuelles Azure, sachez qu’il n’existe pas dans la conception de commutateur externe. Azure IoT Edge pour Linux sur Windows n’est pas compatible non plus sur une machine virtuelle Azure exécutant la référence SKU de serveur, sauf si un script spécifique est exécuté et affiche un commutateur par défaut. Pour plus d’informations sur l’affichage d’un commutateur par défaut, consultez la section [Windows Server](#windows-server) ci-dessous. 

> [!NOTE]
>
> Toutes les machines virtuelles Azure supposées héberger EFLOW doivent être des machines virtuelles qui [prennent en charge la virtualisation imbriquée](../virtual-machines/acu.md)


## <a name="limited-use-of-external-switch"></a>Utilisation limitée d’un commutateur externe
Dans les scénarios où la machine virtuelle ne peut pas obtenir d’adresse IP via un commutateur externe, la fonctionnalité de déploiement utilise automatiquement le commutateur internet par défaut pour le déploiement. Cela signifie que la gestion d’Azure IoT Edge pour les machines virtuelles Linux ne peut être exécutée que sur l’appareil cible (à savoir que la connexion à Azure IoT Edge pour la machine virtuelle Linux via l’extension SSH PowerShell WAC n’est possible que sur localhost).

## <a name="windows-server"></a>Windows Server
Pour les utilisateurs de Windows Server, sachez qu’Azure IoT Edge pour Linux sur Windows ne prend pas en charge automatiquement le commutateur par défaut.

* Conséquences pour la machine virtuelle locale : assurez-vous que la machine virtuelle EFLOW peut obtenir une adresse IP via le commutateur externe.

* Conséquences pour la machine virtuelle Azure : étant donné qu’il n’existe pas de commutateur externe sur les machines virtuelles Azure, il n’est pas possible de déployer EFLOW avant de configurer un commutateur interne sur le serveur.

Il n’existe pas de commutateur par défaut sur les références SKU de serveur par défaut (que la référence SKU de serveur s’exécute ou non sur une machine virtuelle Azure). Lors du déploiement sur une machine virtuelle Azure où le commutateur externe ne peut pas être utilisé, le commutateur par défaut doit être configuré manuellement avant le déploiement d’Azure IoT Edge pour Linux sur Windows. Notre fonctionnalité de déploiement couvre cela car elle nécessite une configuration IP pour le commutateur interne, une configuration NAT, ainsi que l’installation et la configuration d’un serveur DHCP. Notre fonctionnalité de déploiement en préversion publique n’utilise pas ces paramètres pour ne compromettre les configurations réseau sur des déploiements en production.

* Vous trouverez des informations pertinentes sur la configuration manuelle du commutateur par défaut ici : [Guide d’activation de la virtualisation imbriquée dans les machines virtuelles Azure](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization)
* Vous trouverez la documentation sur la configuration d’un serveur DHCP pour ce scénario ici : [Déployer DHCP à l’aide de Windows PowerShell](/windows-server/networking/technologies/dhcp/dhcp-deploy-wps)