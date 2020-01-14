---
title: Vue d’ensemble des charges de travail Red Hat sur Azure | Microsoft Docs
description: Découvrez les offres de produits Red Hat disponibles sur Azure
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: df787d4102752bdf61e30bc00d0d08307ac12319
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454973"
---
# <a name="red-hat-workloads-on-azure"></a>Charges de travail Red Hat sur Azure
Les charges de travail Red Hat sont prises en charge par le biais de diverses offres sur Azure. Les images RHEL (Red Hat Enterprise Linux) sont au cœur des charges de travail RHEL, tout comme l’infrastructure RHUI (Red Hat Update Infrastructure).

## <a name="red-hat-enterprise-linux-rhel-images"></a>Images RHEL (Red Hat Enterprise Linux)
Azure propose une offre étendue d’images RHEL sur Azure. Ces images sont mises à disposition par le biais de deux modèles de licence différents : paiement à l’utilisation (PAYG) et Bring Your Own Subscription (BYOS). De nouvelles images RHEL sur Azure sont publiées lorsque de nouvelles versions RHEL sont commercialisées et mises à jour tout au long de leur cycle de vie, si nécessaire.

### <a name="pay-as-you-go-payg-images"></a>Images avec paiement à l’utilisation (PAYG)
Azure propose différentes images RHEL PAYG. Ces images sont correctement autorisées pour RHEL et sont associées à une source de mises à jour (Red Hat Update infrastructure). Des frais Premium sont facturés pour ces images en ce qui concerne les droits d’utilisation de RHEL et les mises à jour. Les variantes d’images RHEL PAYG sont notamment :
* RHEL standard
* RHEL for SAP
* RHEL for SAP avec des services de haute disponibilité et de mise à jour.

Vous pouvez utiliser les images PAYG si vous ne souhaitez pas vous soucier de payer séparément le nombre approprié d’abonnements.

### <a name="bring-your-own-subscription-byos-images"></a>Images Bring Your Own Subscription (BYOS)
Azure propose également des images RHEL BYOS. Ces images peuvent être utiles aux clients qui ont des abonnements Red Hat existants et souhaitent les utiliser dans Azure. Vous devez convertir vos abonnements existants en abonnements d’accès au cloud avant de pouvoir les utiliser dans Azure. L’accès à ces images est accordé uniquement lorsque Red Hat vérifie que vous disposez d’un nombre suffisant d’abonnements d’accès au cloud. L’utilisation de ces images permet à un client d’éviter une double facturation qui peut être encourue suite à l’utilisation des images PAYG. Vous pouvez demander l’accès aux images BYOS [ici](https://aka.ms/rhel-byos).

> [!NOTE]
> Remarque sur la double facturation : Une double facturation est encourue quand un utilisateur paye deux fois les abonnements RHEL. Cela se produit généralement lorsqu’un client utilise un gestionnaire d’abonnements pour joindre des droits d’utilisation à une machine virtuelle RHEL PAYG. Par exemple, un client qui utilise un gestionnaire d’abonnements pour joindre des droits d’utilisation pour des packages SAP à une image RHEL PAYG sera indirectement doublement facturé, car il payera deux fois pour RHEL : une fois via les frais Premium PAYG et une fois par le biais de son abonnement SAP. Cela ne se produit pas pour les utilisateurs d’images BYOS.

## <a name="red-hat-update-infrastructure-rhui"></a>RHUI (Red Hat Update Infrastructure)
Azure fournit une infrastructure RHUI (Red Hat Update Infrastructure) uniquement pour les machines virtuelles RHEL PAYG. RHUI est en fait un miroir des réseaux de distribution de contenu Red Hat, mais n’est accessible qu’aux machines virtuelles RHEL PAYG Azure. Vous aurez accès aux packages appropriés en fonction de l’image RHEL que vous avez déployée. Par exemple, une image RHEL for SAP aura accès aux packages SAP en plus des packages RHEL de base.

### <a name="rhui-update-behavior"></a>Comportement de mise à jour RHUI
Les images RHEL connectées à RHUI effectuent, par défaut, une mise à jour vers la dernière version mineure de RHEL lors de l’exécution d’une `yum update`. Ce comportement signifie qu’une machine virtuelle RHEL 7.4 peut être mise à niveau vers RHEL 7.7 si une opération `yum update` est exécutée sur celle-ci. Cela est lié à la conception de RHUI. Toutefois, ce comportement de mise à niveau peut être atténué en basculant des dépôts RHEL standard vers les [dépôts EUS (Extended Update support)](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur les [images RHEL sur Azure](./redhat-images.md)
* En savoir plus sur l’[infrastructure RHUI (Red Hat Update Infrastructure)](./redhat-rhui.md)
* En savoir plus sur l’[offre RHEL BYOS](./redhat-byos.md)