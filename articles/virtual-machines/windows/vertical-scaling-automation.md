---
title: Utiliser Azure Automation pour mettre à l’échelle verticalement des machines virtuelles Windows | Microsoft Docs
description: Mettre à l’échelle verticalement une machine virtuelle Windows en réponse à des alertes de surveillance avec Azure Automation
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4f964713-fb67-4bcc-8246-3431452ddf7d
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/18/2019
ms.author: kasing
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a7cccd36c619e58b8dedb9a52e70c478dc7b857c
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707918"
---
# <a name="vertically-scale-windows-vms-with-azure-automation"></a>Mettre à l’échelle verticalement des machines virtuelles Windows avec Azure Automation

La mise à l’échelle verticale consiste à augmenter ou à réduire les ressources d’un ordinateur en réponse à la charge de travail. Dans Azure, cette opération est possible en modifiant la taille de la machine virtuelle. Cela peut vous aider dans les scénarios suivants

* Si la machine virtuelle n’est pas souvent utilisée, vous pouvez la réduire pour réduire les coûts mensuels
* Si la machine virtuelle présente un pic d’utilisation, sa taille peut être augmentée pour augmenter sa capacité maximale

Les grandes lignes des étapes sont présentées ci-dessous.

1. Configuration d’Azure Automation pour accéder à vos machines virtuelles
2. Importer les runbooks de mise à l’échelle verticale Azure Automation dans votre abonnement
3. Ajouter un webhook à votre runbook
4. Ajouter une alerte à votre machine virtuelle

## <a name="scale-limitations"></a>Limitations relatives à la mise à l’échelle

En raison de la taille de la première machine virtuelle, les différentes tailles de mise à l’échelle peuvent être limitées en fonction de la disponibilité des autres tailles dans le cluster dans lequel la machine virtuelle actuelle est déployée. Dans les runbooks publiés et utilisés dans cet article, nous nous chargeons de ce cas et appliquons uniquement la mise à l’échelle dans les paires de machines virtuelles suivantes. Cela signifie qu’une machine virtuelle Standard_D1v2 ne peut pas de suite être convertie en Standard_G5 ou Basic_A0. Le scale-up/scale-down des tailles de machines virtuelles contraintes n’est pas pris en charge non plus. 

Vous pouvez choisir d’effectuer une mise à l’échelle entre les paires de tailles suivantes :

* [Série A](#a-series)
* [Série B](#b-series)
* [Série D](#d-series)
* [Série E](#e-series)
* [Série F](#f-series)
* [Série G](#g-series)
* [Série H](#h-series)
* [Série L](#l-series)
* [Série M](#m-series)
* [Série N](#n-series)

### <a name="a-series"></a>Série A

| Taille initiale | Taille du scale up | 
| --- | --- |
| Basic_A0 | Basic_A1 |
| Basic_A1 | Basic_A2 |
| Basic_A2 | Basic_A3 |
| Basic_A3 | Basic_A4 |
| Standard_A0 | Standard_A1 |
| Standard_A1 | Standard_A2 |
| Standard_A2 | Standard_A3 |
| Standard_A3 | Standard_A4 |
| Standard_A5 | Standard_A6 |
| Standard_A6 | Standard_A7 |
| Standard_A8 | Standard_A9 |
| Standard_A10 | Standard_A11 |
| Standard_A1_v2 | Standard_A2_v2 |
| Standard_A2_v2 | Standard_A4_v2 |
| Standard_A4_v2 | Standard_A8_v2 |
| Standard_A2m_v2 | Standard_A4m_v2 |
| Standard_A4m_v2 | Standard_A8m_v2 |

### <a name="b-series"></a>Série B

| Taille initiale | Taille du scale up | 
| --- | --- |
| Standard_B1s | Standard_B2s |
| Standard_B1ms | Standard_B2ms |
| Standard_B2ms | Standard_B4ms |
| Standard_B4ms | Standard_B8ms |

### <a name="d-series"></a>Série D

| Taille initiale | Taille du scale up | 
| --- | --- |
| D1 standard | D2 standard |
| D2 standard | D3 standard |
| D3 standard | D4 standard |
| D11 standard | D12 standard |
| D12 standard | D13 standard |
| D13 standard | D14 standard |
| Standard_DS1 | Standard_DS2 |
| Standard_DS2 | Standard_DS3 |
| Standard_DS3 | Standard_DS4 |
| Standard_DS11 | Standard_DS12 |
| Standard_DS12 | Standard_DS13 |
| Standard_DS13 | Standard_DS14 |
| Standard_D1_v2 | Standard_D2_v2 |
| Standard_D2_v2 | Standard_D3_v2 |
| Standard_D3_v2 | Standard_D4_v2 |
| Standard_D4_v2 | Standard_D5_v2 |
| Standard_D11_v2 | Standard_D12_v2 |
| Standard_D12_v2 | Standard_D13_v2 |
| Standard_D13_v2 | Standard_D14_v2 |
| Standard_DS1_v2 | Standard_DS2_v2 |
| Standard_DS2_v2 | Standard_DS3_v2 |
| Standard_DS3_v2 | Standard_DS4_v2 |
| Standard_DS4_v2 | Standard_DS5_v2 |
| Standard_DS11_v2 | Standard_DS12_v2 |
| Standard_DS12_v2 | Standard_DS13_v2 |
| Standard_DS13_v2 | Standard_DS14_v2 |
| Standard_D2_v3 | Standard_D4_v3 |
| Standard_D4_v3 | Standard_D8_v3 |
| Standard_D8_v3 | Standard_D16_v3 |
| Standard_D16_v3 | Standard_D32_v3 |
| Standard_D32_v3 | Standard_D64_v3 |
| Standard_D2s_v3 | Standard_D4s_v3 |
| Standard_D4s_v3 | Standard_D8s_v3 |
| Standard_D8s_v3 | Standard_D16s_v3 |
| Standard_D16s_v3 | Standard_D32s_v3 |
| Standard_D32s_v3 | Standard_D64s_v3 |
| Standard_DC2s | Standard_DC4s |

### <a name="e-series"></a>Série E

| Taille initiale | Taille du scale up | 
| --- | --- |
| Standard_E2_v3 | Standard_E4_v3 |
| Standard_E4_v3 | Standard_E8_v3 |
| Standard_E8_v3 | Standard_E16_v3 |
| Standard_E16_v3 | Standard_E20_v3 |
| Standard_E20_v3 | Standard_E32_v3 |
| Standard_E32_v3 | Standard_E64_v3 |
| Standard_E2s_v3 | Standard_E4s_v3 |
| Standard_E4s_v3 | Standard_E8s_v3 |
| Standard_E8s_v3 | Standard_E16s_v3 |
| Standard_E16s_v3 | Standard_E20s_v3 |
| Standard_E20s_v3 | Standard_E32s_v3 |
| Standard_E32s_v3 | Standard_E64s_v3 |

### <a name="f-series"></a>Série F

| Taille initiale | Taille du scale up | 
| --- | --- |
| Standard_F1 | Standard_F2 |
| Standard_F2 | Standard_F4 |
| Standard_F4 | Standard_F8 |
| Standard_F8 | Standard_F16 |
| Standard_F1s | Standard_F2s |
| Standard_F2s | Standard_F4s |
| Standard_F4s | Standard_F8s |
| Standard_F8s | Standard_F16s |
| Standard_F2s_v2 | Standard_F4s_v2 |
| Standard_F4s_v2 | Standard_F8s_v2 |
| Standard_F8s_v2 | Standard_F16s_v2 |
| Standard_F16s_v2 | Standard_F32s_v2 |
| Standard_F32s_v2 | Standard_F64s_v2 |
| Standard_F64s_v2 | Standard_F7s_v2 |

### <a name="g-series"></a>Série G

| Taille initiale | Taille du scale up | 
| --- | --- |
| Standard_G1 | Standard_G2 |
| Standard_G2 | Standard_G3 |
| Standard_G3 | Standard_G4 |
| Standard_G4 | Standard_G5 |
| Standard_GS1 | Standard_GS2 |
| Standard_GS2 | Standard_GS3 |
| Standard_GS3 | Standard_GS4 |
| Standard_GS4 | Standard_GS5 |

### <a name="h-series"></a>Série H

| Taille initiale | Taille du scale up | 
| --- | --- |
| Standard_H8 | Standard_H16 |
| Standard_H8m | Standard_H16m |

### <a name="l-series"></a>Série L

| Taille initiale | Taille du scale up | 
| --- | --- |
| Standard_L4s | Standard_L8s |
| Standard_L8s | Standard_L16s |
| Standard_L16s | Standard_L32s |
| Standard_L8s_v2 | Standard_L16s_v2 |
| Standard_L16s_v2 | Standard_L32s_v2 |
| Standard_L32s_v2 | Standard_L64s_v2 |
| Standard_L64s_v2 | Standard_L80s_v2 |

### <a name="m-series"></a>Série M

| Taille initiale | Taille du scale up | 
| --- | --- |
| Standard_M8ms | Standard_M16ms |
| Standard_M16ms | Standard_M32ms |
| Standard_M32ms | Standard_M64ms |
| Standard_M64ms | Standard_M128ms |
| Standard_M32ls | Standard_M64ls |
| Standard_M64s | Standard_M128s |
| Standard_M64 | Standard_M128 |
| Standard_M64m | Standard_M128m |

### <a name="n-series"></a>Série N

| Taille initiale | Taille du scale up | 
| --- | --- |
| Standard_NC6 | Standard_NC12 |
| Standard_NC12 | Standard_NC24 |
| Standard_NC6s_v2 | Standard_NC12s_v2 |
| Standard_NC12s_v2 | Standard_NC24s_v2 |
| Standard_NC6s_v3 | Standard_NC12s_v3 |
| Standard_NC12s_v3 | Standard_NC24s_v3 |
| Standard_ND6 | Standard_ND12 |
| Standard_ND12 | Standard_ND24 |
| Standard_NV6 | Standard_NV12 |
| Standard_NV12 | Standard_NV24 |
| Standard_NV6s_v2 | Standard_NV12s_v2 |
| Standard_NV12s_v2 | Standard_NV24s_v2 |

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Configuration d’Azure Automation pour accéder à vos machines virtuelles
La première chose à faire est de créer un compte Azure Automation qui hébergera les runbooks utilisés pour mettre à l’échelle une machine virtuelle. Depuis peu, le service Automation dispose de la fonctionnalité « Compte d'identification » qui facilite la configuration du Principal du service permettant d'exécuter automatiquement les runbooks au nom de l’utilisateur de façon très simple. Pour en savoir plus à ce sujet, consultez l’article ci-dessous :

* [Authentifier des Runbooks avec un compte d’identification Azure](../../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importer les runbooks de mise à l’échelle verticale Azure Automation dans votre abonnement
Les runbooks nécessaires à la mise à l’échelle verticale de votre machine virtuelle sont déjà publiés dans la galerie de runbooks Azure Automation. Vous devez les importer dans votre abonnement. Vous pouvez apprendre à importer des runbooks dans l’article suivant.

* [Galeries de runbooks et de modules pour Azure Automation](../../automation/automation-runbook-gallery.md)

Les runbooks qui doivent être importés sont affichés dans l’image ci-dessous

![Importer des runbooks](./media/vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Ajouter un webhook à votre runbook
Une fois que vous avez importé les runbooks, vous devrez ajouter un webhook au runbook afin qu’il puisse être déclenché par une alerte émanant d’une machine virtuelle. Les détails de la création d’un webhook pour votre runbook se trouvent ici

* [Webhooks Azure Automation](../../automation/automation-webhooks.md)

Veillez à bien copier le webhook avant de fermer la boîte de dialogue, car vous en aurez besoin dans la section suivante.

## <a name="add-an-alert-to-your-virtual-machine"></a>Ajouter une alerte à votre machine virtuelle
1. Sélectionnez les paramètres des machines virtuelles
2. Sélectionnez Règles d’alerte
3. Sélectionnez Ajouter une alerte
4. Sélectionnez une valeur pour déclencher l’alerte
5. Sélectionnez une condition, qui une fois remplie, va déclencher l’alerte
6. Sélectionnez un seuil pour la condition de l’étape 5. à remplir
7. Sélectionnez une période sur laquelle le service d’analyse vérifie l’état de la condition et du seuil dans les étapes 5 et 6
8. Collez le webhook que vous avez copié dans la section précédente.

![Ajouter une alerte à la machine virtuelle 1](./media/vertical-scaling-automation/add-alert-webhook-1.png)

![Ajouter une alerte à la machine virtuelle 2](./media/vertical-scaling-automation/add-alert-webhook-2.png)

