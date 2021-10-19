---
title: Considérations relatives à l’application des correctifs pour BareMetal pour Oracle
description: En savoir plus sur les considérations relatives à l’application des correctifs pour le système d’exploitation/noyau pour BareMetal Infrastructure pour Oracle.
ms.topic: reference
ms.subservice: baremetal-oracle
ms.date: 10/06/2021
ms.openlocfilehash: c842ad56e6777817e5db84e51a4d1302aebf5a63
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129657489"
---
# <a name="patching-considerations-for-baremetal-for-oracle"></a>Considérations relatives à l’application des correctifs pour BareMetal pour Oracle

Dans cet article, nous allons examiner les considérations importantes relatives aux correctifs du système d’exploitation/noyau pour BareMetal Infrastructure pour Oracle.

Pour des performances réseau et une stabilité du système appropriées, installez la version spécifique au système d’exploitation des pilotes eNIC et fNIC. Consultez le tableau de compatibilité suivant. 

Les serveurs sont fournis aux clients avec des versions compatibles. Toutefois, la mise à jour corrective du système d’exploitation et/ou du noyau peut entraîner la restauration de la version par défaut des pilotes. Veillez donc à confirmer que la version correcte du pilote est exécutée après la mise à jour corrective du système d’exploitation/noyau.

| Fournisseur du système d’exploitation | Version du package du système d’exploitation | Version du microprogramme | Pilote eNIC | Pilote fNIC |
| --- | --- | --- | --- | --- |
| Red Hat | RHEL 7.6 | 3.2.3i | 2.3.0.53 | 1.6.0.34 |
| Red Hat | RHEL 7.6 | 4.1.1b | 2.3.0.53 | 1.6.0.34 |

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la configuration Ethernet de BareMetal pour Oracle.

> [!div class="nextstepaction"]
> [Configuration Ethernet de BareMetal pour Oracle](oracle-baremetal-ethernet.md)

