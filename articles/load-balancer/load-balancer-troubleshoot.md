---
title: Résoudre les erreurs courantes d’Azure Load Balancer
description: Découvrez comment résoudre les problèmes courants liés à Azure Load Balancer.
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
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: cddaf1bde84d7e60eb59bd4c58c65fa889e06ae3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98028809"
---
# <a name="troubleshoot-azure-load-balancer"></a>Résoudre les problèmes liés à Azure Load Balancer

Cette page contient des informations pour résoudre les problèmes courants liés à Azure Load Balancer (versions De base et Standard). Pour plus d’informations sur Standard Load Balancer, consultez la [présentation de Standard Load Balancer](load-balancer-standard-diagnostics.md).

Lorsque la connectivité de l’équilibreur de charge n’est pas disponible, les symptômes les plus courants sont les suivants :

- Les machines virtuelles situées derrière Load Balancer ne répondent pas aux sondes d’intégrité 
- Les machines virtuelles situées derrière Load Balancer ne répondent pas au trafic sur le port configuré

Lorsque les clients externes aux machines virtuelles principales passent par l’équilibreur de charge, l’adresse IP des clients est utilisée pour la communication. Assurez-vous que l’adresse IP des clients est ajoutée à la liste d’autorisation des NSG.

## <a name="no-outbound-connectivity-from-standard-internal-load-balancers-ilb"></a>Aucune connectivité sortante à partir des équilibreurs de charge internes standards (ILB)

**Validation et résolution**

Les équilibrages standards sont **sécurisés par défaut**. Les équilibrages standards sont autorisés à se connecter à Internet via une adresse IP publique *masquée*. Cela n’est pas recommandé pour les charges de travail de production, car l’adresse IP n’est ni statique ni verrouillée via des NSG dont vous êtes propriétaire. Si vous êtes récemment passé d’un ILB De base à un ILB Standard, vous devez créer une IP publique explicitement via une configuration [Sortie uniquement](egress-only.md) qui verrouille l’adresse IP via des NSG. Vous pouvez également utiliser une passerelle [NAT Gateway](../virtual-network/nat-overview.md) sur votre sous-réseau.

## <a name="cant-change-backend-port-for-existing-lb-rule-of-a-load-balancer-that-has-virtual-machine-scale-set-deployed-in-the-backend-pool"></a>Impossible de modifier le port principal pour la règle d’équilibreur de charge existante d’un équilibreur de charge dont le groupe de machines virtuelles identiques est déployé dans le pool principal.

### <a name="cause-the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-virtual-machine-scale-set"></a>Cause : Le port principal ne peut pas être modifié lorsqu’une règle d’équilibreur de charge est utilisée par une sonde d’intégrité pour l’équilibreur de charge référencé par le groupe de machines virtuelles identiques.

**Résolution** : Pour modifier le port, vous pouvez supprimer la sonde d’intégrité en mettant à jour le groupe de machines virtuelles identiques, puis mettre à jour le port et reconfigurer la sonde d’intégrité.

## <a name="small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>Le petit trafic passe toujours par l’équilibreur de charge après la suppression des machines virtuelles du pool principal de l’équilibreur de charge.

### <a name="cause-vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>Cause : Les machines virtuelles supprimées du pool principal ne doivent plus recevoir le trafic. La faible quantité de trafic réseau peut être liée à un stockage, à un DNS et à d’autres fonctions dans Azure.

Pour vérifier, vous pouvez suivre une trace réseau. Le nom de domaine complet utilisé pour vos comptes de stockage blob est répertorié dans les propriétés de chaque compte de stockage.  À partir d’une machine virtuelle au sein de votre abonnement Azure, vous pouvez exécuter une commande nslookup pour déterminer l’adresse IP Azure attribuée à ce compte de stockage.

## <a name="additional-network-captures"></a>Captures de réseau supplémentaires

Si vous décidez d’ouvrir un dossier de support, collectez les informations suivantes pour accélérer la résolution du problème. Choisissez une machine virtuelle principale unique pour effectuer les tests suivants :

- Utilisez PsPing à partir d’une des machines virtuelles principales du réseau virtuel pour tester la réponse du port de la sonde (par exemple : PsPing 10.0.0.4:3389) et enregistrez les résultats. 
- Si aucune réponse n’est reçue dans ces tests Ping, exécutez une trace Netsh simultanée sur la machine virtuelle principale et la machine virtuelle de test du réseau virtuel tout en exécutant PsPing, puis arrêtez la trace Netsh.

## <a name="load-balancer-in-failed-state"></a>Équilibreur de charge en état d’échec

**Résolution :**

- Une fois que vous avez identifié la ressource en état d’échec, accédez à [Azure Resource Explorer](https://resources.azure.com/) et identifiez la ressource dans cet état.
- Mettez à jour le bouton bascule sur le coin supérieur droit sur Lecture/écriture.
- Cliquez sur Modifier pour la ressource en état d’échec.
- Cliquez sur PUT, puis sur GET pour vous assurer que l’état d’approvisionnement est maintenant « Succès ».
- Vous pouvez ensuite entreprendre d’autres actions lorsque la ressource n’est plus en état d’échec.

## <a name="next-steps"></a>Étapes suivantes

Si les étapes précédentes ne vous permettent pas de résoudre le problème, ouvrez un [ticket d’incident](https://azure.microsoft.com/support/options/).
