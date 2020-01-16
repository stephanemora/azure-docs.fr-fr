---
title: Erreur InvalidNetworkConfigurationErrorCode – Azure HDInsight
description: Diverses raisons pour les échecs de création de cluster avec InvalidNetworkConfigurationErrorCode dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: f857ee47f5dd8018d2e26aab47252533b0b17617
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887102"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>La création du cluster échoue avec InvalidNetworkConfigurationErrorCode in Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

Si vous voyez le code d’erreur `InvalidNetworkConfigurationErrorCode` avec la description « La configuration du réseau virtuel n’est pas compatible avec la spécification HDInsight », cela indique généralement un problème avec la [configuration du réseau virtuel](../hdinsight-plan-virtual-network-deployment.md) pour votre cluster. En fonction du reste de la description de l’erreur, suivez les sections ci-dessous pour résoudre votre problème.

## <a name="hostname-resolution-failed"></a>« Échec de la résolution du nom d’hôte »

### <a name="issue"></a>Problème

La description de l’erreur contient le message « Échec de la résolution du nom d’hôte ».

### <a name="cause"></a>Cause :

Cette erreur pointe vers un problème de configuration DNS personnalisée. Les serveurs DNS au sein d’un réseau virtuel peuvent transférer les requêtes DNS aux programmes de résolution récursifs d’Azure pour résoudre les noms d’hôte au sein de ce réseau virtuel (pour plus d’informations, consultez [Résolution de noms dans les réseaux virtuels](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)). Les programmes de résolution récursifs d’Azure sont accessibles via l’adresse IP virtuelle 168.63.129.16. Cette adresse IP est uniquement accessible à partir des machines virtuelles Azure. Par conséquent, il ne fonctionnera pas si vous utilisez un serveur DNS local ou si votre serveur DNS est une machine virtuelle Azure, qui ne fait pas partie du réseau virtuel du cluster.

### <a name="resolution"></a>Résolution

1. Utilisez SSH dans la machine virtuelle qui fait partie du cluster, puis exécutez la commande `hostname -f`. Cela renverra le nom de domaine complet de l’hôte (appelé `<host_fqdn>` dans les instructions ci-dessous).

1. Exécutez ensuite la commande `nslookup <host_fqdn>` (par exemple `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`). Si cette commande résout le nom en adresse IP, cela signifie que votre serveur DNS fonctionne correctement. Dans ce cas, déclenchez un cas de support avec HDInsight et nous étudierons votre problème. Dans votre cas de support, incluez les étapes de dépannage que vous avez exécutées. Cela nous aidera à résoudre le problème plus rapidement.

1. Si la commande ci-dessus ne retourne pas d’adresse IP, exécutez `nslookup <host_fqdn> 168.63.129.16` (par exemple `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`). Si cette commande est en mesure de résoudre l’adresse IP, cela signifie que votre serveur DNS ne transfère pas la requête au DNS d’Azure, ou qu’il ne s’agit pas d’une machine virtuelle qui fait partie du même réseau virtuel que le cluster.

1. Si vous ne disposez pas d’une machine virtuelle Azure pouvant agir en tant que serveur DNS personnalisé dans le réseau virtuel du cluster, vous devez d’abord l’ajouter. Créez une machine virtuelle dans le réseau virtuel, qui sera configuré en tant que redirecteur DNS.

1. Une fois que vous avez déployé une machine virtuelle dans votre réseau virtuel, configurez les règles de transfert DNS sur cette machine virtuelle. Transférez toutes les demandes de résolution de noms iDNS à 168.63.129.16 et le reste à votre serveur DNS. [Voici](../hdinsight-plan-virtual-network-deployment.md) un exemple de cette configuration pour un serveur DNS personnalisé.

1. Ajoutez l’adresse IP de cette machine virtuelle en tant que première entrée DNS pour la configuration DNS du réseau virtuel.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>« Échec de la connexion au compte de stockage Azure »

### <a name="issue"></a>Problème

La description de l’erreur contient le message « Échec de la connexion au compte de stockage Azure » ou « Échec de la connexion à Azure SQL ».

### <a name="cause"></a>Cause :

Le Stockage Azure et SQL n’ont pas d’adresses IP fixes. Nous devons donc autoriser les connexions sortantes vers toutes les adresses IP pour autoriser l’accès à ces services. Les étapes de résolution exactes varient selon que vous avez configuré un groupe de sécurité réseau (NSG) ou des règles définies par l’utilisateur (UDR). Pour plus d’informations sur ces configurations, reportez-vous à la section sur le [contrôle du trafic réseau avec HDInsight avec les groupes de sécurité réseau et les itinéraires définis par l’utilisateur](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip).

### <a name="resolution"></a>Résolution

* Si votre cluster utilise un [groupe de sécurité réseau (NSG](../../virtual-network/virtual-network-vnet-plan-design-arm.md)).

    Accédez au Portail Microsoft Azure et identifiez le groupe de sécurité réseau associé au sous-réseau dans lequel le cluster est en cours de déploiement. Dans la section **Règles de sécurité de trafic sortant**, autoriser l’accès sortant à Internet sans limitation (notez qu' il s’agit d’un numéro de **priorité** plus faible ici signifie une priorité plus élevée). En outre, dans la section **Sous-réseaux**, vérifiez si ce groupe de sécurité réseau est appliqué au sous-réseau du cluster.

* Si votre cluster utilise des [itinéraires définis par l’utilisateur (UDR)](../../virtual-network/virtual-networks-udr-overview.md).

    Accédez au Portail Azure et identifiez le tableau de routage associe au sous-réseau sur lequel le cluster est déployé. Une fois que vous avez trouvé le tableau de routage pour le sous-réseau, examinez la section **Itinéraires** dans celle-ci.

    Si des itinéraires sont définis, assurez-vous qu’il existe des itinéraires pour les adresses IP de la région où le cluster a été déployé et que le **NextHopType** de chaque itinéraire est **Internet**. Un itinéraire doit être défini pour chaque adresse IP requise décrite dans l’article ci-dessus.

---

### <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour en savoir plus, voir [Création d’une requête de support Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
