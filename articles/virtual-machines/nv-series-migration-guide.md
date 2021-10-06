---
title: Guide de migration de la série NV
description: Guide de migration de la série NV
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: 3f37a9c14a66156b908eb1d046f98301add07c20
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128562380"
---
# <a name="nv-series-migration-guide"></a>Guide de migration de la série NV

À mesure que des tailles de machine virtuelle avec des GPU plus puissantes deviennent disponibles dans les centres de données Azure, évaluez vos charges de travail et de migrer les machines virtuelles des séries NV et NV_Promo. Ces machines virtuelles héritées peuvent être migrées vers de nouvelles séries de machines virtuelles, comme NVsv3 et NVasv4, pour de meilleures performances avec un coût réduit. La série de machines virtuelles NVsv3 est dotée de GPU Nvidia M60. La série NVasv4 est dotée de GPU AMD Radeon instinct MI25.

Les principales différences entre les séries NV et NV_Promo et les nouvelles séries NVsv3 et NVasv4 sont les suivantes :
- Performances améliorées.
- Prise en charge du stockage Premium.
- Option permettant de choisir une taille de GPU fractionnaire pour les configurations à plusieurs GPU.

Les séries NVsv3 et NVasv4 ont des cœurs plus modernes et une plus grande capacité.

La section suivante résume la différence entre la série NV héritée et les séries NVsv3 et NVv4.
 
 ## <a name="nvsv3-series"></a>Série NVsv3 

Les machines virtuelles de la série NVv3 sont dotées de GPU NVIDIA Tesla M60 et de la technologie NVIDIA GRID avec des processeurs Intel E5-2690 v4 (Broadwell) et de la technologie Hyper-Threading d’Intel. Ces machines virtuelles sont destinées aux applications graphiques accélérées par GPU et aux bureaux virtuels sur lesquels les clients souhaitent :
- visualiser leurs données ;
- simuler les résultats à afficher ;
- travailler sur CAD ;
- afficher et diffuser du contenu en continu. 

Ces machines virtuelles peuvent également exécuter des charges de travail de précision unique, telles que le codage et le rendu.

Les machines virtuelles NVv3 prennent en charge le stockage Premium et offrent une mémoire RAM deux fois supérieure à celle de la série NV. Pour obtenir les spécifications les plus récentes, consultez [Tailles de machines virtuelles de calcul accéléré par GPU : série NVsv3](nvv3-series.md)

| Taille actuelle de machine virtuelle | Taille de machine virtuelle cible | Différence de spécification  |
|---|---|---|
|Standard_NV6 <br> Standard_NV6_Promo |Standard_NV12s_v3  | Processeurs virtuels : 12 (+6) <br> Mémoire : 112 Gio (+56) <br> Stockage temporaire (SSD) en Gio : 320 (-20) <br> Disques de données max. : 12 (-12) <br> Accélération réseau : Oui <br> Stockage Premium : Oui  |
|Standard_NV12 <br> Standard_NV12_Promo |Standard_NV24s_v3  | Processeurs virtuels : 24 (+12) <br>Mémoire : 224 Gio (+112) <br>Stockage temporaire (SSD) en Gio : 640 (-40)<br>Disques de données max. : 24 (-24)<br>Accélération réseau : Oui <br>Stockage Premium : Oui   |
|Standard_NV24 <br> Standard_NV24_Promo |Standard_NV48s_v3  | Processeurs virtuels : 48 (+24) <br>Mémoire : 448 Gio (+224) <br>Stockage temporaire (SSD) en Gio : 1280 (-160) <br>Nombre maximal de disques de données : 32 (-32) <br>Accélération réseau : Oui <br>Stockage Premium : Oui   |

## <a name="nvv4-series"></a>Série NVv4 

Les machines virtuelles de la série NVv4 sont dotées de GPU AMD Radeon Instinct MI25 et de processeurs AMD EPYC 7V12 (Rome). Avec la série NVv4, Azure introduit des machines virtuelles avec des GPU partiels. Choisissez la machine virtuelle à la bonne taille pour les applications graphiques accélérées par GPU depuis un huitième d’un GPU avec une mémoire tampon de trame de 2 Gio jusqu’à un GPU complet avec une mémoire tampon de trame de 16 Gio.

Les machines virtuelles NVv4 prennent actuellement en charge uniquement le système d’exploitation invité Windows. Pour obtenir les spécifications les plus récentes, consultez [Tailles de machines virtuelles de calcul accéléré par GPU : série NVv4](nvv4-series.md)

| Taille actuelle de machine virtuelle | Taille de machine virtuelle cible | Différence de spécification  |
|---|---|---|
|Standard_NV6 <br> Standard_NV6_Promo |Standard_NV16as_v4  | Processeurs virtuels : 16 (+10) <br>Mémoire : 56 Gio  <br>Stockage temporaire (SSD) en Gio : 352 (+12) <br>Disques de données max. : 16 (-8) <br>Accélération réseau : Oui <br>Stockage Premium : Oui   |
|Standard_NV12 <br> Standard_NV12_Promo |Standard_NV32as_v4  | Processeurs virtuels : 32 (+20) <br>Mémoire : 112 Gio <br>Stockage temporaire (SSD) en Gio : 704 (+24) <br>Disques de données max. : 32 (+16)<br>Accélération réseau : Oui <br>Stockage Premium : Oui   |
|Standard_NV24 <br> Standard_NV24_Promo |N/A  | N/A  |

## <a name="migration-steps-for-general-changes"></a>Étapes de migration pour les modifications générales

Pour gérer les modifications générales :

1. Choisissez une série et une taille pour la migration. 

1. Obtenez le quota de la série de machines virtuelles cible.

1. Redimensionnez la taille actuelle de machine virtuelle de la série NV à la taille cible.

  Si la taille cible est NVv4, veillez à supprimer le pilote GPU NVIDIA et à installer le pilote GPU AMD.

## <a name="migration-steps-for-breaking-changes"></a>Étapes de la migration pour les changements cassants

Pour gérer les changements cassants, suivez les étapes décrites dans les sections suivantes.

### <a name="select-a-target-size-for-migration"></a>Sélectionner une taille cible pour la migration

Après avoir évalué votre utilisation actuelle, déterminez le type de machine virtuelle avec GPU dont vous avez besoin. En fonction des impératifs liés à la charge de travail, vous avez différents choix. Voici comment choisir :

- Si la charge de travail est de type graphique ou visualisation, et dépend fortement de l’utilisation du GPU Nvidia, migrez vers la série NVsv3.
- Si la charge de travail est de type graphique ou visualisation, et ne dépend pas fortement de l’utilisation d’un type de GPU spécifique, migrez vers la série NVsv3 ou la série NVVasv4.
 
> [!Note]
>Parmi les bonnes pratiques, il est recommandé de sélectionner une taille de machine virtuelle en fonction du coût et des performances. Les recommandations de cet article sont basées sur une comparaison un-à-un des métriques de performances pour les tailles NV et NV_Promo, et la correspondance la plus proche dans une autre série de machines virtuelles.
>Avant de choisir la taille appropriée, effectuez une comparaison des coûts à l’aide de la calculatrice de prix Azure.

### <a name="get-a-quota-for-the-target-vm-family"></a>Obtenir un quota pour la famille de machines virtuelles cible 

Suivez ce guide pour [demander une augmentation du quota de processeurs virtuels par famille de machines virtuelles](../azure-portal/supportability/per-vm-quota-requests.md). Sélectionnez la série NVSv3 ou la série NVv4 comme nom de la famille de machines virtuelles en fonction de la taille de machine virtuelle cible que vous avez sélectionnée pour la migration.

### <a name="resize-the-current-vm"></a>Redimensionner la machine virtuelle actuelle

Vous pouvez [redimensionner la machine virtuelle](resize-vm.md).

## <a name="faq"></a>Forum aux questions
**Q :** Quel pilote GPU dois-je utiliser pour la taille de machine virtuelle cible ? 

**R :** Pour la série NVsv3, utilisez le [pilote Nvidia GRID](./windows/n-series-driver-setup.md). Pour NVv4, utilisez les [pilotes GPU AMD](./windows/n-series-amd-driver-setup.md). 

**Q :** J’utilise actuellement l’extension de pilote GPU Nvidia. Fonctionnera-t-il pour la taille de machine virtuelle cible ? 

**R :** L’[extension de pilote Nvidia](./extensions/hpccompute-gpu-windows.md) actuelle fonctionnera avec NVsv3. Utilisez les [extensions de pilote GPU AMD](./extensions/hpccompute-amd-gpu-windows.md) si la taille de machine virtuelle cible est NVv4. 
 
**Q :** Quelle série de machines virtuelles cible dois-je utiliser en cas de dépendance envers CUDA ? 

 **R :** NVv3 prend en charge CUDA. La série de machines virtuelles NVv4 avec les GPU AMD ne prend pas en charge CUDA.
