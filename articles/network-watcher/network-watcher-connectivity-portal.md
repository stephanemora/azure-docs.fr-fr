---
title: Résoudre les problèmes liés aux connexions – Portail Azure
titleSuffix: Azure Network Watcher
description: Découvrez comment utiliser la fonctionnalité de résolution des problèmes associés aux connexions d’Azure Network Watcher à l’aide du portail Azure.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: f33c5f0fdf69737df0d8bd83499ded1e0e0f4f88
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97898111"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Résoudre les problèmes associés aux connexions avec Azure Network Watcher à l’aide du portail Azure.

> [!div class="op_single_selector"]
> - [Portail](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [Azure CLI](network-watcher-connectivity-cli.md)
> - [API REST Azure](network-watcher-connectivity-rest.md)

Découvrez comment utiliser la résolution des problèmes associés aux connexions pour vérifier si une connexion TCP directe entre une machine virtuelle et un point de terminaison donné peut être établie.

## <a name="before-you-begin"></a>Avant de commencer

Cet article part du principe que vous disposez des ressources suivantes :

* Une instance de Network Watcher dans la région où vous souhaitez résoudre les problèmes associés à une connexion.
* Les machines virtuelles avec lesquelles résoudre les problèmes associés aux connexions.

> [!IMPORTANT]
> Pour résoudre les problèmes de connexion, la machine virtuelle à partir de laquelle vous procédez doit disposer de l’extension de machine virtuelle `AzureNetworkWatcherExtension` installée. Pour installer l’extension sur une machine virtuelle Windows, consultez la page [Azure Network Watcher Agent virtual machine extension for Windows](../virtual-machines/extensions/network-watcher-windows.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) (Extension de machine virtuelle d’agent Azure Network Watcher pour Windows). Pour une machine virtuelle Linux, consultez la page [Azure Network Watcher Agent virtual machine extension for Linux](../virtual-machines/extensions/network-watcher-linux.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) (Extension de machine virtuelle d’agent Azure Network Watcher pour Linux). L’extension n’est pas nécessaire sur le point de terminaison de destination.

## <a name="check-connectivity-to-a-virtual-machine"></a>Vérifier la connectivité à une machine virtuelle

Cet exemple vérifie la connectivité à une machine virtuelle de destination sur le port 80.

Accédez à Network Watcher et cliquez sur **Résolution des problèmes de connexion**. Sélectionnez la machine virtuelle dont vous souhaitez vérifier la connectivité. Dans la section **Destination**, choisissez **Sélectionner une machine virtuelle** puis sélectionnez la machine virtuelle appropriée et le port à tester.

Une fois que vous cliquez sur **Vérifier**, la connectivité entre les machines virtuelles sur le port spécifié est vérifiée. Dans l’exemple, la machine virtuelle de destination est inaccessible et une liste de tronçons apparaît.

![Consulter les résultats de la connectivité d’une machine virtuelle][1]

## <a name="check-remote-endpoint-connectivity"></a>Vérifier la connectivité du point de terminaison distant

Pour vérifier la connectivité et la latence vers un point de terminaison distant, sélectionnez la case d’option **Spécifier manuellement** dans la section **Destination**, entrez l’URL et le port puis cliquez sur **Vérifier**.  Cette opération est utilisée pour les points de terminaison distants comme des sites web et des points de terminaison de stockage.

![Consulter les résultats de la connectivité d’un site web][2]

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment automatiser les captures de paquets avec des alertes de machine virtuelle en consultant [Create an alert triggered packet capture (Créer une capture de paquets déclenchée par alerte)](network-watcher-alert-triggered-packet-capture.md)

Recherchez si certains types de trafic sont autorisés au sein ou en dehors de votre machine virtuelle en consultant [Check IP flow verify (Vérifier les flux IP)](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png