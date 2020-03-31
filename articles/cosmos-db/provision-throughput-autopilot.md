---
title: Créer des conteneurs et des bases de données Azure Cosmos en mode Autopilot
description: Découvrez les avantages et les cas d'usage, et apprenez à approvisionner des bases de données et des conteneurs Azure Cosmos en mode Autopilot.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 89af30788fe5129cddc6a3607b8c722549b610d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79225677"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autopilot-mode-preview"></a>Créer des conteneurs et des bases de données Azure Cosmos en mode Autopilot (Préversion)

Azure Cosmos DB vous permet d'approvisionner le débit de vos conteneurs en mode manuel ou Autopilot. Cet article décrit les avantages et les cas d'usage du mode Autopilot.

> [!NOTE]
> Le mode Autopilot est actuellement disponible en préversion publique. Vous pouvez [activer Autopilot pour les nouveaux conteneurs et bases de données](#create-a-database-or-a-container-with-autopilot-mode) uniquement. Il n’est pas disponible pour les conteneurs et bases de données existants.

En plus de l’approvisionnement manuel du débit, vous pouvez désormais configurer des conteneurs Azur  Cosmos en mode Autopilot. Les conteneurs et les bases de données configurés en mode Autopilot effectueront  **automatiquement et immédiatement la mise à l’échelle du débit approvisionné en fonction des besoins de votre application, sans incidence sur la disponibilité, la latence, le débit ou les performances de la charge de travail de manière globale.**

Lors de la configuration de conteneurs et de bases de données en mode Autopilot, vous devez spécifier le débit maximal `Tmax` à ne pas dépasser. Les conteneurs peuvent ensuite mettre à l’échelle leur débit afin que `0.1*Tmax < T < Tmax`. En d’autres termes, les conteneurs et les bases de données évoluent instantanément en fonction des besoins de la charge de travail, passant de 10 % de la valeur de débit maximal que vous avez configurée à la valeur de débit maximal configurée. Vous pouvez modifier le paramètre de débit maximal (`Tmax`) sur une base de données ou un conteneur en mode Autopilot à tout moment. Avec l’option Autopilot, le débit minimal de 400 RU/s par conteneur ou base de données n’est plus applicable.

Pendant la préversion de la fonctionnalité Autopilot, le débit maximal spécifié sur le conteneur ou la base de données permet au système d’être utilisé dans la limite de stockage calculée. Si la limite de stockage est dépassée, le débit maximal est automatiquement ajusté à une valeur supérieure. Lorsque vous utilisez le débit du niveau de la base de données avec le mode Autopilot, le nombre de conteneurs autorisés dans une base de données est calculé comme suit : `0.001*TMax`. Par exemple, si vous provisionnez 20 000 unités de requête par seconde en mode Autopilot, la base de données peut comporter 20 conteneurs.

## <a name="benefits-of-autopilot-mode"></a>Avantages du mode Autopilot

Les conteneurs Azure Cosmos configurés en mode Autopilot présentent les avantages suivants :

* **Simple :** en mode Autopilot, la complexité liée à la gestion manuelle du débit approvisionné (en unités de requête) et de la capacité des différents conteneurs disparaît.

* **Évolutifs :** en mode Autopilot, les conteneurs font l'objet d'une mise à l'échelle transparente de la capacité de débit approvisionnée en fonction des besoins. Les connexions client et les applications ne sont pas interrompues, et il n'y a aucun impact sur les contrats de niveau de service existants.

* **Économiques :** Lorsque vous utilisez des conteneurs configurés en mode Autopilot, vous payez uniquement pour les ressources dont vos charges de travail ont besoin sur une base horaire.

* **Hautement disponibles :** En mode Autopilot, les conteneurs utilisent le même backend,distribué à échelle mondiale, à tolérance de pannes et à haute disponibilité, pour garantir la durabilité et la haute disponibilité des données.

## <a name="use-cases-of-autopilot-mode"></a>Cas d'usage du mode Autopilot

Les cas d'usage des conteneurs Azure Cosmos configurés en mode Autopilot peuvent être les suivants :

* **Charges de travail variables :** Lorsque vous exécutez une application peu utilisée avec un pic d’utilisation d’une heure à plusieurs heures chaque jour ou plusieurs fois par an. Cela concerne par exemple les applications de ressources humaines, de budgétisation et de création de rapports opérationnels. Dans de tels scénarios, il est possible d’utiliser les conteneurs configurés en mode Autopilot et vous n’avez plus besoin de configurer manuellement les capacités de pointe ou moyennes.

* **Charges de travail imprévisibles :** lorsque vous exécutez des charges de travail pour lesquelles la base de données est utilisée tout au long de la journée, mais avec également des pics d'activité difficiles à prévoir. Cela peut par exemple concerner un site dédié aux conditions de circulation dont l'activité augmente brusquement lorsque les prévisions météorologiques changent. Les conteneurs configurés en mode Autopilot augmentent leur capacité pour répondre aux besoins de la charge de pointe de l'application et la réduisent lorsque le pic d'activité est passé.

* **Nouvelles applications :** lorsque vous déployez une nouvelle application sans connaître le nombre d'unités de requête (débit approvisionné) dont vous avez besoin. Les conteneurs configurés en mode Autopilot permettent une mise à l'échelle automatique en fonction des besoins en capacité et des exigences de votre application.

* **Applications peu utilisées :** lorsque vous disposez d'une application qui n'est utilisée que quelques heures plusieurs fois par jour, par semaine ou par mois, comme une application à faible volume, un site web ou un blog.

* **Bases de données de développement et de test :** Si certains de vos développeurs utilisent des conteneurs pendant les heures de travail, mais n’en ont pas besoin à la nuit ou pendant les week-ends. Avec les conteneurs configurés en mode Autopilot, ils sont réduits au minimum lorsqu’ils ne sont pas utilisés.

* **Charges de travail/requêtes de production planifiées :** si vous disposez d'une série de demandes/opérations/requêtes planifiées sur un même conteneur et que vous souhaitez réduire le débit au strict minimum pendant les périodes d'inactivité, vous pouvez désormais facilement le faire. Lorsqu'une requête/demande planifiée est transmise à un conteneur configuré en mode Autopilot, elle est automatiquement mise à l'échelle en fonction des besoins pour exécuter l'opération.

Les solutions aux problèmes précédents requièrent non seulement un temps considérable en termes d'implémentation, mais elles compliquent également la configuration ou votre code, et une intervention manuelle est souvent nécessaire pour remédier à ces problèmes. Le mode Autopilot active les scénarios ci-dessus prêts à l’emploi, afin que vous n’ayez plus à vous soucier de ces problèmes.

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>Comparaison : conteneurs configurés en mode manuel ou en mode Autopilot

|  | Conteneurs configurés en mode manuel  | Conteneurs configurés en mode Autopilot |
|---------|---------|---------|
| **Débit approvisionné** | Approvisionné manuellement. | Mise à l’échelle automatique et instantanée en fonction des modèles d’utilisation de la charge de travail. |
| **Limitation du nombre de demandes/opérations (429)**  | Peut intervenir si la consommation dépasse la capacité approvisionnée. | Ne se produira pas si le débit consommé est compris dans le débit maximal que vous choisissez avec le mode Autopilot.   |
| **Planification de la capacité** |  Vous devez procéder à une planification initiale de la capacité et approvisionner le débit dont vous avez besoin. |    Vous n'avez pas à vous soucier de la planification de la capacité. Le système prend automatiquement en charge la planification et la gestion de la capacité. |
| **Tarification** | Approvisionnement manuel en unités de requête (RU) par heure. | Pour les comptes à une seule région d'écriture, le débit utilisé est facturé sur une base horaire, en utilisant le taux d'unités de requête par heure du mode Autopilot. <br/><br/>Pour les comptes à plusieurs régions d'écriture, aucuns frais supplémentaires ne s'appliquent au mode Autopilot. Vous payez le débit utilisé sur une base horaire en utilisant le même taux de RU multimaîtres par heure. |
| **Idéal pour les types de charges de travail** |  Charges de travail prévisibles et stables|   Charges de travail imprévisibles et variables  |

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>Créer une base de données ou un conteneur en mode Autopilot

Vous pouvez configurer les bases de données ou les conteneurs en mode Autopilot lors de leur création via le portail Azure. Procédez comme suit pour créer une base de données ou un conteneur, activer le mode Autopilot et spécifier le débit maximal (RU/s).

1. Connectez-vous au [Portail Azure](https://portal.azure.com) ou à l’ [explorateur Azure Cosmos DB.](https://cosmos.azure.com/)

1. Accédez à votre compte Azure Cosmos DB et ouvrez l’onglet **Explorateur de données**.

1. Sélectionnez **Nouveau conteneur**. Entrez un nom pour votre base de données, conteneur, ainsi qu'une clé de partition. Sous **débit**, sélectionnez l’option **Autopilot** et choisissez le débit maximal (RU/s) que la base de données ou que le conteneur ne peut pas dépasser lors de l’utilisation de l’option Autopilot.

   ![Création d’un conteneur et configuration du débit Autopilot](./media/provision-throughput-autopilot/create-container-autopilot-mode.png)

1. Sélectionnez **OK**.

Pour créer une base de données à débit partagé avec le mode Autopilot, sélectionnez l'option **Approvisionner le débit d’une base de données**.

## <a name="throughput-and-storage-limits-for-autopilot"></a><a id="autopilot-limits"></a> Limites de débit et de stockage pour AutoPilot

Le tableau suivant indique les limites de débit et de stockage pour AutoPilot pour les différentes options en mode AutoPilot :

|Limite de débit maximale  |Limite de stockage maximale  |
|---------|---------|
|4 000 RU/s  |   50 Go    |
|20 000 RU/s  |  200 Go  |
|100 000 RU/s    |  1 To   |
|500 000 RU/s    |  5 To  |

## <a name="next-steps"></a>Étapes suivantes

* Passez en revue le FAQ [Autopilot](autopilot-faq.md).
* En savoir plus sur les [partitions logiques](partition-data.md).
* Découvrez comment [approvisionner le débit sur un conteneur Azure Cosmos](how-to-provision-container-throughput.md).
* Découvrez comment [approvisionner le débit sur une base de données Azure Cosmos](how-to-provision-database-throughput.md).
