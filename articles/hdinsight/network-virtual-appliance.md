---
title: Configurer une appliance virtuelle réseau dans Azure HDInsight
description: Découvrez comment configurer un certain nombre de fonctionnalités supplémentaires pour votre appliance virtuelle réseau dans Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: cbc2104ae3c55ae3670867b7a253d812f3a4be0e
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82868278"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Configurer une appliance virtuelle réseau dans Azure HDInsight

> [!Important]
> Les informations suivantes sont requises **uniquement** si vous souhaitez configurer une appliance virtuelle réseau (NVA) autre que le pare-feu Azure.

Le pare-feu Azure est automatiquement configuré pour autoriser le trafic dans la plupart des scénarios importants courants. L’utilisation d’une autre appliance virtuelle réseau vous oblige à configurer un certain nombre de fonctionnalités supplémentaires. Gardez les facteurs suivants à l’esprit pendant la configuration de votre appliance virtuelle réseau :

* Les services compatibles avec les points de terminaison de service doivent être configurés avec des points de terminaison de service.
* Les dépendances d’adresses IP sont destinées au trafic non HTTP/S (à la fois le trafic TCP et UDP).
* Les points de terminaison HTTP/HTTPS avec des noms FQDN peuvent être placés dans votre dispositif NVA.
* Les points de terminaison HTTP/HTTPS avec des caractères génériques sont des dépendances qui peuvent varier selon le nombre de qualificateurs.
* Affectez la table de routage créée à votre sous-réseau HDInsight.

## <a name="service-endpoint-capable-dependencies"></a>Dépendances compatibles avec les points de terminaison de service

| **Point de terminaison** |
|---|
| Azure SQL |
| Stockage Azure |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>Dépendances des adresses IP

| **Point de terminaison** | **Détails** |
|---|---|
| \*:123 | Vérification de l’horloge NTP. Le trafic est vérifié à plusieurs points de terminaison sur le port 123 |
| Adresses IP publiées [ici](hdinsight-management-ip-addresses.md) | Ces adresses IP sont associées au service HDInsight |
| Adresses IP privées AAD-DS pour les clusters ESP |
| \*:16800 pour KMS Windows Activation |
| \*12000 pour Log Analytics |

### <a name="fqdn-httphttps-dependencies"></a>Dépendances HTTP/HTTPS FQDN

> [!Important]
> La liste ci-dessous contient seulement quelques-uns des noms FQDN les plus importants. Si vous avez besoin de noms FQDN supplémentaires (principalement Stockage Azure et Azure Service Bus) pour configurer votre appliance virtuelle réseau (NVA), accédez à [ce fichier](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

| **Point de terminaison**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                           |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser un pare-feu pour limiter le trafic sortant](./hdinsight-restrict-outbound-traffic.md)
* [Architecture de réseau virtuel Azure HDInsight](hdinsight-virtual-network-architecture.md)
