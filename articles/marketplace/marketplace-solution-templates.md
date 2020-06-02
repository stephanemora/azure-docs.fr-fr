---
title: Guide de publication d’offres de modèle de solution d’applications Azure | Place de marché Azure
description: Cet article décrit les conditions requises pour la publication d’un modèle de solution sur la Place de marché Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: f62b3478c5c711423913b5918886b43b79ac691d
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858332"
---
# <a name="publishing-guide-for-azure-applications-solution-template-offers"></a>Guide de publication d’offres de modèle de solution d’applications Azure

Cet article explique les conditions requises pour la publication des offres de modèle de solution, qui est une façon de publier une offre d’application Azure dans la Place de marché Azure. Le type d’offre de modèle de solution nécessite un [modèle Azure Resource Manager (modèle ARM)](../azure-resource-manager/templates/overview.md) pour déployer automatiquement votre infrastructure de solution.

Utilisez le type d’offre de *modèle de solution* d’application Azure dans les conditions suivantes :

- Votre solution nécessite une automatisation supplémentaire de la configuration et du déploiement au-delà d’une seule machine virtuelle, par exemple une combinaison de machines virtuelles, de mise en réseau et de ressources de stockage.
- Vos clients vont gérer la solution eux-mêmes.

L’appel à l’action que voit le client pour ce type d’offre est *Obtenir maintenant*.

## <a name="requirements-for-solution-template-offers"></a>Conditions requises pour les offres de modèles de solution

| **Configuration requise** | **Détails**  |
| ---------------  | -----------  |
|Facturation et mesure    |  Les offres de modèle de solution ne sont pas des offres de transaction, mais peuvent être utilisés pour déployer des offres de machines virtuelles payantes facturées via la place de marché commerciale de Microsoft. Les ressources que le modèle ARM de la solution déploie sont configurées dans l’abonnement Azure du client. Les machines virtuelles avec paiement à l’utilisation font l’objet de transactions avec le client par le biais de Microsoft et sont facturées dans le cadre de l’abonnement Azure du client.<br/> Pour la facturation BYOL (apportez votre propre licence), bien que Microsoft facture les frais d’infrastructure engagés dans l’abonnement client, vous effectuez la transaction de vos frais de licence logicielle directement avec le client.   |
|Disque dur virtuel compatible avec Azure  |   Les machines virtuelles doivent être basées sur Windows ou Linux. Pour plus d'informations, consultez les pages suivantes : <ul> <li>[Créer une offre d’application Azure](./partner-center-portal/create-new-azure-apps-offer.md) (pour les disques durs virtuels Windows).</li><li>[Distributions Linux approuvées sur Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) (pour les disques durs virtuel Linux).</li></ul> |
| Attribution de l’utilisation client | L’activation de l’attribution de l’utilisation de client est obligatoire pour tous les modèles de solution publiés sur la Place de marché Azure. Pour plus d’informations sur l’attribution de l’utilisation de client et sur son activation, consultez [Attribution de l’utilisation de client partenaire Azure](./azure-partner-customer-usage-attribution.md).  |
| Utiliser des disques managés | Les [disques managés](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) représentent l’option par défaut pour les disques persistants des machines virtuelles IaaS (infrastructure as a service) dans Azure. Vous devez utiliser des disques managés dans les modèles de solution. <ul><li>Pour mettre à jour vos modèles de solution, suivez les instructions dans [Utiliser les disques managés dans les modèles Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) et utilisez les [exemples](https://github.com/Azure/azure-quickstart-templates) fournis.<br><br> </li><li>Pour publier le disque dur virtuel sous forme d’image dans la Place de marché Azure, importez les disques durs virtuels sous-jacents des disques managés dans un compte de stockage à l’aide de l’une des méthodes suivantes :<ul><li>[Azure PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [L’interface de ligne de commande Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul></ul> |

## <a name="next-steps"></a>Étapes suivantes

Si vous ne l’avez pas déjà fait, découvrez comment [développer votre activité dans le cloud avec la Place de marché Azure](https://azuremarketplace.microsoft.com/sell).

Pour vous inscrire à l’Espace partenaires et commencer à travailler dessus :

- [Connectez-vous à l’Espace partenaires](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) pour créer ou terminer votre offre.
- Pour plus d’informations, consultez [Créer une offre d’application Azure](./partner-center-portal/create-new-azure-apps-offer.md).
