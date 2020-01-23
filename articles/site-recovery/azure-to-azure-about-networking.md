---
title: Informations sur les réseaux dans la récupération d'urgence de machines virtuelles Azure avec Azure Site Recovery
description: Fournit une vue d’ensemble de la mise en réseau en vue d’une réplication des machines virtuelles Azure avec Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 1/8/2020
ms.author: sutalasi
ms.openlocfilehash: 9fe3b4c0b7acc9c1e980d5885043d30503c211c4
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754500"
---
# <a name="about-networking-in-azure-vm-disaster-recovery"></a>Informations sur les réseaux dans la récupération d'urgence de machines virtuelles Azure



Cet article fournit des instructions détaillées sur la mise en réseau quand vous répliquez et récupérez des machines virtuelles d’une région vers une autre avec [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Avant de commencer

Découvrez comment Site Recovery permet la récupération d’urgence pour [ce scénario](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Infrastructure réseau classique

Le diagramme suivant illustre un environnement Azure classique pour des applications qui s’exécutent sur des machines virtuelles Azure :

![environnement client](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Si vous utilisez Azure ExpressRoute ou une connexion VPN de votre réseau local vers Azure, l’environnement ressemble à ceci :

![environnement client](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Les réseaux sont généralement protégés à l’aide de pare-feu et de groupes de sécurité réseau. Les pare-feu se servent d’une liste verte basée sur l’adresse IP ou l’URL pour contrôler la connectivité réseau. Les groupes de sécurité réseau appliquent des règles qui utilise les plages d’adresses IP pour contrôler la connectivité réseau.

>[!IMPORTANT]
> L’utilisation d’un proxy authentifié pour contrôler la connectivité réseau n’est pas pris en charge par Site Recovery, et la réplication ne peut pas être activée.


## <a name="outbound-connectivity-for-urls"></a>Connectivité sortante pour les URL

Si vous utilisez un proxy de pare-feu basé sur des URL pour contrôler la connectivité sortante, autorisez ces URL Site Recovery :


**URL** | **Détails**  
--- | ---
*.blob.core.windows.net | Nécessaire pour que les données puissent être écrites dans le compte de stockage de cache dans la région source à partir de la machine virtuelle. Si vous connaissez tous les comptes de stockage de cache de vos machines virtuelles, vous pouvez autoriser l’accès aux URL de compte de stockage spécifiques (par exemple, cache1.blob.core.windows.net et cache2.blob.core.windows.net) au lieu de *.blob.core.windows.net.
login.microsoftonline.com | Nécessaire pour l’autorisation et l’authentification aux URL du service Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Nécessaire pour que la communication du service Site Recovery puisse avoir lieu à partir de la machine virtuelle. Vous pouvez utiliser l’« Adresse IP de Site Recovery » correspondante si votre proxy de pare-feu prend en charge les adresses IP.
*.servicebus.windows.net | Nécessaire pour que les données de surveillance et de diagnostic Site Recovery puissent être écrites à partir de la machine virtuelle. Vous pouvez utiliser l’« Adresse IP de supervision de Site Recovery » correspondante si votre proxy de pare-feu prend en charge les adresses IP.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Connectivité sortante pour les plages d’adresses IP

Si vous utilisez un proxy de pare-feu basé sur une adresse IP ou un groupe de sécurité réseau pour contrôler la connectivité sortante, ces plages d’adresses IP doivent être autorisées.

- Toutes les plages d’adresses IP qui correspondent aux comptes de stockage dans la région source
    - Créez une règle de groupe de sécurité réseau basée sur une [balise de service de stockage](../virtual-network/security-overview.md#service-tags) pour la région source.
    - Autorisez ces adresses pour que les données puissent être écrites dans le compte de stockage de cache, à partir de la machine virtuelle.
- Créer une règle de groupe de sécurité réseau basée sur une [balise de service Azure Active Directory (AAD)](../virtual-network/security-overview.md#service-tags) pour autoriser l’accès à toutes les adresses IP correspondant à AAD
    - Si de nouvelles adresses sont ajoutées ultérieurement à Azure Active Directory (AAD), vous devez créer de nouvelles règles de groupe de sécurité réseau.
- Créez une règle de groupe de sécurité réseau basée sur une balise du service EventsHub pour la région cible, en autorisant l’accès à la supervision de Site Recovery.
- Créez une règle de groupe de sécurité réseau basée sur une balise du service AzureSiteRecovery pour autoriser l’accès au service Site Recovery dans n’importe quelle région.
- Nous vous recommandons de créer les règles de groupe de sécurité réseau requises sur un groupe de sécurité réseau de test, et de vérifier qu’il n’y a aucun problème avant de créer les règles sur un groupe de sécurité réseau de production.


Si vous préférez utiliser des plages d’adresses IP Site Recovery (non recommandé), reportez-vous au tableau ci-dessous :

   **Cible** | **IP Site Recovery** |  **Adresse IP de surveillance Site Recovery**
   --- | --- | ---
   Asie Est | 52.175.17.132 | 13.94.47.61
   Asie Sud-Est | 52.187.58.193 | 13.76.179.223
   Inde centrale | 52.172.187.37 | 104.211.98.185
   Inde Sud | 52.172.46.220 | 104.211.224.190
   Centre-Nord des États-Unis | 23.96.195.247 | 168.62.249.226
   Europe Nord | 40.69.212.238 | 52.169.18.8
   Europe Ouest | 52.166.13.64 | 40.68.93.145
   USA Est | 13.82.88.226 | 104.45.147.24
   USA Ouest | 40.83.179.48 | 104.40.26.199
   États-Unis - partie centrale méridionale | 13.84.148.14 | 104.210.146.250
   USA Centre | 40.69.144.231 | 52.165.34.144
   USA Est 2 | 52.184.158.163 | 40.79.44.59
   Japon Est | 52.185.150.140 | 138.91.1.105
   OuJapon Est | 52.175.146.69 | 138.91.17.38
   Brésil Sud | 191.234.185.172 | 23.97.97.36
   Australie Est | 104.210.113.114 | 191.239.64.144
   Sud-Australie Est | 13.70.159.158 | 191.239.160.45
   Centre du Canada | 52.228.36.192 | 40.85.226.62
   Est du Canada | 52.229.125.98 | 40.86.225.142
   Centre-USA Ouest | 52.161.20.168 | 13.78.149.209
   USA Ouest 2 | 52.183.45.166 | 13.66.228.204
   Ouest du Royaume-Uni | 51.141.3.203 | 51.141.14.113
   Sud du Royaume-Uni | 51.140.43.158 | 51.140.189.52
   Sud du Royaume-Uni 2 | 13.87.37.4| 13.87.34.139
   Nord du Royaume-Uni | 51.142.209.167 | 13.87.102.68
   Centre de la Corée | 52.231.28.253 | 52.231.32.85
   Corée du Sud | 52.231.198.185 | 52.231.200.144
   France Centre | 52.143.138.106 | 52.143.136.55
   France Sud | 52.136.139.227 |52.136.136.62
   Australie Centre| 20.36.34.70 | 20.36.46.142
   Centre de l’Australie 2| 20.36.69.62 | 20.36.74.130
   Afrique du Sud Ouest | 102.133.72.51 | 102.133.26.128
   Afrique du Sud Nord | 102.133.160.44 | 102.133.154.128
   Gouvernement américain - Virginie | 52.227.178.114 | 23.97.0.197
   US Gov Iowa | 13.72.184.23 | 23.97.16.186
   Gouvernement des États-Unis – Arizona | 52.244.205.45 | 52.244.48.85
   Gouvernement des États-Unis – Texas | 52.238.119.218 | 52.238.116.60
   Est des États-Unis – US DoD | 52.181.164.103 | 52.181.162.129
   Centre des États-Unis – US DoD | 52.182.95.237 | 52.182.90.133
   Chine du Nord | 40.125.202.254 | 42.159.4.151
   Chine Nord 2 | 40.73.35.193 | 40.73.33.230
   Chine orientale | 42.159.205.45 | 42.159.132.40
   Chine orientale 2 | 40.73.118.52| 40.73.100.125
   Allemagne Nord| 51.116.208.58| 51.116.58.128
   Allemagne Centre-Ouest | 51.116.156.176 | 51.116.154.192
   Suisse Ouest | 51.107.231.223| 51.107.154.128
   Suisse Nord | 51.107.68.31| 51.107.58.128
   Norvège Est | 51.120.100.64| 51.120.98.128
   Norvège Ouest | 51.120.220.65| 51.120.218.160

## <a name="example-nsg-configuration"></a>Exemple de configuration de groupe de sécurité réseau

Cet exemple montre comment configurer des règles de groupes de sécurité réseau pour une machine virtuelle à répliquer.

- Si vous utilisez des règles de groupe de sécurité réseau pour contrôler la connectivité sortante, utilisez des règles « Autoriser le trafic HTTPS sortant » sur port:443 pour toutes les plages d’adresses IP requises.
- L’exemple part du principe que « USA Est » est l’emplacement source de la machine virtuelle, et que « USA Centre » en est l’emplacement cible.

### <a name="nsg-rules---east-us"></a>Règles de groupe de sécurité réseau - USA Est

1. Créez une règle de sécurité HTTPS sortante (443) pour « Storage.EastUS » sur le groupe de sécurité réseau comme indiqué dans la capture d’écran ci-dessous.

      ![storage-tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. Créez une règle de sécurité HTTPS sortante (443) pour « AzureActiveDirectory » sur le groupe de sécurité réseau comme indiqué dans la capture d’écran ci-dessous.

      ![aad-tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. Comme pour les règles de sécurité ci-dessus, créez une règle de sécurité HTTPS sortante (443) pour « EventHub.CentralUS » sur le groupe de sécurité réseau qui correspond à la position cible. Celle-ci permet d’accéder à la supervision de Site Recovery.

4. Créez une règle de sécurité HTTPS sortante (443) pour « AzureSiteRecovery » sur le groupe de sécurité réseau. Celle-ci permet d’accéder au service Site Recovery dans n’importe quelle région.

### <a name="nsg-rules---central-us"></a>Règles de groupe de sécurité réseau - USA Centre

Ces règles sont nécessaires pour que la réplication puisse être activée de la région cible vers la région source après le basculement :

1. Créez une règle de sécurité HTTPS sortante (443) pour « Storage.CentralUS » sur le groupe de sécurité réseau.

2. Créez une règle de sécurité HTTPS sortante (443) pour « AzureActiveDirectory » sur le groupe de sécurité réseau.

3. Comme pour les règles de sécurité ci-dessus, créez une règle de sécurité HTTPS sortante (443) pour « EventHub.Eastus » sur le groupe de sécurité réseau qui correspond à la position source. Celle-ci permet d’accéder à la supervision de Site Recovery.

4. Créez une règle de sécurité HTTPS sortante (443) pour « AzureSiteRecovery » sur le groupe de sécurité réseau. Celle-ci permet d’accéder au service Site Recovery dans n’importe quelle région.

## <a name="network-virtual-appliance-configuration"></a>Configuration des appliances virtuelles réseau

Si vous utilisez des appliances virtuelles réseau pour contrôler le trafic réseau sortant des machines virtuelles, l’appliance risque de présenter des limitations si tout le trafic de réplication passe par elle. Nous vous recommandons de créer un point de terminaison de service réseau dans votre réseau virtuel pour « Stockage » afin que le trafic de réplication n’atteigne pas l’appliance virtuelle réseau.

### <a name="create-network-service-endpoint-for-storage"></a>Créer un point de terminaison de service réseau pour le stockage
Vous pouvez créer un point de terminaison de service réseau dans votre réseau virtuel pour « Stockage » afin que le trafic de réplication ne quitte pas la limite Azure.

- Sélectionnez votre réseau virtuel Azure et cliquez sur Points de terminaison de service

    ![storage-endpoint](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Cliquez sur Ajouter et l’onglet Ajouter des points de terminaison de service s’ouvre
- Sélectionnez Microsoft.Storage sous Service et les sous-réseaux requis sous le champ Sous-réseaux, puis cliquez sur Ajouter

>[!NOTE]
>Ne limitez pas l’accès au réseau virtuel aux comptes de stockage utilisés pour ASR. Vous devez autoriser l’accès de Tous les réseaux

### <a name="forced-tunneling"></a>Tunneling forcé

Vous pouvez remplacer l’itinéraire système par défaut d’Azure pour le préfixe d’adresse 0.0.0.0/0 par un [itinéraire personnalisé](../virtual-network/virtual-networks-udr-overview.md#custom-routes) et rediriger le trafic des machines virtuelles vers une appliance virtuelle réseau locale, mais cette configuration n’est pas recommandée pour la réplication Site Recovery. Si vous utilisez des itinéraires personnalisés, vous devez [créer un point de terminaison de service de réseau virtuel](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) dans votre réseau virtuel pour « Stockage » afin que le trafic de réplication ne quitte pas la limite Azure.

## <a name="next-steps"></a>Étapes suivantes
- Commencer à protéger vos charges de travail en [répliquant des machines virtuelles Azure](site-recovery-azure-to-azure.md).
- En savoir plus sur la [conservation des adresses IP](site-recovery-retain-ip-azure-vm-failover.md) pour le basculement de machines virtuelles Azure.
- En savoir plus sur la récupération d’urgence des [machines virtuelles Azure via ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
