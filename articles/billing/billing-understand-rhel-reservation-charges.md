---
title: Comprendre la remise de plan de réservation de Red Hat et d’utilisation - Azure | Microsoft Docs
description: Découvrez comment les remises de plan de Red Hat sont appliquées aux logiciels de Red Hat sur les machines virtuelles.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2019
ms.author: cwatson
ms.openlocfilehash: 9eba59bcdc9974519fc1ed37078a205eff28f116
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58653083"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Comprendre comment la remise de plan de Red Hat Linux Enterprise logiciel réservation est appliquée pour Azure

Une fois que vous achetez un plan de Red Hat Linux, la remise est automatiquement appliquée aux Red Hat machines virtuelles (VM) qui correspondent à la réservation. Un plan de Red Hat Linux couvre le coût de l’exécution du logiciel de Red Hat sur une machine virtuelle Azure.

Pour acheter le plan de Red Hat Linux approprié, vous devez comprendre quelles machines virtuelles Hat de rouge vous exécutez et le nombre de processeurs virtuels sur ces machines virtuelles. Utilisez les sections suivantes pour vous aider à identifier l’abonnement à acheter à partir de votre fichier CSV d’utilisation.

## <a name="discount-applies-to-different-vm-sizes"></a>Remise s’applique aux différentes tailles de machine virtuelle

Comme des Instances de machine virtuelle réservées, achats de plan de Red Hat offrent la flexibilité de taille d’instance. Cela signifie que votre remise s’applique même quand vous déployez une machine virtuelle avec un nombre différent de processeurs virtuels. La remise s’applique aux différentes tailles de machine virtuelle au sein de l’abonnement logiciel.

Le montant de remise dépend du ratio indiqué dans les tableaux suivants. Le ratio évalue l’encombrement relatif de chaque compteur du groupe. Le ratio dépend des processeurs virtuels de la machine virtuelle. Utilisez la valeur de taux pour calculer le nombre d’instances de machine virtuelle bénéficier de la remise de plan de Red Hat Linux.

Par exemple, si vous achetez un plan pour Red Hat Linux Enterprise Server pour une machine virtuelle avec des processeurs virtuels 3 ou 4, le rapport pour cette réservation est 2. La remise couvre le coût du logiciel Red Hat :

- 2 machines virtuelles déployées avec 1 ou 2 processeurs virtuels,
- 1 machine virtuelle déployée avec 3 ou 4 processeurs virtuels
- ou 0,77 (soit environ 77 %) d’une machine virtuelle avec au moins 5 processeurs virtuels.

Le ratio pour au moins 5 processeurs virtuels est 2,6. Par conséquent, une réservation pour Red Hat avec une machine virtuelle avec 5 ou plus de processeurs virtuels couvre une seule partie du coût logiciel, qui est d’environ 77 %.

## <a name="understand-red-hat-vm-usage-before-you-buy"></a>Comprendre l’utilisation de la machine virtuelle Red Hat avant d’acheter

Les tableaux suivants indiquent les abonnements logiciels pour lesquels vous pouvez acheter une réservation, les compteurs d’utilisation associés et les ratios de chacun.

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Noms dans la place de marché du portail Azure :

- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 7 (dernière lvm)

|Machine virtuelle de Red Hat | ID du compteur| Ratio| Exemple de taille de machine virtuelle|
| -------| ------------------------| --- |--- |
|1-4 processeurs virtuels licence de machine virtuelle|077a07bb-20f8-4bc6-b596-ab7211a1e247|1|D4s_v3|
|1-4 processeurs virtuels licence de machine virtuelle|2f96d035-3bac-46d6-b2bc-c6daa0938536|1|D4s_v3|
|1-4 processeurs virtuels licence de machine virtuelle|4831a7b4-bdd4-48a2-8e95-18d053971ede|1|D4s_v3|
|processeurs virtuels 5 + licence de machine virtuelle|291b2cbc-6c34-4e2b-a4e4-1ff8c106f672|2.166666667|D8s_v3|
|processeurs virtuels 5 + licence de machine virtuelle|3b6661c4-03dd-45e7-88c9-512fcb7906d5|2.166666667|D8s_v3|
|processeurs virtuels 5 + licence de machine virtuelle|037eddc0-fedd-4d73-b5d8-92fba9edb831|2.166666667|D8s_v3|
|processeurs virtuels 5 + licence de machine virtuelle|432cdeee-4034-4ddf-9ba4-9250a19b0d5f|2.166666667|D8s_v3|
|processeurs virtuels 5 + licence de machine virtuelle|794dcb90-0793-43e6-9909-70d29974e56d|2.166666667|D8s_v3|
|processeurs virtuels 5 + licence de machine virtuelle|86b5b0b4-3c19-4720-82e9-874f8c58b48e|2.166666667|D8s_v3|
|processeurs virtuels 5 + licence de machine virtuelle|86c35ec3-0a48-426a-9625-22d80e6ea55b|2.166666667|D8s_v3|
|processeurs virtuels 5 + licence de machine virtuelle|8b698c7a-47f1-4cba-8ae1-9853d5ad562d|2.166666667|D8s_v3|
|processeurs virtuels 5 + licence de machine virtuelle|a4daffb4-96f4-4fc5-b1e6-fd3a2cf3595e|2.166666667|D8s_v3|
|processeurs virtuels 5 + licence de machine virtuelle|a838cfb1-0bd3-4965-84f0-663f49afc2e2|2.166666667|D8s_v3|
|processeurs virtuels 5 + licence de machine virtuelle|99aed7b9-a0a9-4783-b90c-be7c2f3c7e30|2.166666667|D8s_v3|
|processeurs virtuels 5 + licence de machine virtuelle|d09f877e-03b4-48b2-b11a-782b965cff19|2.166666667|D8s_v3|
|processeurs virtuels 44 licence de machine virtuelle|6f44ae85-a70e-44be-83ec-153a0bc23979|2.166666667||
|processeurs virtuels 60 licence de machine virtuelle|b9edcc5b-a429-4778-bc5a-82e7fa07fe55|2.166666667||

### <a name="red-hat-enterprise-linux-for-sap-with-ha"></a>Red Hat Enterprise Linux pour SAP avec haute disponibilité

Nom dans la place de marché du portail Azure :

|Machine virtuelle de Red Hat | ID du compteur | Ratio|Exemple de taille de machine virtuelle|
| ------- | --- | ------------------------| --- | --- |
|1-4 processeurs virtuels licence de machine virtuelle |4d902611-eed7-4060-a33e-3c7fdbac6406|1|D4s_v3|
|processeurs virtuels 5 + licence de machine virtuelle|6dfb482b-23ea-487f-810c-e66360f025de|2.333333333|D8s_v3|

### <a name="red-hat-enterprise-linux-with-ha"></a>Red Hat Enterprise Linux avec haute disponibilité

Noms dans la place de marché du portail Azure :

|Machine virtuelle de Red Hat | ID du compteur | Ratio|Exemple de taille de machine virtuelle|
| ------- |------------------------| --- | --- |
|1-4 processeurs virtuels licence de machine virtuelle|e9711132-d9d9-450c-8203-25cfc4bce8de|1|D4s_v3|
|processeurs virtuels 5 + licence de machine virtuelle|93954aa4-b55f-4b7b-844d-a119d6bf3c4e|2|D8s_v3|

### <a name="rhel-for-sap-business-applications"></a>RHEL for SAP Business Applications

Noms dans la place de marché du portail Azure :

- Red Hat Enterprise Linux 6.8 pour les applications métiers SAP
- Red Hat Enterprise Linux 7.3 pour les applications métiers SAP
- Red Hat Enterprise Linux 7.4 pour SAP
- Red Hat Enterprise Linux 7.5 pour SAP

|Machine virtuelle de Red Hat | ID du compteur | Ratio|Exemple de taille de machine virtuelle|
| ------- |------------------------| --- |--- |
|1 processeur virtuel licence de machine virtuelle|25889e91-c740-42ac-bc52-6b8f73b98575|1|D2s_v3|
|2 processeurs virtuels licence de machine virtuelle|2a0c92c8-23a7-4dc9-a39c-c4a73a85b5da|1|D2s_v3|
|4 processeurs virtuels licence de machine virtuelle|875898d3-3639-423c-82c1-38846281b7e8|1|D4s_v3|
|6 processeurs virtuels licence de machine virtuelle|69a140fa-e08e-415c-85f2-48158e4c73a0|2.166666667||
|8 processeurs virtuels licence de machine virtuelle|777a5a74-22d6-48c9-9705-ac38fe05a278|2.166666667|D8s_v3|
|12 processeurs virtuels licence de machine virtuelle|d6b8917a-5127-497a-9f48-1e959df98812|2.166666667||
|16 processeurs virtuels licence de machine virtuelle|03667e82-e009-425a-83f7-8ebddbca5af4|2.166666667|D16s_v3|
|processeurs virtuels 20 licences de machine virtuelle|bbd65e5b-35f1-42be-b86d-6625fbc1f1a4|2.166666667||
|24 processeurs virtuels licence de machine virtuelle|c2c07d3e-a7d0-400b-8832-b532bfd0be25|2.166666667|ND24s|
|32 processeurs virtuels licence de machine virtuelle|633d1494-5ec1-46f0-a742-eaf58eeaec7e|2.166666667|D32s_v3|
|processeurs virtuels 40 licence de machine virtuelle|737142c3-8e4f-4fc1-aa41-05b1661edff8|2.166666667||
|processeurs virtuels 44 licence de machine virtuelle|722bda73-a8c8-4d04-b96b-541f0bb6c0c4|2.166666667||
|processeurs virtuels 60 licence de machine virtuelle|a22bb342-ba9a-4529-a178-39a92ce770b6|2.166666667||
|64 processeurs virtuels licence de machine virtuelle|d37c8e17-e5f2-4060-881b-080dd4a8c4ce|2.166666667|64s_v3|
|72 processeurs virtuels licence de machine virtuelle|14341b96-e92c-4dca-ba66-322c88a79aa6|2.166666667|F72s_v2|
|processeurs virtuels 96 licence de machine virtuelle|8b2e5cb8-0362-4cbf-a30a-115e8d6dbc49|2.166666667||
|128 processeurs virtuels licence de machine virtuelle|9b198a68-974a-47a7-9013-49169ac0f2e9|2.166666667| M128ms|

### <a name="rhel-for-sap-hana"></a>RHEL for SAP HANA

Noms dans la place de marché du portail Azure :

- Red Hat Enterprise Linux 6.7 pour SAP HANA
- Red Hat Enterprise Linux 7.2 pour SAP HANA
- Red Hat Enterprise Linux 7.3 pour SAP HANA

|Machine virtuelle de Red Hat | ID du compteur | Ratio|Exemple de taille de machine virtuelle|
| ------- |------------------------| --- |--- |
|1 processeur virtuel licence de machine virtuelle|be0a59d1-eed7-47ec-becd-453267753793|1|D2s_v3|
|2 processeurs virtuels licence de machine virtuelle|3b97c9f5-f5d5-4fd3-a421-b78fca32a656|1|D2s_v3|
|4 processeurs virtuels licence de machine virtuelle|b39feb58-57bf-40f2-8193-f4fe9ac3dda3|1|D4s_v3|
|6 processeurs virtuels licence de machine virtuelle|a5963812-0f5a-4053-8ace-2b5babd15ed8|2.166666667||
|8 processeurs virtuels licence de machine virtuelle|5460ab4d-ce9a-46af-8ad5-ca5e53d715b5|2.166666667|D8s_v3|
|12 processeurs virtuels licence de machine virtuelle|0e3bc72d-a888-4bcf-8437-119f763a3215|2.166666667||
|16 processeurs virtuels licence de machine virtuelle|b40e95d8-3176-42f0-967c-497785c031b2|2.166666667|D16s_v3|
|processeurs virtuels 20 licences de machine virtuelle|81f34277-499d-40a3-a634-99adc08e2d45|2.166666667||
|24 processeurs virtuels licence de machine virtuelle|e03f1906-d35d-4084-b2cd-63281869c8ee|2.166666667|ND24s|
|32 processeurs virtuels licence de machine virtuelle|0a58c082-ceb8-4327-9b64-887c30dddb23|2.166666667|D32s_v3|
|processeurs virtuels 40 licence de machine virtuelle|a14225c0-04e6-4669-974f-e2ddd61a9c5b|2.166666667||
|processeurs virtuels 44 licence de machine virtuelle|378b8125-d8a5-4e09-99bc-c1462534ffb0|2.166666667||
|processeurs virtuels 60 licence de machine virtuelle|5d7db11a-54e9-404e-aaa8-509fac7c0638|2.166666667||
|64 processeurs virtuels licence de machine virtuelle|3c8157b2-a57d-45ce-ba02-bd86e9209795|2.166666667|64s_v3|
|72 processeurs virtuels licence de machine virtuelle|5e87a3ee-7afb-4040-b8d9-b109ddb38f31|2.166666667|F72s_v2|
|processeurs virtuels 96 licence de machine virtuelle|b13895fc-0d06-4de9-b860-627c471cd247|2.166666667||
|128 processeurs virtuels licence de machine virtuelle|6e67ac0b-19d3-4289-96df-05d0093d4b3b|2.166666667| M128ms|

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les réservations, consultez les articles suivants :

- [Qu’est-ce qu’une réservation Azure ?](billing-save-compute-costs-reservations.md)
- [Prépayer les plans de logiciels de Red Hat avec les réservations Azure](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Prépayer des machines virtuelles avec des instances de machines virtuelles réservées Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gérer les réservations pour Azure](billing-manage-reserved-vm-instance.md)
- [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
- [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).