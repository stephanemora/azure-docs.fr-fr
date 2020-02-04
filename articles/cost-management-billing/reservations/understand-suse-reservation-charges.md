---
title: Remise sur abonnement logiciel - Azure | Microsoft Docs
description: Découvrez comment les remises sur abonnement logiciel sont appliquées aux logiciels sur des machines virtuelles.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2019
ms.author: banders
ms.openlocfilehash: f3d0c123fcbd16f3a4125ad74f0502863b6ce6d1
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "75986661"
---
# <a name="azure-software-plan-discount"></a>Remise sur abonnement logiciel Azure

Les abonnements logiciels Azure pour SUSE et RedHat sont des réservations qui s'appliquent aux machines virtuelles déployées. La remise sur abonnement logiciel est appliquée aux logiciels utilisés sur les machines virtuelles déployées qui correspondent à la réservation.

Lorsque vous arrêtez une machine virtuelle, la remise est automatiquement appliquée à une autre machine virtuelle correspondante, si disponible. Un abonnement logiciel couvre le coût de l'exécution du logiciel sur une machine virtuelle. Les autres frais liés au calcul, au stockage et à la mise en réseau sont facturés séparément.

Pour acheter l'abonnement approprié, vous devez déterminer votre utilisation des machines virtuelles et le nombre de processeurs virtuels qu'elles contiennent. Utilisez les sections suivantes pour identifier l'abonnement à acheter en fonction de vos données d'utilisation.

## <a name="how-reservation-discount-is-applied"></a>Comment la remise de réservation est-elle appliquée ?

Une remise de réservation repose sur le principe de « *capacité utilisée ou perdue* ». Ainsi, si vous ne disposez pas des ressources correspondantes pour une heure donnée, vous perdez une quantité de réservation pour cette heure. Vous ne pouvez pas reporter les heures réservées inutilisées.

Lorsque vous arrêtez une ressource, la remise de réservation s'applique automatiquement à une autre ressource correspondante dans l'étendue spécifiée. Si aucune ressource correspondante n'est trouvée dans l'étendue spécifiée, les heures réservées sont *perdues*.

## <a name="review-redhat-vm-usage-before-you-buy"></a>Évaluer l'utilisation de la machine virtuelle Red Hat avant l'achat

Procurez-vous le nom du produit à partir de vos données d'utilisation et achetez l'abonnement RedHat correspondant au même type et à la même taille.

Par exemple, si vous utilisez le produit **Red Hat Enterprise Linux - Licence de machine virtuelle dotée de 1 à 4 processeurs virtuels**, vous devez acheter **Red Hat Enterprise Linux** pour **Machine virtuelle dotée de 1 à 4 processeurs virtuels**.

<!--ADD RHEL SCREENSHOT -->

## <a name="review-suse-vm-usage-before-you-buy"></a>Évaluer l'utilisation de la machine virtuelle SUSE avant l'achat

Procurez-vous le nom du produit à partir de vos données d'utilisation et achetez l'abonnement SUSE correspondant au même type et à la même taille.

Par exemple, si vous utilisez le produit **SUSE Linux Enterprise Server Priority - Support pour machine virtuelle dotée de 2 à 4 processeurs virtuels**, vous devez acheter **SUSE Linux Enterprise Server Priority** pour **2 à 4 processeurs virtuels**.

![Exemple de sélection du produit à acheter](./media/understand-suse-reservation-charges/select-suse-linux-enterprise-server-priority-2-4-vcpu.png)

## <a name="discount-applies-to-different-vm-sizes-for-suse-plans"></a>La remise s'applique à différentes tailles de machines virtuelles pour les abonnements SUSE

Tout comme les instances réservées de machine virtuelle, l’achat d’un abonnement SUSE offre une flexibilité de taille d’instance. Cela signifie que votre remise s’applique même quand vous déployez une machine virtuelle avec un nombre différent de processeurs virtuels. La remise s’applique aux différentes tailles de machine virtuelle au sein de l’abonnement logiciel.

Le montant de remise dépend du ratio indiqué dans les tableaux suivants. Le ratio évalue l’encombrement relatif de chaque compteur du groupe. Le ratio dépend des processeurs virtuels de la machine virtuelle. Utilisez la valeur du ratio pour calculer le nombre d’instances de machine virtuelle qui bénéficient de la remise de l’abonnement SUSE Linux.

Par exemple, si vous achetez un abonnement SUSE Linux Enterprise Server for HPC Priority pour une machine virtuelle avec 3 ou 4 processeurs virtuels, le ratio de cette réservation est 2. La remise couvre le coût du logiciel SUSE pour les configurations suivantes :

- 2 machines virtuelles déployées avec 1 ou 2 processeurs virtuels,
- 1 machine virtuelle déployée avec 3 ou 4 processeurs virtuels
- ou 0,77 (soit environ 77 %) d’une machine virtuelle avec au moins 5 processeurs virtuels.

Le ratio pour au moins 5 processeurs virtuels est 2,6. Par conséquent, une réservation de SUSE avec une machine virtuelle contenant au moins 5 processeurs virtuels couvre seulement une partie du coût du logiciel, en l’occurrence environ 77 %.

Les tableaux suivants indiquent les abonnements logiciels pour lesquels vous pouvez acheter une réservation, les compteurs d’utilisation associés et les ratios de chacun.

### <a name="suse-linux-enterprise-server-for-hpc-priority"></a>SUSE Linux Enterprise Server for HPC Priority

Nom dans la place de marché du portail Azure :

- SLES 12 SP3 for HPC (Priority)

|Machine virtuelle SUSE | ID du compteur| Ratio| Exemple de taille de machine virtuelle|
| -------| ------------------------| --- |--- |
|SLES for HPC 1-2 vCPUs|e275a668-ce79-44e2-a659-f43443265e98|1|D2s_v3|
|SLES for HPC 3-4 vCPUs|e531e1c0-09c9-4d83-b7d0-a2c6741faa22|2|D4s_v3|
|SLES for HPC 5+ vCPUs|4edcd5a5-8510-49a8-a9fc-c9721f501913|2.6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>SUSE Linux Enterprise Server for HPC Standard

Nom dans la place de marché du portail Azure :

- SLES 12 SP3 for HPC

|Machine virtuelle SUSE | ID du compteur | Ratio|Exemple de taille de machine virtuelle|
| ------- | --- | ------------------------| --- |
|SLES for HPC 1-2 vCPUs |8c94ad45-b93b-4772-aab1-ff92fcec6610|1|D2s_v3|
|SLES for HPC 3-4 vCPUs|4ed70d2d-e2bb-4dcd-b6fa-42da71861a1c|1,92308|D4s_v3|
|SLES for HPC 5+ vCPUs |907a85de-024f-4dd6-969c-347d47a1bdff|2,92308|D8s_v3|

### <a name="suse-linux-enterprise-server-for-sap-priority"></a>SUSE Linux Enterprise Server for SAP Priority

Noms dans la place de marché du portail Azure :

- SLES for SAP 15 (Priority)
- SLES for SAP 12 SP3 (Priority)
- SLES for SAP 12 SP2 (Priority)

|Machine virtuelle SUSE | ID du compteur | Ratio|Exemple de taille de machine virtuelle|
| ------- |------------------------| --- | --- |
|SLES for SAP Priority 1-2 vCPUs|497fe0b6-fa3c-4e3d-a66b-836097244142|1|D2s_v3|
|SLES for SAP Priority 3-4 vCPUs |847887de-68ce-4adc-8a33-7a3f4133312f|2|D4s_v3|
|SLES for SAP Priority 5+ vCPUs |18ae79cd-dfce-48c9-897b-ebd3053c6058|2,41176|D8s_v3|

### <a name="suse-linux-enterprise-server-priority"></a>SUSE Linux Enterprise Server Priority

Noms dans la place de marché du portail Azure :

- SLES 15 (PRIORITY)
- SLES 12 SP3 (Priority)
- SLES 11 SP4 (Priority)

|Machine virtuelle SUSE | ID du compteur | Ratio|Exemple de taille de machine virtuelle|
| ------- |------------------------| --- |--- |
|SLES 1 vCPU|462cd632-ec6b-4663-b79f-39715f4e8b38|1|B1ms|
|SLES 2-4 vCPUs |924bee71-5eb8-424f-83ed-a58823c33908|2|D4s_v3|
|SLES 2-4 vCPUs |60b3ae9d-e77a-46b2-9cdf-92fa87407969|2|D4s_v3|
|SLES 6 vCPUs |e8862232-6131-4dbe-bde4-e2ae383afc6f|3||
|SLES 8 vCPUs |e11331a8-fd32-4e71-b60e-4de2a818c67a|3.2|D8s_v3|
|SLES 12 core vCPUs |a5afd00d-d3ef-4bcd-8b42-f158b2799782|3.2||
|SLES 16 vCPUs |bb21066f-fe46-46d3-8006-b326b1663e52|3.2| D16s_v3|
|SLES 20 vCPUs |c5228804-1de6-4bd4-a61c-501d9003acc8|3.2| |
|SLES 24 cores vCPUs |-005d-4075-ac11-822ccde9e8f6|3.2| ND24s|
|SLES 32 vCPUs |180c1a0a-b0a5-4de3-a032-f92925a4bf90|3.2| D32s_v3|
|SLES 40 cores vCPUs |a161d3d3-0592-4956-9b64-6829678b6506|3.2||
|SLES 64 vCPUs |7f5a36ed-d5b5-4732-b6bb-837dbf0fb9d8|3.2| D64s_v3|
|SLES 72 cores vCPUs |93329a72-24d7-4faa-93d9-203f367ed334|3.2|F72s_v2|
|SLES 96 cores vCPUs |2018c3a8-ff13-41f8-b64d-9558c5206547|3.2||
|SLES 128 cores vCPUss |ac27e4d7-44b5-4fee-bc1a-78ac5b4abaf7|3.2| M128ms|

### <a name="suse-linux-enterprise-server-standard"></a>SUSE Linux Enterprise Server Standard

Noms dans la place de marché du portail Azure :

- SLES 15
- SLES 15 (Standard)
- SLES 12 SP3 (Standard)

|Machine virtuelle SUSE | ID du compteur | Ratio|Exemple de taille de machine virtuelle|
| ------- |------------------------| --- |--- |
|SLES 1-2 cores vCPUs |4b2fecfc-b110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|SLES 3-4 cores vCPUs |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1,92308|D4s_v3|
|SLES 5+ vCPUs |7b349b65-d906-42e5-833f-b2af38513468|2,30769| D8s_v3|

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les réservations, consultez les articles suivants :

- [Qu’est-ce qu’une réservation Azure ?](save-compute-costs-reservations.md)
- [Prépayer les abonnements logiciels SUSE avec les réservations Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Prépayer des machines virtuelles avec des instances de machines virtuelles réservées Azure](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gérer les réservations Azure](manage-reserved-vm-instance.md)
- [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](understand-reserved-instance-usage.md)
- [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](understand-reserved-instance-usage-ea.md)
