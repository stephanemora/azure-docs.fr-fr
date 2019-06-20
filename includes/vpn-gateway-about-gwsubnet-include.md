---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0334f9fd2d749b88580ff3857d705de2ae961902
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67176917"
---
La passerelle de réseau virtuel utilise un sous-réseau spécifique, appelé sous-réseau de passerelle. Le sous-réseau de passerelle fait partie de la plage d’adresses IP du réseau virtuel que vous spécifiez lors de la configuration de votre réseau virtuel. Il contient les adresses IP utilisées par les ressources et les services de passerelle de réseau virtuel. Le sous-réseau doit être nommé « GatewaySubnet » pour qu’Azure puisse déployer les ressources de passerelle. Vous ne pouvez pas spécifier un autre sous-réseau pour y déployer les ressources de passerelle. Si vous n’avez pas de sous-réseau nommé « GatewaySubnet », vous ne pourrez pas créer votre passerelle VPN.

Lorsque vous créez le sous-réseau de passerelle, vous spécifiez le nombre d’adresses IP que contient le sous-réseau. Le nombre d’adresses IP requises varie selon la configuration de la passerelle VPN que vous souhaitez créer. Certaines configurations nécessitent plus d’adresses IP que d’autres. Nous vous recommandons de créer un sous-réseau de passerelle dont la taille est définie sur /27 ou /28.

Si une erreur s’affiche indiquant que l’espace d’adressage chevauche un sous-réseau, ou que le sous-réseau n’est pas contenu dans l’espace d’adressage de votre réseau virtuel, vérifiez la plage d’adresses de votre réseau virtuel. Vous n’avez peut-être pas suffisamment d’adresses IP disponibles dans la plage d’adresses que vous avez créée pour votre réseau virtuel. Par exemple, si votre sous-réseau par défaut englobe la plage d’adresses complète, il n’y a plus d’adresses IP disponibles pour créer des sous-réseaux supplémentaires. Vous pouvez ajuster vos sous-réseaux au sein de l’espace d’adressage existant pour libérer des adresses IP, ou spécifier une plage d’adresses supplémentaire pour y créer le sous-réseau de passerelle.