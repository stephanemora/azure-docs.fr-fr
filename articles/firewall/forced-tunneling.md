---
title: Tunneling forcé du pare-feu Azure
description: Vous pouvez configurer un tunneling forcé pour router le trafic Internet vers une appliance virtuelle réseau ou un pare-feu supplémentaire en vue d’un traitement ultérieur.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 4093f91e55272a32ce7df4a78e2ee8b3ebed5fde
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444834"
---
# <a name="azure-firewall-forced-tunneling-preview"></a>Tunneling forcé du pare-feu Azure (préversion)

Vous pouvez configurer le pare-feu Azure pour router tout le trafic Internet vers un tronçon suivant désigné au lieu d’accéder directement à Internet. Par exemple, vous pouvez disposer d’un pare-feu de périphérie local ou d’une autre appliance virtuelle réseau (NVA) pour traiter le trafic réseau avant qu’il ne soit dirigé vers Internet.

> [!IMPORTANT]
> Le tunneling forcé du pare-feu Azure est actuellement disponible en préversion publique.
>
> Cette préversion publique est fournie sans contrat de niveau de service et ne doit pas être utilisée pour les charges de travail de production. Il est possible que certaines fonctionnalités ne soient pas prises en charge, disposent de capacités limitées ou ne soient pas accessibles à tous les emplacements Azure. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Par défaut, le tunneling forcé n’est pas autorisé sur le pare-feu Azure afin de garantir que toutes ses dépendances Azure sortantes sont satisfaites. Les configurations de routes définies par l’utilisateur (UDR) sur le *AzureFirewallSubnet* ayant une route par défaut qui n’est pas directement dirigé vers Internet sont désactivées.

## <a name="forced-tunneling-configuration"></a>Configuration de tunneling forcé

Pour prendre en charge le tunneling forcé, le trafic de gestion des services est séparé du trafic client. Un sous-réseau dédié supplémentaire nommé *AzureFirewallManagementSubnet* ayant sa propre adresse IP publique associée est nécessaire. La seule route autorisée sur ce sous-réseau est une route par défaut vers Internet, et la propagation de route BGP doit être désactivée.

Si vous avez une route par défaut publiée via BGP pour forcer le trafic vers un emplacement local, vous devez créer *AzureFirewallSubnet* et *AzureFirewallManagementSubnet* avant de déployer votre pare-feu et avoir une route UDR ayant une route par défaut vers Internet. De plus, la propagation de la route de la passerelle de réseau virtuel doit être désactivée.

Dans cette configuration, *AzureFirewallSubnet* peut désormais inclure des routes vers n’importe quel pare-feu local ou n’importe quelle appliance virtuelle réseau pour traiter le trafic avant qu’il ne soit dirigé vers Internet. Vous pouvez également publier ces routes via le protocole BGP vers *AzureFirewallSubnet* si la propagation de la route de la passerelle de réseau virtuel est activée sur ce sous-réseau.

Une fois que vous avez configuré le pare-feu Azure pour prendre en charge le tunneling forcé, vous ne pouvez pas annuler la configuration. Si vous supprimez toutes les autres configurations IP de votre pare-feu, la configuration IP de gestion est également supprimée et le pare-feu est libéré. L’adresse IP publique attribuée à la configuration IP de gestion ne peut pas être supprimée, mais vous pouvez attribuer une autre adresse IP publique.

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Déployer et configurer un pare-feu Azure dans un réseau hybride à l’aide du portail Azure](tutorial-hybrid-portal.md)