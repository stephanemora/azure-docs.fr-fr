---
title: Créer une offre de machine virtuelle sur la Place de marché Azure.
description: Créer une offre de machine virtuelle sur la Place de marché Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 04/08/2021
ms.openlocfilehash: 626ee3d9fb7ec193e1384712a03c23feefef67af
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2021
ms.locfileid: "111525027"
---
# <a name="create-a-virtual-machine-offer-on-azure-marketplace"></a>Créer une offre de machine virtuelle sur la Place de marché Azure

Cet article explique comment créer une offre de machine virtuelle Azure sur[la Place de marché Azure](https://azuremarketplace.microsoft.com/). Il s’applique aux machines virtuelles Windows et Linux qui contiennent un système d’exploitation, un disque dur virtuel (VHD) et jusqu’à 16 disques de données.

Avant de commencer, [créez un compte Place de marché commerciale dans l’Espace partenaires](create-account.md). Vérifiez que votre compte est inscrit dans le programme de Place de marché commerciale.

## <a name="before-you-begin"></a>Avant de commencer

Si vous ne l’avez pas encore fait, passez en revue [Planifier une offre de machine virtuelle](marketplace-virtual-machines.md). Elle explique les exigences techniques de votre machine virtuelle et répertorie les informations et les ressources dont vous aurez besoin lors de la création de votre offre.

## <a name="create-a-new-offer"></a>Créer une offre

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/home).
2. Dans le volet gauche, sélectionnez **Place de marché commerciale** > **Vue d’ensemble**.
3. Dans la page **Vue d’ensemble**, sélectionnez **Nouvelle offre** > **Machine virtuelle Azure**.

    ![Capture d’écran montrant les options du menu du volet gauche et le bouton « Nouvelle offre ».](./media/create-vm/new-offer-azure-virtual-machine.png)

> [!NOTE]
> Après publication d’une offre, les modifications que vous y apportez dans l’Espace partenaires apparaissent sur la Place de marché Azure uniquement après la republication de l’offre. Veillez à toujours republier une offre après y avoir apporté des modifications.

Entrez un **ID d’offre**. Il s’agit d’un identificateur unique par offre dans votre compte.

- Cet ID est visible par les clients dans l’adresse web de l’offre de la Place de marché Azure ainsi que dans Azure PowerShell et Azure CLI, le cas échéant.
- Utilisez uniquement des lettres minuscules et des chiffres. L’ID peut inclure des traits d’union et des traits de soulignement, mais pas d’espaces, et est limité à 50 caractères. Par exemple, si vous entrez **test-offer-1**, l’adresse web de l’offre sera `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- L’ID d’offre ne peut pas être changé une fois que vous avez sélectionné **Créer**.

Entrez un **Alias d’offre**. L’alias d’offre est le nom attribué à l’offre dans l’Espace partenaires.

- Ce nom n’est pas utilisé sur la Place de marché Azure. Il est différent du nom de l’offre et des autres valeurs présentées aux clients.

Sélectionnez **Créer** pour générer l’offre et continuer. Espace partenaires ouvre la page **Configuration de l’offre**.

## <a name="test-drive-optional"></a>Vestion d’évaluation (facultatif)

Une version d’évaluation constitue un excellent moyen de présenter votre offre à des clients potentiels en leur donnant accès à un environnement préconfiguré pendant un nombre fixe d’heures. L’offre d’une version d’évaluation entraîne un taux de conversion accru et génère des prospects hautement qualifiés. Pour en savoir plus sur les versions d’évaluation, consultez [Qu’est-ce qu’une version d’évaluation ?](./what-is-test-drive.md).

> [!TIP]
> Une version d’évaluation n’est pas un essai gratuit. Vous pouvez proposer une version d’évaluation, un essai gratuit ou les deux. Dans les deux cas, vos clients disposent de votre solution pendant une période fixe. Cependant, une version d’évaluation propose en outre une visite autoguidée concrète des principales fonctionnalités de votre produit, ainsi qu’une démonstration des avantages de celui-ci au travers d’un scénario d’implémentation réel.

Pour activer une version d'évaluation, cochez la case **Activer une version d'évaluation**. Vous configurerez ultérieurement la version d'évaluation. La version d'évaluation nécessite la configuration d’un système CRM (voir la section suivante). Pour supprimer une version d'évaluation de votre offre, désactivez cette case à cocher.

## <a name="customer-leads"></a>Prospects

[!INCLUDE [Customer leads](includes/customer-leads.md)] 

Si vous effectuez des modifications, sélectionnez **Enregistrer le brouillon** avant de poursuivre vers le prochaine tabulation du menu de navigation gauche **Propriétés**.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les propriétés de l’offre de machine virtuelle](azure-vm-create-properties.md)
- [Bonnes pratiques pour le référencement des offres](gtm-offer-listing-best-practices.md)