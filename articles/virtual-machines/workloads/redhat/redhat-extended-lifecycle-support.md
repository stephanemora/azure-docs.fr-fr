---
title: Support étendu du cycle de vie Red Hat Enterprise Linux
description: Découvrez le module complémentaire de support du cycle de vie étendu de Red Hat Enterprise
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: mathapli
ms.reviewer: cynthn
ms.openlocfilehash: e7c9f9b158d01204536712f8024ed098cd7a5037
ms.sourcegitcommit: 65d518d1ccdbb7b7e1b1de1c387c382edf037850
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94372856"
---
# <a name="red-hat-enterprise-linux-rhel-extended-lifecycle-support"></a>Support du cycle de vie étendu de Red Hat Enterprise Linux (RHEL)
Cet article fournit des informations sur le support du cycle de vie étendu des images Red Hat Enterprise :
* Red Hat Enterprise Linux 6  

## <a name="red-hat-enterprise-linux-6-lifecycle"></a>Cycle de vie de Red Hat Enterprise Linux 6
À partir du 30 novembre 2020, Red Hat Enterprise Linux 6 atteindra la fin de la phase de maintenance. La phase de maintenance est suivie de la phase de vie étendue. Lorsque Red Hat Enterprise Linux 6 approche de la fin des phases complète/de maintenance, il est fortement recommandé de procéder à une mise à niveau vers Red Hat Enterprise Linux 7 ou 8. Si des clients doivent rester sur Red Hat Enterprise Linux 6, il est recommandé d’ajouter le module complémentaire de support du cycle de vie étendu (ELS) de Red Hat Enterprise Linux.

## <a name="frequently-asked-questions"></a>Forum Aux Questions (FAQ)

#### <a name="im-running-red-hat-enterprise-linux-6-and-cant-migrate-to-a-later-version-at-this-time-what-options-do-i-have"></a>J’exécute Red Hat Enterprise Linux 6 et ne peut pas migrer vers une version ultérieure pour l’instant. Quelles sont les options dont je dispose ?
* Continuez à exécuter Red Hat Enterprise Linux 6 et achetez les référentiels du module complémentaire de support du cycle de vie étendu (ELS) pour continuer à bénéficier d’une maintenance logicielle et d’un support technique limités (voir le processus de mise à niveau et les détails de tarification ci-dessous).
* Migrez vers Red Hat Enterprise Linux 7 ou 8 dès que possible.

#### <a name="what-is-the-additional-charge-for-using-red-hat-enterprise-linux-extended-life-cycle-support-els-add-on"></a>Quels sont les frais supplémentaires liés à l’utilisation du module complémentaire de support du cycle de vie étendu (ELS) de Red Hat Enterprise Linux ?

|Taille de la machine virtuelle|Période de facturation supplémentaire|Montant supplémentaire en dollars (USD)| Notes|
|---|---|---|---|
| Petit invité virtuel (<= 4 cœurs) | Frais supplémentaires horaires | À confirmer | |
|  | Frais supplémentaires mensuels | À confirmer | Pour instances réservées |
|  | Frais supplémentaires annuels | À confirmer | Pour instances réservées |
| Grand invité virtuel (> 4 cœurs) | Frais supplémentaires horaires | À confirmer | |
|  | Frais supplémentaires mensuels | À confirmer | Pour instances réservées |
|  | Frais supplémentaires annuels | À confirmer | Pour instances réservées |

#### <a name="what-is-the-process-to-add-extended-life-cycle-support-els-repositories-to-continue-to-receive-software-maintenance-bug-and-security-fixes-and--support-for-red-hat-enterprise-linux-6"></a>Quel est le processus d’ajout de référentiels de support de cycle de vie étendu (ELS) pour continuer à bénéficier de la maintenance logicielle (correctifs de bogues et de sécurité) et du support pour Red Hat Enterprise Linux 6 ?

Le processus de bout en bout de souscription de l’ELS sera bientôt disponible ici (au plus tard le 15 novembre 2020).

## <a name="next-steps"></a>Étapes suivantes

* Pour voir la liste complète des images RHEL dans Azure, consultez [Images RHEL (Red Hat Enterprise Linux) disponibles dans Azure](./redhat-imagelist.md).
* Pour en savoir plus sur Azure Red Hat Update Infrastructure, consultez [Red Hat Update Infrastructure pour les machines virtuelles RHEL à la demande dans Azure](./redhat-rhui.md).
* Pour en savoir plus sur l’offre RHEL BYOS, consultez [Images Gold BYOS (Apportez votre propre abonnement) de Red Hat Enterprise Linux dans Azure](./byos.md).
* Pour obtenir des informations sur les stratégies de prise en charge de Red Hat pour toutes les versions de RHEL, consultez la page [Cycle de vie de Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).