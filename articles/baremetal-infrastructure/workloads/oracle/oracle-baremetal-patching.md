---
title: Considérations relatives à l’application des correctifs pour BareMetal pour Oracle
description: En savoir plus sur les considérations relatives à l’application des correctifs pour le système d’exploitation/noyau pour BareMetal Infrastructure pour Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 5af713c776def8af558531c84013b221a8d30087
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558596"
---
# <a name="patching-considerations-for-baremetal-for-oracle"></a>Considérations relatives à l’application des correctifs pour BareMetal pour Oracle

Dans cet article, nous allons examiner les considérations importantes relatives aux correctifs du système d’exploitation/noyau pour BareMetal Infrastructure pour Oracle.

Pour des performances réseau et une stabilité du système appropriées, installez la version spécifique au système d’exploitation des pilotes eNIC et fNIC comme indiqué dans le tableau de compatibilité suivant. 

Les serveurs sont fournis aux clients avec des versions compatibles. Toutefois, lors de la mise à jour corrective du système d’exploitation/noyau, les pilotes peuvent être restaurés vers les versions de pilote par défaut. Veillez donc à confirmer que la version appropriée du pilote exécute les opérations de mise à jour corrective du système d’exploitation/noyau suivantes.

| Fournisseur du système d’exploitation | Version du package du système d’exploitation | Version du microprogramme | Pilote eNIC | Pilote fNIC |
| --- | --- | --- | --- | --- |
| Red Hat | RHEL 7.6 | 3.2.3i | 2.3.0.53 | 1.6.0.34 |
| Red Hat | RHEL 7.6 | 4.1.1b | 2.3.0.53 | 1.6.0.34 |

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la configuration Ethernet de BareMetal pour Oracle.

> [!div class="nextstepaction"]
> [Configuration Ethernet de BareMetal pour Oracle](oracle-baremetal-ethernet.md)

