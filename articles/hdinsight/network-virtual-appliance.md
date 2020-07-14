---
title: Configurer une appliance virtuelle réseau dans Azure HDInsight
description: Découvrez comment configurer un certain nombre de fonctionnalités supplémentaires pour votre appliance virtuelle réseau dans Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 805be8d5c9ab4f6316251adbb9bce3e99f4fa01d
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086668"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Configurer une appliance virtuelle réseau dans Azure HDInsight

> [!Important]
> Les informations suivantes sont requises **uniquement** si vous souhaitez configurer une appliance virtuelle réseau (NVA) autre que le pare-feu Azure.

Le pare-feu Azure est automatiquement configuré pour autoriser le trafic dans la plupart des scénarios importants courants. L’utilisation d’une autre appliance virtuelle réseau vous oblige à configurer un certain nombre de fonctionnalités supplémentaires. Gardez les facteurs suivants à l’esprit pendant la configuration de votre appliance virtuelle réseau :

* Les services pouvant prendre en charge les points de terminaison de service peuvent être configurés avec des points de terminaison de service, ce qui entraîne le contournement du NVA, généralement pour des considérations relatives aux coûts ou aux performances.
* Les dépendances d’adresses IP sont destinées au trafic non HTTP/S (à la fois le trafic TCP et UDP).
* Les points de terminaison HTTP/HTTPS avec des noms FQDN peuvent être mis sur liste verte dans votre dispositif NVA.
* Affectez la table de routage créée à votre sous-réseau HDInsight.

## <a name="service-endpoint-capable-dependencies"></a>Dépendances compatibles avec les points de terminaison de service

Vous pouvez éventuellement activer un ou plusieurs points de terminaison de service suivants, ce qui entraînera le contournement du NVA. Cette option peut être utile pour de grandes quantités de transferts de données afin de réduire les coûts et d’optimiser les performances. 

| **Point de terminaison** |
|---|
| Azure SQL |
| Stockage Azure |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>Dépendances des adresses IP

| **Point de terminaison** | **Détails** |
|---|---|
| Adresses IP publiées [ici](hdinsight-management-ip-addresses.md) | Ces adresses IP sont destinées à l’emplacement de contrôle HDInsight et doivent être incluses dans UDR pour éviter le routage asymétrique |
| Adresses IP privées AAD-DS | Nécessaires uniquement pour les clusters ESP|


### <a name="fqdn-httphttps-dependencies"></a>Dépendances HTTP/HTTPS FQDN

> [!Important]
> La liste ci-dessous contient seulement quelques-uns des noms FQDN les plus importants. Si vous avez d’une liste complète de noms FQDN supplémentaires (principalement Stockage Azure et Azure Service Bus) pour configurer votre appliance virtuelle réseau (NVA), accédez à [ce fichier](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json). Ces dépendances sont utilisées par les opérations du plan de contrôle HDInsight pour créer un cluster.

| **Point de terminaison**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser un pare-feu pour limiter le trafic sortant](./hdinsight-restrict-outbound-traffic.md)
* [Architecture de réseau virtuel Azure HDInsight](hdinsight-virtual-network-architecture.md)
