---
title: Azure Hybrid Benefit et machines virtuelles Linux
description: Azure Hybrid Benefit vous permet de réaliser des économies sur vos machines virtuelles Linux s’exécutant sur Azure.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: westonh
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: 1459c80c857a436c8369d3ebe89794e0b69c33b1
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94980879"
---
# <a name="azure-hybrid-benefit--how-it-applies-for-linux-virtual-machines"></a>Comment Azure Hybrid Benefit s’applique-t-il aux machines virtuelles Linux ?

## <a name="overview"></a>Vue d’ensemble

Azure Hybrid Benefit vous permet de migrer plus facilement vos machines virtuelles Red Hat Enterprise Linux (RHEL) et SUSE Linux Enterprise Server (SLES) locales vers Azure à l’aide de votre propre abonnement logiciel Red Hat ou SUSE préexistant. Avec cet avantage, vous payez uniquement les coûts d’infrastructure de votre machine virtuelle, car les frais liés aux logiciels sont couverts par votre abonnement RHEL ou SLES. L’avantage s’applique à toutes les images PAYG (paiement à l’accès) de la place de marché RHEL et SLES.

> [!IMPORTANT]
> Azure Hybrid Benefit pour machines virtuelles Linux est accessible publiquement


## <a name="benefit-description"></a>Description de l’avantage

Azure Hybrid Benefit vous permet de migrer plus facilement vos serveurs locaux RHEL et SLES vers Azure en convertissant les machines virtuelles PAYG RHEL et SLES existantes sur Azure pour la facturation Bring Your Own Subscription (BYOS). En règle générale, les machines virtuelles déployées à partir d’images PAYG sur Azure génèrent des frais d’infrastructure et des frais de logiciel. Avec Azure Hybrid Benefit, les machines virtuelles PAYG peuvent être converties en modèle de facturation BYOS sans redéploiement, ce qui évite tout risque de temps d’arrêt.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Visualisation des coûts Azure Hybrid Benefit sur les machines virtuelles Linux.":::

Lors de l’activation de l’avantage sur une machine virtuelle RHEL ou SLES, vous ne serez plus facturé pour les frais de logiciel supplémentaires généralement engagés sur une machine virtuelle PAYG. Votre machine virtuelle fera l’objet d’une facturation BYOS, ce qui comprend uniquement les frais de matériel de calcul, et non les frais de logiciel.

Si vous le souhaitez, vous pouvez également convertir une machine virtuelle sur laquelle l’avantage a été activé pour revenir à un modèle de facturation PAYG.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Étendue de l’éligibilité Azure Hybrid Benefit pour les machines virtuelles Linux

Azure Hybrid Benefit est disponible pour toutes les images PAYG de la place de marché RHEL et SLES. L’avantage n’est pas encore disponible pour les images BYOS ou personnalisées de la place de marché RHEL ou SLES.

Les instances réservées, les hôtes dédiés et les avantages hybrides SQL ne sont pas éligibles à Azure Hybrid Benefit si vous utilisez déjà l’avantage avec des machines virtuelles Linux.

## <a name="how-to-get-started"></a>Pour commencer

### <a name="red-hat-customers"></a>Clients Red Hat

Azure Hybrid Benefit pour RHEL est accessible aux clients qui disposent d’abonnements RHEL actifs/inutilisés pouvant être utilisés dans Azure et qui ont activé un ou plusieurs de ces abonnements pour une utilisation dans Azure avec le programme [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access). 

1.  Activez un ou plusieurs de vos abonnements RHEL pouvant être utilisés dans Azure à l’aide de l’[interface client de Red Hat Cloud Access](https://access.redhat.com/management/cloud).
1.  Le ou les abonnements Azure fournis durant le processus d’activation de Red Hat Cloud Access sont alors autorisés à utiliser la fonctionnalité Azure Hybrid Benefit.
1.  Appliquez Azure Hybrid Benefit à l’une de vos machines virtuelles RHEL PAYG existantes ainsi qu’à toutes les nouvelles machines virtuelles RHEL que vous déployez à partir d’images PAYG de la Place de marché Azure.
1.  Effectuez les [étapes recommandées suivantes](https://access.redhat.com/articles/5419341) pour configurer des sources de mise à jour pour les machines virtuelles RHEL et tenir compte des consignes de conformité des abonnements RHEL.


### <a name="suse-customers"></a>Clients SUSE

1.    Inscrivez-vous au programme de cloud public SUSE.
1.    Appliquer l’avantage aux machines virtuelles existantes par le biais d’Azure CLI
1.    Inscrivez vos machines virtuelles recevant l’avantage avec une source de mises à jour distincte.


### <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Activez et désactivez l’avantage dans Azure CLI.

Vous pouvez utiliser la commande « az vm update » pour mettre à jour les machines virtuelles existantes. Pour les machines virtuelles RHEL, exécutez la commande avec un paramètre --license-type « RHEL_BYOS ». Pour les machines virtuelles SLES, exécutez la commande avec un paramètre --license-type « SLES_BYOS ».

#### <a name="cli-example-to-enable-the-benefit"></a>Exemple de CLI pour activer l’avantage :
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
#### <a name="cli-example-to-disable-the-benefit"></a>Exemple de CLI pour désactiver l’avantage :
Pour désactiver l’avantage, utilisez une valeur licence-type « None ».
```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

#### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>Exemple CLI pour activer l’avantage sur un grand nombre de machines virtuelles
Pour activer l’avantage sur un grand nombre de machines virtuelles, vous pouvez utiliser le paramètre `--ids` dans Azure CLI.

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file containing a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

Les exemples suivants illustrent deux méthodes d’obtention d’une liste d’ID de ressource : l’un au niveau du groupe de ressources, l’autre au niveau de l’abonnement.
```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="check-ahb-status-of-a-vm"></a>Vérifier l’état AHB d’une machine virtuelle
Vous pouvez afficher l’état AHB d’une machine virtuelle de deux façons : en utilisant Azure CLI ou en utilisant Azure Instance Metadata Service Azure (Azure IMDS).


### <a name="azure-cli"></a>Azure CLI

La commande `az vm get-instance-view` peut être utilisée à cet effet. Recherchez un champ licenseType dans la réponse. Si le champ licenseType existe et que la valeur est « RHEL_BYOS » ou « SLES_BYOS », cela signifie que l’avantage est activé sur votre machine virtuelle.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

À partir de la machine virtuelle, vous pouvez interroger les métadonnées attestées IMDS pour déterminer le licenseType de la machine virtuelle. La valeur licenseType « RHEL_BYOS » ou « SLES_BYOS » indique que l’avantage est activé sur votre machine virtuelle. En savoir plus sur les métadonnées attestées [ici](./instance-metadata-service.md#attested-data)

## <a name="compliance"></a>Conformité

### <a name="red-hat"></a>Red Hat

Les clients qui utilisent Azure Hybrid Benefit pour RHEL acceptent les [conditions légales](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) et la [déclaration de confidentialité](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf) standard associés aux offres RHEL de la Place de marché Azure.

Les clients utilisant Azure Hybrid Benefit pour RHEL peuvent fournir des mises à jour logicielles et des correctifs à ces machines virtuelles de trois façons :

1.  [Red Hat Update Infrastructure (RHUI)](../workloads/redhat/redhat-rhui.md) (option par défaut)
1.  Red Hat Satellite Server
1.  Red Hat Subscription Manager

Les clients qui choisissent l’option RHUI peuvent continuer à utiliser RHUI en tant que source de mise à jour principale pour leurs machines virtuelles RHEL AHB sans attacher d’abonnements RHEL à ces machines virtuelles.  Les clients qui choisissent l’option RHUI doivent veiller à la conformité de l’abonnement RHEL.

Les clients qui choisissent Red Hat Satellite Server ou Red Hat Subscription Manager doivent supprimer la configuration de RHUI, puis attacher un abonnement RHEL avec Cloud Access à leurs machines virtuelles RHEL AHB.  

Vous trouverez des informations supplémentaires sur la conformité de l’abonnement Red Hat, les mises à jour logicielles et les sources pour les machines virtuelles RHEL AHB [ici](https://access.redhat.com/articles/5419341).

### <a name="suse"></a>SUSE

Pour utiliser Azure Hybrid Benefit pour vos machines virtuelles SLES, vous devez d’abord être inscrit au programme de cloud public SUSE. Apprenez-en davantage sur le programme ici. Après avoir acheté des abonnements SUSE, vous devez inscrire vos machines virtuelles à l’aide de ces abonnements auprès de votre propre source de mises à jour à l’aide du Centre de clients SUSE, du serveur d’outil de gestion des abonnements ou du gestionnaire SUSE.

## <a name="frequently-asked-questions"></a>Forum aux questions
*Q : Puis-je utiliser un type de licence « RHEL_BYOS » avec une image SLES ou inversement ?*

A : Non, vous ne pouvez pas. Toute tentative d’entrer un type de licence ne correspondant pas à la distribution en cours d’exécution sur votre machine virtuelle ne met pas à jour les métadonnées de facturation. Toutefois, si vous entrez accidentellement un type de licence incorrect, la mise à jour de votre machine virtuelle vers le type de licence qui convient activera l’avantage.

*Q : Je me suis inscrit au programme Red Hat Cloud Access, mais je ne peux toujours pas activer l’avantage sur mes machines virtuelles RHEL. Que faire ?*

A : L’inscription au programme Red Hat Cloud Access peut mettre un certain temps à se propager entre Red Hat et Azure. Si l’erreur persiste après un jour ouvrable, contactez le support technique de Microsoft.

*Q : J’ai déployé une machine virtuelle à l’aide d’une image de référence RHEL BYOS. Puis-je convertir la facturation de ces images de BYOS à PAYG ?*

A : Non, vous ne pouvez pas. Azure Hybrid Benefit prend en charge la conversion uniquement sur les images avec Paiement à l'utilisation.

*Q : J’ai déployé une machine virtuelle à l’aide d’une image de référence RHEL BYOS. Puis-je convertir la facturation de ces images de BYOS à PAYG ?*

A : Non, vous ne pouvez pas. Azure Hybrid Benefit prend en charge la conversion uniquement sur les images avec Paiement à l'utilisation.

*Q : J’ai chargé ma propre image RHEL à partir d’un dossier local (via ASR ou autre) vers Azure. Puis-je convertir la facturation de ces images de BYOS à PAYG ?*

A : Non, vous ne pouvez pas. La fonctionnalité Azure Hybrid Benefit est actuellement disponible uniquement pour les images de la Place de marché RHEL et SLES. 

*Q : Puis-je utiliser Azure Hybrid Benefit sur des machines virtuelles déployées à partir d’images SAP de la Place de marché RHEL et SLES ?*

R : Oui, c’est possible. Vous pouvez utiliser le type de licence « RHEL_BYOS » pour les machines virtuelles RHEL et « SLES_BYOS » afin de convertir des machines virtuelles déployées à partir d’images SAP de la Place de marché RHEL et SLES.

*Q : Puis-je utiliser Azure Hybrid Benefit sur un groupe de machines virtuelles identiques (VMSS) pour RHEL et SLES ?*

A : Non, vous ne pouvez pas. Les VMSS ne figurent actuellement pas dans l’étendue d’Azure Hybrid Benefit pour RHEL et SLES.

*Q : Puis-je utiliser Azure Hybrid Benefit sur des instances réservées (RI) pour RHEL et SLES ?*

A : Non, vous ne pouvez pas. Les RI ne figurent actuellement pas dans l’étendue d’Azure Hybrid Benefit pour RHEL et SLES.

*Q : Puis-je utiliser Azure Hybrid Benefit sur une machine virtuelle déployée pour SQL Server sur des images RHEL ?*

A : Non, vous ne pouvez pas. Aucune prise en charge de ces éléments n’est prévue.
 

## <a name="common-issues"></a>Problèmes courants
Cette section contient une liste des problèmes courants susceptibles de se produire et des procédures d’atténuation.

| Error | Limitation des risques |
| ----- | ---------- |
| « Impossible de terminer l’action, car les informations dont nous disposons indique que vous n’avez pas réussi à activer Red Hat Cloud Access sur votre abonnement Azure... » | Pour pouvoir utiliser l’avantage avec des machines virtuelles RHEL, vous devez d’abord inscrire vos abonnements Azure auprès de Red Hat Cloud Access. Visitez ce lien pour en savoir plus sur l’inscription de vos abonnements Azure pour Red Hat Cloud Access

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment créer et mettre à jour des machines virtuelles et ajouter des types de licences (RHEL_BYOS, SLES_BYOS) pour Azure Hybrid Benefit avec [Azure CLI ici.](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest&preserve-view=true)
