---
author: manish-shukla01
ms.author: cwatson
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11-20-2018
ms.openlocfilehash: 044f735560eb7d57a90a16c1e3ce1255a9fe0d54
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742190"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Flexibilité en termes de taille de machine virtuelle avec des instances de machines virtuelles réservées

Avec une instance de machine virtuelle réservée optimisée pour la flexibilité de la taille de l’instance, la réservation que vous achetez peut s’appliquer aux tailles de machine virtuelle d’un même groupe de séries de taille. Par exemple, si vous achetez une réservation pour une taille de machine virtuelle qui est répertoriée dans la table de la série DSv2, comme Standard_DS5_v2, la remise sur réservation peut s’appliquer aux quatre autres tailles répertoriées dans cette même table :

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Mais cette remise sur réservation ne s’applique pas aux tailles de machine virtuelle qui figurent dans des tables différentes, comme ce qui figure dans la table de mémoire haute de la série DSv2 : Standard_DS11_v2, Standard_DS12_v2, etc.

Dans le groupe de séries de taille, le nombre de machines virtuelles auxquelles la remise sur réservation s’applique dépend de la taille de machine virtuelle que vous choisissez lorsque vous achetez une réservation. Ce nombre dépend également des tailles des machines virtuelles que vous utilisez. La colonne de ratio indiquée dans les tableaux suivants compare l’empreinte relative de chaque taille de machine virtuelle de ce groupe. Utilisez la valeur de ratio pour calculer la façon dont la remise sur réservation s’applique aux machines virtuelles vous utilisez.

## <a name="examples"></a>Exemples

Les exemples suivants utilisent les tailles et ratios de la table de la série DSv2.

 Vous achetez une instance de machine virtuelle réservée avec la taille Standard_DS4_v2, où le ratio ou l’empreinte relative par rapport aux autres tailles de cette série correspond à la valeur 8.

- Scénario 1 : Utiliser huit machines virtuelles de taille Standard_DS1_v2 avec un ratio de valeur 1. Votre remise sur réservation s’applique à ces huit machines virtuelles.
- Scénario 2 : Utiliser deux machines virtuelles de taille Standard_DS2_v2 avec chacune un ratio de valeur 2. Utiliser également une machine virtuelle de taille Standard_DS3_v2 avec un ratio de valeur 4. Empreinte totale : 2 + 2 + 4 = 8. Votre remise sur réservation s’applique à ces trois machines virtuelles.
- Scénario 3 : Utiliser une machine virtuelle de taille Standard_DS5_v2 avec un ratio de valeur 16. Votre remise sur réservation s’applique à la moitié du coût du calcul de cette machine virtuelle.

Les sections suivantes présentent les tailles qui figurent dans le même groupe de séries de taille lorsque vous achetez une instance de machine virtuelle réservée optimisée pour la flexibilité de taille d’instance.

## <a name="b-series"></a>Série B

| Taille | Ratio|
|---|---|
| Standard_B1s | 1 |
|Standard_B2s|4|

Pour plus d’informations, voir [Tailles de machines virtuelles modulables Série B](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="b-series-high-memory"></a>Série B mémoire haute

| Taille | Ratio|
|---|---|
| Standard_B1ms |1|
|Standard_B2ms|4|
|Standard_B4ms|8|
|Standard_B8ms|16|

Pour plus d’informations, voir [Tailles de machines virtuelles modulables Série B](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="d-series"></a>Série D

| Taille | Ratio|
|---|---|
| D1 standard|1|
|D2 standard|2|
|D3 standard|4|
|D4 standard|8|

Pour plus d’informations, voir [Tailles de machines virtuelles des générations précédentes](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="d-series-high-memory"></a>Série D mémoire haute

| Taille | Ratio|
|---|---|
| D11 standard|1|
|D12 standard|2|
|D13 standard|4|
|D14 standard|8|

Pour plus d’informations, voir [Tailles de machines virtuelles des générations précédentes](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series"></a>Série DS

| Taille | Ratio|
|---|---|
|Standard_DS1|1|
|Standard_DS2|2|
|Standard_DS3|4|
|Standard_DS4|8|

Pour plus d’informations, voir [Tailles de machines virtuelles des générations précédentes](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series-high-memory"></a>Série DS mémoire haute

| Taille | Ratio|
|---|---|
|Standard_DS11|1|
|Standard_DS12|2|
|Standard_DS13|4|
|Standard_DS14|8|

Pour plus d’informations, voir [Tailles de machines virtuelles des générations précédentes](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series"></a>Séries DSv2

| Taille | Ratio|
|---|---|
|Standard_DS1_v2|1|
|Standard_DS2_v2|2|
|Standard_DS3_v2|4|
|Standard_DS4_v2|8|
|Standard_DS5_v2|16|

Pour plus d’informations, voir [Tailles de machine virtuelle à usage général](../articles/virtual-machines/windows/sizes-general.md#dv2-series).

## <a name="dsv2-series-high-memory"></a>Série DSv2 mémoire haute

| Taille | Ratio|
|---|---|
|Standard_DS11_v2|1|
|Standard_DS11-1_v2|1|
|Standard_DS12_v2|2|
|Standard_DS12-1_v2|2|
|Standard_DS12-2_v2|2|
|Standard_DS13_v2|4|
|Standard_DS13-2_v2|4|
|Standard_DS13-4_v2|4|
|Standard_DS14_v2|8|
|Standard_DS14-4_v2|8|
|Standard_DS14-8_v2|8|
|Standard_DS15_v2|10|

Pour plus d’informations, voir [Tailles de machine virtuelle à mémoire optimisée](../articles/virtual-machines/windows/sizes-memory.md#dsv2-series-11-15).

## <a name="dsv3-series"></a>Série DSv3

| Taille | Ratio|
|---|---|
|Standard_D2s_v3|1|
|Standard_D4s_v3|2|
|Standard_D8s_v3|4|
|Standard_D16s_v3|8|
|Standard_D32s_v3|16|
|Standard_D64s_v3|32|

Pour plus d’informations, voir [Tailles de machine virtuelle à usage général](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-sup1sup).

## <a name="dv2-series"></a>Série Dv2

| Taille | Ratio|
|---|---|
|Standard_D1_v2|1|
|Standard_D2_v2|2|
|Standard_D3_v2|4|
|Standard_D4_v2|8|
|Standard_D5_v2|16|

Pour plus d’informations, voir [Tailles de machine virtuelle à usage général](../articles/virtual-machines/windows/sizes-general.md#dv2-series).

## <a name="dv2-series-high-memory"></a>Série Dv2 mémoire haute

| Taille | Ratio|
|---|---|
|Standard_D11_v2|1|
|Standard_D12_v2|2|
|Standard_D13_v2|4|
|Standard_D14_v2|8|
|Standard_D15_v2|10|

Pour plus d’informations, voir [Tailles de machine virtuelle à mémoire optimisée](../articles/virtual-machines/windows/sizes-memory.md#dv2-series-11-15).

## <a name="dv3-series"></a>Série Dv3

| Taille | Ratio|
|---|---|
| Standard_D2_v3|1|
|Standard_D4_v3|2|
|Standard_D8_v3|4|
|Standard_D16_v3|8|
|Standard_D32_v3|16|
|Standard_D64_v3|32|

Pour plus d’informations, voir [Tailles de machine virtuelle à usage général](../articles/virtual-machines/windows/sizes-general.md#dv3-series-sup1sup).

## <a name="esv3-series"></a>Série ESv3

| Taille | Ratio|
|---|---|
|Standard_E2s_v3|1|
|Standard_E4s_v3|2|
|Standard_E4-2s_v3|2|
|Standard_E8s_v3|4|
|Standard_E8-2s_v3|4|
|Standard_E8-4s_v3|4|
|Standard_E16s_v3|8|
|Standard_E16-4s_v3|8|
|Standard_E16-8s_v3|8|
|Standard_E32s_v3|16|
|Standard_E32-8s_v3|16|
|Standard_E32-16s_v3|16|
|Standard_E64s_v3|32|
|Standard_E64-16s_v3|32|
|Standard_E64-32s_v3|32|

Pour plus d’informations, voir [Tailles de machine virtuelle à mémoire optimisée](../articles/virtual-machines/windows/sizes-memory.md#esv3-series).

## <a name="ev3-series"></a>Série Ev3

| Taille | Ratio|
|---|---|
| Standard_E2_v3|1|
|Standard_E4_v3|2|
|Standard_E8_v3|4|
|Standard_E16_v3|8|
|Standard_E32_v3|16|
|Standard_E64_v3|32|

Pour plus d’informations, voir [Tailles de machine virtuelle à mémoire optimisée](../articles/virtual-machines/windows/sizes-memory.md#ev3-series).

## <a name="f-series"></a>Série F

| Taille | Ratio|
|---|---|
| Standard_F1|1|
|Standard_F2|2|
|Standard_F4|4|
|Standard_F8|8|
Standard_F16|16|

Pour plus d’informations, voir [Tailles de machine virtuelle à calcul optimisé](../articles/virtual-machines/windows/sizes-compute.md#f-series).

## <a name="fs-series"></a>Série FS

| Taille | Ratio|
|---|---|
| Standard_F1s|1|
|Standard_F2s|2|
|Standard_F4s|4|
|Standard_F8s|8|
|Standard_F16s|16|

Pour plus d’informations, voir [Tailles de machine virtuelle à calcul optimisé](../articles/virtual-machines/windows/sizes-compute.md#fs-series-sup1sup).

## <a name="fsv2-series"></a>Série Fsv2

| Taille | Ratio|
|---|---|
|Standard_F2s_v2|1|
|Standard_F4s_v2|2|
|Standard_F8s_v2|4|
|Standard_F16s_v2|8|
|Standard_F32s_v2|16|
|Standard_F64s_v2|32|
|Standard_F72s_v2|36|

Pour plus d’informations, voir [Tailles de machine virtuelle à calcul optimisé](../articles/virtual-machines/windows/sizes-compute.md#fsv2-series-sup1sup).

## <a name="h-series"></a>Série H

| Taille | Ratio|
|---|---|
| Standard_H8|1|
|Standard_H16|2|

Pour plus d’informations, consultez [Tailles de machine virtuelle de calcul haute performance](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="h-series-high-memory"></a>Série H mémoire haute

| Taille | Ratio|
|---|---|
| Standard_H8m|1|
|Standard_H16m|2|

Pour plus d’informations, consultez [Tailles de machine virtuelle de calcul haute performance](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="ls-series"></a>Série Ls

| Taille | Ratio|
|---|---|
| Standard_L4s|1|
|Standard_L8s|2|
|Standard_L16s|4|
|Standard_L32s|8|

Pour plus d’informations, voir [Tailles de machine virtuelle à stockage optimisé](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="m-series"></a>Série M

| Taille | Ratio|
|---|---|
| Standard_M64s|1|
|Standard_M128s|2|

Pour plus d’informations, voir [Tailles de machine virtuelle à mémoire optimisée](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional"></a>Série M mémoire fractionnée

| Taille | Ratio|
|---|---|
| Standard_M16s|1|
|Standard_M32s|2|

Pour plus d’informations, voir [Tailles de machine virtuelle à mémoire optimisée](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-high-memory"></a>Série M mémoire haute fractionnée

| Taille | Ratio|
|---|---|
|Standard_M8ms|1|
|Standard_M8-2ms|1|
|Standard_M8-4ms|1|
|Standard_M16ms|2|
|Standard_M16-4ms|2|
|Standard_M16-8ms|2|
|Standard_M32ms|4|
|Standard_M32-8ms|4|
|Standard_M32-16ms|4|

Pour plus d’informations, voir [Tailles de machine virtuelle à mémoire optimisée](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-large"></a>Série M grande mémoire fractionnée

| Taille | Ratio|
|---|---|
| Standard_M32ls|1|
|Standard_M64ls|2|

Pour plus d’informations, voir [Tailles de machine virtuelle à mémoire optimisée](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-high-memory"></a>Série M mémoire haute

| Taille | Ratio|
|---|---|
| Standard_M64ms|1|
|Standard_M64-16ms|1|
|Standard_M64-32ms|1|
|Standard_M128ms|2|
|Standard_M128-32ms|2|
|Standard_M128-64ms|2|

Pour plus d’informations, voir [Tailles de machine virtuelle à mémoire optimisée](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="nc-series"></a>Série NC

| Taille | Ratio|
|---|---|
| Standard_NC6|1|
|Standard_NC12|2|
|Standard_NC24|4|

Pour plus d’informations, voir [Tailles de machine virtuelle à GPU optimisé](../articles/virtual-machines/windows/sizes-gpu.md).

## <a name="ncv2-series"></a>Série NCv2

| Taille | Ratio|
|---|---|
| Standard_NC6s_v2|1|
|Standard_NC12s_v2|2|
|Standard_NC24s_v2|4|

Pour plus d’informations, voir [Tailles de machine virtuelle à GPU optimisé](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).

## <a name="ncv3-series"></a>Série NCv3

| Taille | Ratio|
|---|---|
| Standard_NC6s_v3|1|
|Standard_NC12s_v3|2|
|Standard_NC24s_v3|4|

Pour plus d’informations, voir [Tailles de machine virtuelle à GPU optimisé](../articles/virtual-machines/windows//sizes-gpu.md#ncv3-series).

## <a name="nd-series"></a>Série ND

| Taille | Ratio|
|---|---|
| Standard_ND6s|1|
|Standard_ND12s|2|
|Standard_ND24s|4|

Pour plus d’informations, voir [Tailles de machine virtuelle à GPU optimisé](../articles/virtual-machines/windows//sizes-gpu.md#nd-series).

## <a name="nv-series"></a>Série NV

| Taille | Ratio|
|---|---|
| Standard_NV6|1|
|Standard_NV12|2|
|Standard_NV24|4|

Pour plus d’informations, voir [Tailles de machine virtuelle à GPU optimisé](../articles/virtual-machines/windows//sizes-gpu.md#nv-series).


