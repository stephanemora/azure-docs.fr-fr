---
title: Série DCsv2 – Machines virtuelles Microsoft Azure
description: Spécifications pour les machines virtuelles de la série DCsv2.
author: mmcrey
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: jushiman
ms.openlocfilehash: 0c5321625f9963a15527d8fb0edef73f0701bd6e
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129856382"
---
# <a name="dcsv2-series"></a>série DCsv2

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Les machines virtuelles de la série DCsv2 permettent de protéger la confidentialité et l’intégrité de vos données et de votre code lors de leur traitement dans le cloud public. La série DCsv2 s’appuie sur Intel® Software Guard Extensions, qui permet aux clients d’utiliser des enclaves sécurisées pour la protection.

Ces machines sont associées à des processeurs Intel® Xeon E-2288G (Coffee Lake) à 3,7 GHz avec la technologie SGX. Grâce à la technologie Intel® Turbo Boost Max 3.0, ces machines peuvent atteindre jusqu’à 5 GHz. 

[!NOTE] L’hyperthreading est désactivé pour plus de sécurité. La tarification est basée sur les performances supérieures des cœurs physiques par rapport aux vCores, ainsi que sur les capacités de sécurité uniques de la série DC.

Exemples de cas d’usage confidentiels : bases de données, blockchain, analyse de données multipartites, détection des fraudes, lutte contre le blanchiment d’argent, analyse de l’utilisation, analyse décisionnelle et apprentissage automatique.

## <a name="configuration"></a>Configuration

[Turbo Boost Max 3.0](https://www.intel.com/content/www/us/en/gaming/resources/turbo-boost.html) : pris en charge (la machine virtuelle du locataire indiquera 3,7 GHz, mais atteindra des vitesses Turbo).<br>
[Hyper-threading](https://www.intel.com/content/www/us/en/gaming/resources/hyper-threading.html) : non pris en charge<br>
[Stockage Premium](premium-storage-performance.md) : pris en charge (non pris en charge pour Standard_DC8_v2)<br>
[Mise en cache de Stockage Premium](premium-storage-performance.md) : pris en charge<br>
[Migration dynamique](maintenance-and-updates.md) : Non pris en charge<br>
[Mises à jour avec préservation de la mémoire](maintenance-and-updates.md) : Non pris en charge<br>
[Génération de machine virtuelle prise en charge](generation-2.md) : Génération 2<br>
[Performances réseau accélérées](../virtual-network/create-vm-accelerated-networking-cli.md) : Non pris en charge<br>
[Disques de système d’exploitation éphémères](ephemeral-os-disks.md) : Pris en charge <br>

## <a name="technical-specifications"></a>Spécifications techniques

| Taille             | Cœurs physiques | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Disques de données max. | Débit de stockage temporaire et mis en cache max. : IOPS / MBps (taille du cache en Gio) | Nombre max. de cartes réseau / Bande passante réseau attendue (Mbits/s) | Mémoire EPC (Mio) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|----------------------------------------------|---------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16                                                                                               | 2   | 28                                      |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32                                                                                               | 2  | 56                                          |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64                                                                                               | 2  | 112                                          |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128                                                                                         | 2   | 168                                         |


## <a name="get-started"></a>Bien démarrer

- Créer des machines virtuelles DCsv2 à l’aide du [Portail Azure](./linux/quick-create-portal.md) ou de la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview)
- Les machines virtuelles de la série DCsv2 sont des [machines virtuelles de 2e génération](./generation-2.md#creating-a-generation-2-vm) et ne prennent en charge que les images `Gen2`.
- Actuellement disponibles dans les régions listées sur la page [Produits Azure par région](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all).

## <a name="more-sizes-and-information"></a>Autres tailles et informations

- [Usage général](sizes-general.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Optimisé pour le GPU](sizes-gpu.md)
- [Calcul haute performance](sizes-hpc.md)
- [Générations précédentes](sizes-previous-gen.md)
- [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)
- [En savoir plus sur les types de disques](./disks-types.md#ultra-disk)

Calculatrice de prix : [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)

Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.
