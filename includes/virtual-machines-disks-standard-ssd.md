---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/14/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a7a4e4b487c324bada818d4815f253110f7f7a60
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234712"
---
# <a name="standard-ssd-managed-disks-for-azure-virtual-machine-workloads"></a>Disques managés SSD Standard pour les charges de travail des machines virtuelles Azure

Les disques managés SSD (Solid State Drives) Standard constituent une option de stockage économique optimisée pour les charges de travail nécessitant des performances cohérentes à des niveaux d’IOPS plus faibles. Les disques SSD Standard offrent une bonne expérience de niveau d’entrée pour ceux qui souhaitent migrer vers le cloud, en particulier si vous rencontrez des problèmes de variation avec les charges de travail exécutées sur vos solutions HDD locales. Les disques SSD Standard fournissent une meilleure disponibilité, cohérence, fiabilité et latence par rapport aux disques HDD et conviennent aux serveurs web, aux serveurs d’application faibles en E/S, aux applications d’entreprise peu utilisées et aux charges de travail de développement/test.

## <a name="standard-ssd-features"></a>Fonctionnalités des disques SSD Standard

**Disques managés** : Les disques SSD Standard sont seulement disponibles en tant que disques managés. Les disques non managés et les objets blob de pages ne sont pas pris en charge sur les disques SSD Standard. Quand vous créez un disque managé, vous spécifiez le type de disque SSD Standard et vous indiquez la taille du disque dont vous avez besoin, puis Azure crée et gère le disque pour vous.
Les disques SSD Standard prennent en charge toutes les opérations de gestion de service qu’offrent les disques managés. Par exemple, vous pouvez créer, copier ou capturer des disques managés SSD Standard comme vous le faites avec des disques managés.

**Machines virtuelles** : Les disques SSD Standard peuvent être utilisés avec toutes les machines virtuelles Azure, y compris les types de machine virtuelle qui ne prennent pas en charge les disques Premium. Par exemple, si vous utilisez une machine virtuelle de série A ou une machine virtuelle de série N ou de série DS, ou de n’importe quelle autre série de machine virtuelle Azure, vous pouvez utiliser des disques SSD Standard. Avec l’introduction des disques SSD Standard, nous permettons à un large éventail de charges de travail qui utilisaient des disques HDD d’effectuer une transition vers des disques SSD et de bénéficier de performances cohérentes, d’une disponibilité plus élevée, d’une meilleure latence et d’une meilleure expérience générale qu’offrent les disques SSD.

**Durabilité et disponibilité élevées** : Les disques SSD Standard reposent sur la même plateforme de disques Azure, qui fournit de façon constante une disponibilité et une durabilité élevées pour les disques. Les disques Azure sont conçus pour offrir une disponibilité de 99,999 pour cent. Comme tous les disques managés, les disques SSD Standard proposent aussi un stockage localement redondant (LRS). Avec le stockage LRS, la plateforme conserve plusieurs réplicas de données pour chaque disque et fournit de façon constante une durabilité de classe Entreprise pour les disques IaaS, avec un taux de défaillance annuel de ZÉRO pour cent, inégalé dans le secteur.

**Captures instantanées** : Comme tous les disques managés, les disques SSD Standard prennent également en charge la création de captures instantanées. Le type de capture instantanée peut être Standard (HDD) ou Premium (SSD). Pour réduire les coûts, nous vous recommandons le type de capture instantanée Standard (HDD) pour tous les types de disque Azure. En effet, quand vous créerez un disque managé à partir d’une capture instantanée, vous pourrez toujours choisir un niveau supérieur comme Standard SSD ou Premium SSD.

## <a name="scalability-and-performance-targets"></a>Objectifs de performance et de scalabilité

Le tableau suivant contient les tailles de disque qui sont actuellement proposées pour les disques SSD Standard.

|Type de disque SSD Standard  |Taille du disque  |IOPS par disque  |Débit par disque  |
|---------|---------|---------|---------|
|E10     |128 Go         |Jusqu’à 500         |Jusqu’à 60 Mio par seconde         |
|E15     |256 Gio         |Jusqu’à 500         |Jusqu’à 60 Mio par seconde         |
|E20     |512 Go         |Jusqu’à 500         |Jusqu’à 60 Mio par seconde         |
|E30     |1 024 Gio         |Jusqu’à 500         |Jusqu’à 60 Mio par seconde         |
|E40     |2 048 Gio         |Jusqu’à 500         |Jusqu’à 60 Mio par seconde         |
|E50     |4 095 Gio         |Jusqu’à 500         |Jusqu’à 60 Mio par seconde         |

Les disques SSD Standard sont conçus pour fournir des latences en millisecondes à un chiffre pour la plupart des opérations d’E/S et offrir les E/S et le débit dans les limites décrites dans le tableau ci-dessus. Les E/S et le débit réels peuvent parfois varier selon les modèles de trafic. Les disques SSD Standard offrent des performances plus cohérentes que les disques HDD avec une latence plus faible.

Par contre, les disques SSD Premium sont meilleurs que les disques SSD Standard, avec des latences faibles, des IOPS/débit élevés et une cohérence encore meilleure des performances de disque provisionné. Ce type de disque est recommandé pour les charge de travail de production critiques. Si votre charge de travail exige la prise en charge des disques hautes performances à faible latence, vous devez envisager d’utiliser le stockage Premium.

Comme les disques SSD Premium, les disques SSD Standard utilisent la taille d’unité d’E/S de 256 Kio. Si les données transférées sont inférieures à 256 Kio, elles sont considérées comme 1 unité d’E/S. Les tailles d’E/S supérieures sont divisées en plusieurs unités d’E/S de 256 Kio. Par exemple, une E/S de 1 100 Kio correspond à cinq unités d’E/S.

## <a name="pricing-and-billing"></a>Tarification et facturation

Lorsque vous utilisez des disques SSD Standard, les règles de facturation suivantes s’appliquent :

- Taille des disques managés
- Instantanés
- Transferts de données sortantes
- Transactions

**Taille des disques managés :** Les disques managés sont facturés selon la taille provisionnée. Azure fait correspondre la taille provisionnée (arrondie à la valeur supérieure) à l’offre de taille de disque la plus proche. Pour plus d’informations sur les tailles de disque proposées, consultez le tableau dans la section Objectifs de performance et de scalabilité ci-dessus. Chaque disque correspond à une taille de disque provisionnée prise en charge et est facturé en conséquence. Par exemple, si vous avez provisionné un disque SSD Standard de 200 Gio, celui-ci va correspondre à l’offre de taille de disque E15 (256 Gio). La facturation de n’importe quel disque provisionné est calculée au prorata horaire sur la base du tarif mensuel de l’offre de stockage Premium. Par exemple, si vous provisionnez un disque E10 et le supprimez au bout de 20 heures, l’offre E10 vous est facturée au prorata de 20 heures. Le montant facturé est indépendant de la quantité de données écrites sur le disque.

**Captures instantanées** : Les captures instantanées de disques managés sont facturées en fonction de la capacité utilisée par les captures instantanées, à la cible et à la source, le cas échéant. Pour plus d’informations sur les captures instantanées, consultez [Captures instantanées de disque managé](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#managed-disk-snapshots).

**Transferts de données sortantes**: les [transferts de données sortantes](https://azure.microsoft.com/pricing/details/bandwidth/) (données sortant des centres de données Azure) sont facturés en fonction de la bande passante utilisée.

**Transactions** : Tout comme les disques HDD Standard, les transactions sur les disques SSD Standard sont facturées. Les transactions comprennent les opérations de lecture et d’écriture sur le disque. La taille d’unité d’E/S utilisée pour comptabiliser les transactions sur disque SSD Standard est de 256 Kio. Les tailles d’E/S supérieures sont divisées en plusieurs unités d’E/S de 256 Kio.

Pour plus d’informations sur le prix des machines virtuelles et des disques managés, consultez :

- [Tarification des machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)
- [Tarification des disques managés](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la création de disques SSD Standard, reportez-vous à l’exemple qui montre comment créer une machine virtuelle avec plusieurs disques SSD Standard.

> [!div class="nextstepaction"]
> [Créer une machine virtuelle avec plusieurs disques SSD Standard](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/)