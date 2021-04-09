---
title: Résolution des problèmes liés à l’état de la sonde d'intégrité Azure Load Balancer
description: Découvrez comment résoudre les problèmes connus liés à l’état de la sonde d'intégrité Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/02/2020
ms.author: allensu
ms.openlocfilehash: 28823c997cd974d5061829df88680ed52075caa0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98029137"
---
# <a name="troubleshoot-azure-load-balancer-health-probe-status"></a>Résolution des problèmes liés à l’état de la sonde d'intégrité Azure Load Balancer

Cette page contient des informations de résolution des problèmes liés aux questions courantes relatives à l’état de la sonde d'intégrité Azure Load Balancer.

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Symptôme : Les machines virtuelles situées derrière l’équilibreur de charge ne répondent pas aux sondes d’intégrité
Pour que les serveurs principaux participent au jeu d’équilibrage de charge, ils doivent réussir la vérification de la sonde. Pour plus d’informations sur les sondes d’intégrité, consultez la page [Comprendre les sondes de l’équilibrage de charge](load-balancer-custom-probe-overview.md). 

Les machines virtuelles du pool principal de l’équilibreur de charge peuvent ne pas répondre aux sondes en raison des raisons suivantes : 
- Une machine virtuelle du pool principal de l’équilibreur de charge est défectueuse 
- Une machine virtuelle du pool principal de l’équilibreur de charge n’écoute pas sur le port de la sonde 
- Un pare-feu ou un groupe de sécurité réseau bloque le port sur les machines virtuelles du pool principal de l’équilibreur de charge 
- Autres erreurs de configuration de l’équilibreur de charge

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Cause 1 : Une machine virtuelle du pool principal de l’équilibreur de charge est défectueuse

**Validation et résolution**

Pour résoudre ce problème, connectez-vous aux machines virtuelles participant et vérifiez si l’état de la machine virtuelle est sain et si elle peut répondre à la commande **PsPing** ou **TCPing** d’une autre machine virtuelle du pool. Si la machine virtuelle est défectueuse ou si elle ne peut pas répondre à la sonde, vous devez corriger le problème et rétablir la machine virtuelle à un état sain pour qu’elle puisse faire partie de l’équilibrage de charge.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Cause 2 : Une machine virtuelle du pool principal de l’équilibreur de charge n’écoute pas sur le port de la sonde
Si la machine virtuelle est saine, mais ne répond pas à la sonde, il se peut que le port de la sonde ne soit pas ouvert sur la machine virtuelle participant ou que la machine virtuelle n’écoute pas sur ce port.

**Validation et résolution**

1. Connectez-vous à la machine virtuelle principale.
2. Ouvrez une invite de commandes et exécutez la commande suivante pour vérifier qu’une application écoute sur le port de la sonde :          netstat -an
3. Si l’état du port indiqué n’est pas **ÉCOUTE**, configurez le port approprié. 
4. Vous pouvez également sélectionner un autre port, qui indique **ÉCOUTE**, et mettre à jour en conséquence la configuration de l’équilibreur de charge.

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Cause 3 : Un pare-feu ou un groupe de sécurité réseau bloque le port sur les machines virtuelles du pool principal de l’équilibreur de charge
Si le pare-feu sur la machine virtuelle bloque le port de la sonde, ou un ou plusieurs groupes de sécurité réseau configurés sur le sous-réseau ou sur la machine virtuelle, ou n’autorise pas la sonde à atteindre le port, la machine virtuelle est incapable de répondre à la sonde d’intégrité.

**Validation et résolution**

1. Si le pare-feu est activé, vérifiez s’il est configuré pour autoriser le port de la sonde. Si tel n’est pas le cas, configurez le pare-feu pour autoriser le trafic sur le port de la sonde, puis procédez à un nouveau test.
2. Dans la liste des groupes de sécurité réseau, vérifiez si le trafic entrant ou sortant sur le port de la sonde subit des interférences.
3. Vérifiez également si une règle **Refuser tout** des groupes de sécurité réseau sur la carte d’interface réseau de la machine virtuelle ou du sous-réseau a une priorité supérieure à celle de la règle par défaut qui autorise les sondes et le trafic de l’équilibreur de charge (les groupes de sécurité réseau doivent autoriser l’adresse IP 168.63.129.16 de l’équilibreur de charge).
4. Si l’une de ces règles bloque le trafic de la sonde, supprimez et reconfigurez les règles pour autoriser le trafic de la sonde.  
5. Vérifiez si la machine virtuelle répond à présent aux sondes d’intégrité.

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Cause 4 : Autres erreurs de configuration de l’équilibreur de charge
Si toutes les causes précédentes semblent vérifiées et résolues et si la machine virtuelle principale ne répond toujours pas à la sonde d’intégrité, testez manuellement la connectivité, puis collectez des traces pour comprendre la connectivité.

**Validation et résolution**

1. Utilisez **Psping** à partir d’une des autres machines virtuelles dans le réseau virtuel pour tester la réponse du port de la sonde (exemple : .\psping.exe -t 10.0.0.4:3389) et enregistrez les résultats. 
2. Utilisez **TCPing** à partir d’une des autres machines virtuelles dans le réseau virtuel pour tester la réponse du port de la sonde (exemple : .\tcping.exe 10.0.0.4 3389) et enregistrez les résultats. 
3. Si aucune réponse n’est reçue dans ces tests ping,
    - exécutez une trace Netsh simultanée sur la machine virtuelle du pool principal cible et une autre machine virtuelle de test à partir du même réseau virtuel. À présent, exécutez un test PsPing pendant un certain temps, collectez des traces réseau, puis arrêtez le test. 
    - Analysez la capture du réseau et vérifiez que des paquets entrants et sortants sont associés à la requête ping. 
        - Si aucun paquet entrant n’est observé sur la machine virtuelle du pool principal, il se peut qu’une erreur de configuration des groupes de sécurité réseau ou UDR bloque le trafic. 
        - Si aucun paquet sortant n’est observé sur la machine virtuelle du pool principal, celle-ci doit être vérifiée pour y rechercher les problèmes non liés (par exemple, une application bloquant le port de la sonde). 
    - Vérifiez si les paquets de la sonde sont contraints d’atteindre une autre destination (probablement via les paramètres UDR) avant l’équilibreur de charge. Ce faisant, le trafic peut ne jamais atteindre la machine virtuelle principale. 
4. Modifiez le type de sonde (par exemple, remplacez HTTP par TCP) et configurez le port correspondant dans les listes de contrôle d’accès (ACL) des groupes de sécurité réseau et le pare-feu pour vérifier si le problème est lié à la configuration de la réponse de la sonde. Pour plus d’informations sur la configuration de la sonde d’intégrité, consultez la page [Endpoint Load Balancing health probe configuration (Configuration d’une sonde d’intégrité pour l’équilibrage de charge des points de terminaison)](/archive/blogs/mast/endpoint-load-balancing-heath-probe-configuration-details).

## <a name="next-steps"></a>Étapes suivantes

Si les étapes précédentes ne vous permettent pas de résoudre le problème, ouvrez un [ticket d’incident](https://azure.microsoft.com/support/options/).