---
title: FAQ sur le calcul confidentiel Azure
description: Réponses aux questions fréquemment posées sur le calcul confidentiel Azure.
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: workloads
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: e9cb691ef60f612672078a9ef84db904c79cbc87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82189443"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Forum aux questions (FAQ) sur le calcul confidentiel Azure

Cet article fournit des réponses à certaines des questions les plus courantes concernant l’exécution de [charges de travail informatiques confidentielles sur Azure](overview.md).

Si le problème lié à Azure n’est pas traité dans cet article, parcourez les forums Azure sur [MSDN et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez publier votre problème sur ces forums ou [@AzureSupport sur Twitter](https://twitter.com/AzureSupport). Vous pouvez également soumettre une demande de support Azure. Pour soumettre une demande de support, sur la [page de support Azure](https://azure.microsoft.com/support/options/), sélectionnez Obtenir de l’aide.

## <a name="confidential-computing-virtual-machines"></a>Machines virtuelles de calcul confidentiel <a id="vm-faq"></a>

1. **Comment commencer à déployer des machines virtuelles de la série DCsv2 ?**

   Voici quelques méthodes permettant de déployer une machine virtuelle DCsv2 :
   - En utilisant un [modèle Azure Resource Manager](../virtual-machines/windows/template-description.md).
   - À partir du [portail Azure](https://portal.azure.com/#create/hub)
   - Dans le modèle de solution de la Place de marché Azure [Calcul confidentiel Azure (machine virtuelle)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview). Le modèle de solution de la Place de marché permet de contraindre un client aux scénarios pris en charge (régions, images, disponibilité, chiffrement de disque). 

1. **Toutes les images de système d’exploitation fonctionnent-elles avec le calcul confidentiel Azure ?**

   Non. Les machines virtuelles ne peuvent être déployées que sur des machines virtuelles de génération 2. Nous proposons la prise en charge de la génération 2 pour Ubuntu Server 18,04, Ubuntu Server 16.04 et Windows Server 2016 Datacenter. En savoir plus sur les machines virtuelles de la génération 2 sur [Linux](../virtual-machines/linux/generation-2.md) et [Windows](../virtual-machines/windows/generation-2.md)

1. **Les machines virtuelles DCsv2 sont grisées dans le portail et je ne peux en sélectionner aucune**

   Selon l’infobulle en regard de la machine virtuelle, différentes actions sont possibles :
    -   **UnsupportedGeneration** : Modifiez la génération de l’image de machine virtuelle en « Gen2 ».
    -   **NotAvailableForSubscription** : La région n’est pas encore disponible pour votre abonnement. Sélectionnez une région disponible.
    -   **InsufficientQuota** : [Créez une demande de support pour augmenter votre quota](../azure-portal/supportability/per-vm-quota-requests.md). Les abonnements d’évaluation gratuite n’ont pas de quota pour les machines virtuelles de calcul confidentiel. 

1. **Les machines virtuelles DCsv2 ne s’affichent pas lorsque je les recherche dans le sélecteur de taille du portail**

   Vérifiez que vous avez sélectionné une région disponible. Veillez également à activer l’option « Effacer tous les filtres » dans le sélecteur de taille. 

1. **Quelle est la différence entre les machines virtuelles des séries DCsv2 et DC ?**

   Les machines virtuelles de la série DC s’exécutent sur d’anciens processeurs Intel à 6 cœurs avec Intel SGX. Elles ont moins de mémoire totale, moins de mémoire EPC (Enclave Page Cage) et sont disponibles dans moins de régions. Ces machines virtuelles ne sont disponibles que dans les régions USA Est et Europe occidentale, et uniquement dans deux tailles : Standard_DC2s et Standard_DC4s. Elles ne seront pas mises à la disposition générale et ne peuvent être déployées que dans l’instance de la Place de marché [Machine virtuelle de série DC pour calcul confidentiel [préversion]](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview).

1. **Les machines virtuelles DCsv2 sont-elles disponibles dans le monde entier ?**

   Non. Ces machines virtuelles ne sont disponibles que dans certaines régions. Pour connaître les dernières régions disponibles, consultez la [page des produits par région](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines). 

1. **Comment installer le Kit de développement logiciel (SDK) Open Enclave ?**
   
   Pour obtenir des instructions sur l’installation du Kit de développement logiciel (SDK) Open Enclave sur une machine Azure ou locale, suivez les instructions du [GitHub SDK Open Enclave](https://github.com/openenclave/openenclave).
     
   Vous pouvez également accéder au GitHub SDK Open Enclave pour obtenir des instructions d’installation spécifiques du système d’exploitation :
     - [Installer le kit de développement logiciel (SDK) Open Enclave sur Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
     - [Installer le kit de développement logiciel (SDK) Open Enclave sur Ubuntu 18.04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
     - [Installer le kit de développement logiciel (SDK) Open Enclave sur Ubuntu 16.04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)
