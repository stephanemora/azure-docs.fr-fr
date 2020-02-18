---
title: Vue d’ensemble des charges de travail Red Hat sur Azure | Microsoft Docs
description: Découvrez les offres de produits Red Hat disponibles sur Azure
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: daba49e6861eb67fd07c6fcf618b2b2d6cdd8c89
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133823"
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

### <a name="red-hat-gold-images-rhel-byos"></a>Images Red Hat Gold (`rhel-byos`)
Azure propose également des images Red Hat Gold. Ces images peuvent être utiles aux clients qui ont des abonnements Red Hat existants et souhaitent les utiliser dans Azure. Vous devez activer vos abonnements Red Hat existants pour Red Hat Cloud Access avant de pouvoir les utiliser dans Azure. L’accès à ces images vous est accordé automatiquement quand vos abonnements Red Hat sont activés pour Cloud Access et qu’ils répondent aux conditions d’éligibilité. L’utilisation de ces images permet à un client d’éviter une double facturation qui peut être encourue suite à l’utilisation des images PAYG.
* [Découvrir comment activer vos abonnements Red Hat pour Cloud Access avec Azure](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs)
* [Découvrir comment localiser les images Red Hat Gold à l’aide du portail Azure, de l’interface CLI ou d’une applet de commande PowerShell](./byos.md)

> [!NOTE]
> Remarque sur la double facturation : Une double facturation est encourue quand un utilisateur paye deux fois les abonnements RHEL. Cela se produit généralement lorsqu’un client utilise un gestionnaire d’abonnements pour joindre des droits d’utilisation à une machine virtuelle RHEL PAYG. Par exemple, un client qui utilise un gestionnaire d’abonnements pour joindre des droits d’utilisation pour des packages SAP à une image RHEL PAYG sera indirectement doublement facturé, car il payera deux fois pour RHEL : une fois via les frais Premium PAYG et une fois par le biais de son abonnement SAP. Cela ne se produit pas pour les utilisateurs d’images BYOS.

### <a name="generation-2-images"></a>Images de 2e génération
Les machines virtuelles de 2e génération fournissent des fonctionnalités plus récentes par rapport aux machines virtuelles de 1ère génération. Les détails sont présentés dans la [documentation sur la 2e génération](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2). La principale différence par rapport à la perspective d’une image RHEL est que les machines virtuelles de 2e génération utilisent une interface UEFI au lieu d’une interface de microprogramme BIOS, et utilisent une table de partition GUID (GPT) au lieu d’un enregistrement de démarrage principal (MBR) au moment du démarrage. Cela permet, entre autres, des tailles de disque de système d’exploitation supérieures à 2 To. De plus, les [machines virtuelles de la série Mv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#mv2-series) s’exécutent uniquement sur les images de 2e génération.

Les images RHEL de 2e génération sont disponibles dans la Place de marché. Recherchez « gen2 » dans la référence SKU d’image quand vous listez toutes les images à l’aide de l’interface de ligne de commande Azure, puis accédez à l’onglet « Avancé » dans le processus de déploiement de machine virtuelle pour déployer une machine virtuelle de 2e génération.

## <a name="red-hat-update-infrastructure-rhui"></a>RHUI (Red Hat Update Infrastructure)
Azure fournit une infrastructure RHUI (Red Hat Update Infrastructure) uniquement pour les machines virtuelles RHEL PAYG. RHUI est en fait un miroir des réseaux de distribution de contenu Red Hat, mais n’est accessible qu’aux machines virtuelles RHEL PAYG Azure. Vous aurez accès aux packages appropriés en fonction de l’image RHEL que vous avez déployée. Par exemple, une image RHEL for SAP aura accès aux packages SAP en plus des packages RHEL de base.

### <a name="rhui-update-behavior"></a>Comportement de mise à jour RHUI
Les images RHEL connectées à RHUI effectuent, par défaut, une mise à jour vers la dernière version mineure de RHEL lors de l’exécution d’une `yum update`. Ce comportement signifie qu’une machine virtuelle RHEL 7.4 peut être mise à niveau vers RHEL 7.7 si une opération `yum update` est exécutée sur celle-ci. Cela est lié à la conception de RHUI. Toutefois, ce comportement de mise à niveau peut être atténué en basculant des dépôts RHEL standard vers les [dépôts EUS (Extended Update support)](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur les [images RHEL sur Azure](./redhat-images.md)
* En savoir plus sur l’[infrastructure RHUI (Red Hat Update Infrastructure)](./redhat-rhui.md)
* En savoir plus sur l’[offre d’images Red Hat Gold (`rhel-byos`)](./byos.md)
