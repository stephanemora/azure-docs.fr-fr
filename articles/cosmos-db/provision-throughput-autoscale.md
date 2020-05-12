---
title: Créer des conteneurs et des bases de données Azure Cosmos avec débit approvisionné de mise à l’échelle automatique.
description: Découvrez les avantages et les cas d’usage et apprenez à approvisionner des bases de données et des conteneurs Azure Cosmos en débit approvisionné de mise à l’échelle automatique.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 81a13dcb7955a7d46f485416bf9b7e4e7be4d9ac
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791712"
---
# <a name="create-azure-cosmos-containers-and-databases-with-autoscale-provisioned-throughput"></a>Créer des conteneurs et des bases de données Azure Cosmos en débit approvisionné de mise à l’échelle automatique

Azure Cosmos DB vous permet de configurer vos conteneurs avec un débit approvisionné standard (manuel) ou un débit approvisionné par mise à l’échelle automatique. Cet article décrit les avantages et les cas d’usage du mode de mise à l’échelle automatique.

> [!NOTE]
> Vous pouvez [activer la mise à l’échelle automatique pour les nouveaux conteneurs et les nouvelles bases de données](#create-db-container-autoscale) uniquement. Il n’est pas disponible pour les conteneurs et bases de données existants.

En plus de l’approvisionnement standard du débit, vous pouvez désormais configurer des conteneurs Azure Cosmos en débit approvisionné de mise à l’échelle automatique. Les conteneurs et les bases de données configurés en débit approvisionné de mise à l’échelle automatique **effectueront automatiquement et immédiatement la mise à l’échelle du débit approvisionné en fonction des besoins de votre application, sans incidence sur la disponibilité, la latence, le débit ou les performances de la charge de travail de manière globale.**

Lors de la configuration de conteneurs et de bases de données avec mise à l’échelle automatique, vous devez spécifier le débit maximal `Tmax` à ne pas dépasser. Les conteneurs peuvent ensuite mettre à l’échelle leur débit afin que `0.1*Tmax < T < Tmax`. En d’autres termes, les conteneurs et les bases de données évoluent instantanément en fonction des besoins de la charge de travail, passant de 10 % de la valeur de débit maximal que vous avez configurée à la valeur de débit maximal configurée. Après la configuration de la mise à l’échelle automatique, vous pouvez modifier le paramètre de débit maximal (`Tmax`) sur une base de données ou un conteneur à tout moment. Avec l’option de mise à l’échelle automatique, le débit minimal de 400 RU/s par conteneur ou base de données n’est plus applicable.

Le débit maximal spécifié sur le conteneur ou la base de données permet au système d’être utilisé dans la limite de stockage calculée. Si la limite de stockage est dépassée, le débit maximal est automatiquement ajusté à une valeur supérieure. Lorsque vous utilisez le débit du niveau de la base de données avec mise à l’échelle automatique, le nombre de conteneurs autorisés dans une base de données est calculé comme suit : `0.001*TMax`. Par exemple, si vous approvisionnez 20 000 RU/s en mise à l’échelle automatique, la base de données peut comporter 20 conteneurs.

## <a name="benefits-of-autoscale-provisioned-throughput"></a><a id="autoscale-benefits"></a> Avantages du débit approvisionné avec mise à l’échelle automatique

Les conteneurs Azure Cosmos configurés avec mise à l’échelle automatique présentent les avantages suivants :

* **Simple :** avec mise à l’échelle automatique, la complexité liée à la gestion manuelle du débit approvisionné (en unités de requête) et de la capacité des différents conteneurs disparaît.

* **Évolutifs :** avec mise à l’échelle automatique, les conteneurs font l’objet d’une mise à l’échelle transparente de la capacité de débit approvisionné en fonction des besoins. Les connexions client et les applications ne sont pas interrompues, et il n'y a aucun impact sur les contrats de niveau de service existants.

* **Économiques :** lorsque vous utilisez des conteneurs configurés avec mise à l’échelle automatique, vous payez uniquement pour les ressources dont vos charges de travail ont besoin sur une base horaire.

* **Hautement disponibles :** avec mise à l’échelle automatique, les conteneurs utilisent le même backend distribué à l’échelle mondiale, à tolérance de pannes et à haute disponibilité afin de garantir la durabilité et la haute disponibilité des données.

## <a name="use-cases-of-autoscale-provisioned-throughput"></a><a id="autoscale-usecases"></a> Cas d’usage du débit approvisionné avec mise à l’échelle automatique

Les cas d’usage des conteneurs Azure Cosmos configurés en mode de mise à l’échelle automatique peuvent être les suivants :

* **Charges de travail variables :** Lorsque vous exécutez une application peu utilisée avec un pic d’utilisation d’une heure à plusieurs heures chaque jour ou plusieurs fois par an. Cela concerne par exemple les applications de ressources humaines, de budgétisation et de création de rapports opérationnels. Dans de tels scénarios, il est possible d’utiliser les conteneurs configurés avec mise à l’échelle automatique et vous n’avez plus besoin de configurer manuellement la capacité maximale ou moyenne.

* **Charges de travail imprévisibles :** lorsque vous exécutez des charges de travail pour lesquelles la base de données est utilisée tout au long de la journée, mais avec également des pics d'activité difficiles à prévoir. Cela peut par exemple concerner un site dédié aux conditions de circulation dont l'activité augmente brusquement lorsque les prévisions météorologiques changent. Les conteneurs configurés avec mise à l’échelle automatique augmentent leur capacité pour répondre aux besoins du pic de charge de l’application et la réduisent lorsque le pic d’activité est passé.

* **Nouvelles applications :** lorsque vous déployez une nouvelle application sans connaître le nombre d'unités de requête (débit approvisionné) dont vous avez besoin. Les conteneurs configurés avec mise à l’échelle automatique permettent une mise à l’échelle automatique en fonction des besoins en capacité et des exigences de votre application.

* **Applications peu utilisées :** lorsque vous disposez d'une application qui n'est utilisée que quelques heures plusieurs fois par jour, par semaine ou par mois, comme une application à faible volume, un site web ou un blog.

* **Bases de données de développement et de test :** Si certains de vos développeurs utilisent des conteneurs pendant les heures de travail, mais n’en ont pas besoin à la nuit ou pendant les week-ends. La configuration des conteneurs avec mise à l’échelle automatique permet de réduire leur capacité au minimum lorsqu’ils ne sont pas utilisés.

* **Charges de travail/requêtes de production planifiées :** si vous disposez d'une série de demandes/opérations/requêtes planifiées sur un même conteneur et que vous souhaitez réduire le débit au strict minimum pendant les périodes d'inactivité, vous pouvez désormais facilement le faire. Lorsqu’une requête/demande planifiée est transmise à un conteneur configuré avec mise à l’échelle automatique, elle effectue automatiquement un scale-up en fonction des besoins pour exécuter l’opération.

Les solutions aux problèmes précédents requièrent non seulement un temps considérable en termes d'implémentation, mais elles compliquent également la configuration ou votre code, et une intervention manuelle est souvent nécessaire pour remédier à ces problèmes. La mise à l’échelle automatique active les scénarios ci-dessus prêts à l’emploi, afin que vous n’ayez plus à vous soucier de ces problèmes.

## <a name="comparison--standard-manual-vs-autoscale-provisioned-throughput"></a>Comparaison : débit approvisionné standard (manuel) contre débit avec mise à l’échelle automatique

|  | Conteneurs configurés avec débit approvisionné standard  | Conteneurs configurés avec débit approvisionné avec mise à l’échelle automatique |
|---------|---------|---------|
| **Débit approvisionné** | Approvisionné manuellement. | Mise à l’échelle automatique et instantanée en fonction des modèles d’utilisation de la charge de travail. |
| **Limitation du nombre de demandes/opérations (429)**  | Peut intervenir si la consommation dépasse la capacité approvisionnée. | Ne se produira pas si le débit consommé est compris dans le débit maximal que vous choisissez avec la mise à l’échelle automatique.   |
| **Planification de la capacité** |  Vous devez procéder à une planification initiale de la capacité et approvisionner le débit dont vous avez besoin. |    Vous n'avez pas à vous soucier de la planification de la capacité. Le système prend automatiquement en charge la planification et la gestion de la capacité. |
| **Tarification** | Approvisionnement manuel en unités de requête (RU) par heure. | Pour les comptes à une seule région d’écriture, le débit utilisé est facturé sur une base horaire, en utilisant le taux de RU/s par heure du mode de mise à l’échelle automatique. <br/><br/>Pour les comptes à plusieurs régions d’écriture, aucuns frais supplémentaires ne s’appliquent au mode de mise à l’échelle automatique. Vous payez le débit utilisé sur une base horaire en utilisant le même taux de RU multimaîtres par heure. |
| **Idéal pour les types de charges de travail** |  Charges de travail prévisibles et stables|   Charges de travail imprévisibles et variables  |

## <a name="create-a-database-or-a-container-with-autoscale"></a><a id="create-db-container-autoscale"></a> Créer une base de données ou un conteneur avec mise à l’échelle automatique

Vous pouvez configurer la mise à l’échelle automatique pour les nouvelles bases de données ou les nouveaux conteneurs lors de leur création via le Portail Azure. Procédez comme suit pour créer une base de données ou un conteneur, activer la mise à l’échelle automatique et spécifier le débit maximal (RU/s).

1. Connectez-vous au [Portail Azure](https://portal.azure.com) ou à l’ [explorateur Azure Cosmos DB.](https://cosmos.azure.com/)

1. Accédez à votre compte Azure Cosmos DB et ouvrez l’onglet **Explorateur de données**.

1. Sélectionnez **Nouveau conteneur**. Entrez un nom pour votre base de données, conteneur, ainsi qu'une clé de partition. Sous **Débit**, sélectionnez l’option **Mise à l’échelle automatique** et choisissez le débit maximal (RU/s) que la base de données ou le conteneur ne peut pas dépasser lors de l’utilisation de l’option de mise à l’échelle automatique.

   ![Création d’un conteneur et configuration du débit de mise à l’échelle automatique](./media/provision-throughput-autoscale/create-container-autoscale-mode.png)

1. Sélectionnez **OK**.

Pour créer une base de données à débit partagé avec la mise à l’échelle automatique en sélectionnant l’option **Approvisionner le débit d’une base de données**.

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a> Limites de débit et de stockage pour la mise à l’échelle automatique

Le tableau suivant indique les limites maximales de débit et de stockage pour les différentes options avec mise à l’échelle automatique :

|Limite de débit maximale  |Limite de stockage maximale  |
|---------|---------|
|4 000 RU/s  |   50 Go    |
|20 000 RU/s  |  200 Go  |
|100 000 RU/s    |  1 To   |
|500 000 RU/s    |  5 To  |

## <a name="next-steps"></a>Étapes suivantes

* Examinez le [FAQ sur la mise à l’échelle automatique](autoscale-faq.md).
* En savoir plus sur les [partitions logiques](partition-data.md).
* Découvrez comment [approvisionner le débit sur un conteneur Azure Cosmos](how-to-provision-container-throughput.md).
* Découvrez comment [approvisionner le débit sur une base de données Azure Cosmos](how-to-provision-database-throughput.md).
