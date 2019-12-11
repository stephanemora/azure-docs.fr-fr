---
title: Créer des conteneurs et des bases de données Azure Cosmos en mode Autopilot
description: Découvrez les avantages et les cas d'usage, et apprenez à approvisionner des bases de données et des conteneurs Azure Cosmos en mode Autopilot.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2259343d2c7bca1f60a5256efcd572e6cc21b565
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706048"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autopilot-mode-preview"></a>Créer des conteneurs et des bases de données Azure Cosmos en mode Autopilot (Préversion)

Azure Cosmos DB vous permet d'approvisionner le débit de vos conteneurs en mode manuel ou Autopilot. Cet article décrit les avantages et les cas d'usage du mode Autopilot.

> [!NOTE]
> Le mode Autopilot est actuellement disponible en préversion publique. Pour activer la fonctionnalité Autopilot sur votre compte Azure Cosmos, consultez la section [Activer Autopilot](#enable-autopilot) de cet article. Vous pouvez activer Autopilot uniquement pour les nouvelles bases de données et les conteneurs, elle n’est pas disponible pour les bases de données et les conteneurs existants.

En plus de l'approvisionnement manuel du débit, vous pouvez désormais configurer des conteneurs Azure Cosmos en mode Autopilot. Les conteneurs et bases de données Azure Cosmos configurés en mode Autopilot **procèdent automatiquement et instantanément à la mise à l'échelle du débit approvisionné en fonction des besoins de votre application, sans compromettre les contrats de niveau de service.**

Il n'est plus nécessaire de gérer manuellement le débit approvisionné ou les problèmes de limitation de débit. Les conteneurs Azure Cosmos configurés en mode Autopilot peuvent faire l'objet d'une mise à l'échelle instantanée pour répondre à la charge de travail sans aucun impact sur la disponibilité, la latence, le débit ou les performances de celle-ci. En cas d'utilisation intensive, les conteneurs Azure Cosmos configurés en mode Autopilot peuvent être mis à l'échelle sans affecter les opérations en cours.

Lors de la configuration de conteneurs et de bases de données en mode Autopilot, vous devez spécifier le débit maximal `Tmax` à ne pas dépasser. Les conteneurs peuvent ensuite faire l'objet d'une mise à l'échelle instantanée en fonction des besoins de la charge de travail dans la plage `0.1*Tmax < T < Tmax`. En d’autres termes, les conteneurs et les bases de données sont instantanément mis à l’échelle en fonction des besoins de la charge de travail, à partir de 10 % de la valeur de débit maximal que vous avez configurée, et jusqu’à la valeur maximale configurée. Vous pouvez à tout moment modifier le paramètre de débit maximal (Tmax) de la base de données ou du conteneur configurés en mode Autopilot. Avec l’option Autopilot, le débit minimal de 400 RU/s par conteneur ou base de données n’est plus applicable.

Pendant la préversion de la fonctionnalité Autopilot, le débit maximal spécifié sur le conteneur ou la base de données permet au système d’être utilisé dans la limite de stockage calculée. Si la limite de stockage est dépassée, le débit maximal est automatiquement ajusté à une valeur supérieure. Lorsque vous utilisez le débit du niveau de la base de données avec le mode Autopilot, le nombre de conteneurs autorisés dans une base de données est calculé comme suit : (0,001 * Débit max.). Par exemple, si vous provisionnez 20 000 unités de requête par seconde en mode Autopilot, la base de données peut comporter 20 conteneurs.

## <a name="benefits-of-autopilot-mode"></a>Avantages du mode Autopilot

Les conteneurs Azure Cosmos configurés en mode Autopilot présentent les avantages suivants :

* **Simple :** en mode Autopilot, la complexité liée à la gestion manuelle du débit approvisionné (en unités de requête) et de la capacité des différents conteneurs disparaît.

* **Évolutifs :** en mode Autopilot, les conteneurs font l'objet d'une mise à l'échelle transparente de la capacité de débit approvisionnée en fonction des besoins. Les connexions client et les applications ne sont pas interrompues, et il n'y a aucun impact sur les contrats de niveau de service existants.

* **Économiques :** lorsque vous utilisez des conteneurs Azure Cosmos configurés en mode Autopilot, vous payez uniquement les ressources dont vos charges de travail ont besoin sur une base horaire.

* **Hautement disponibles :** les conteneurs Azure Cosmos configurés en mode Autopilot utilisent le même back-end distribué à l'échelle mondiale, à tolérance de panne et à haute disponibilité pour garantir la durabilité des données et une haute disponibilité constante.

## <a name="use-cases-of-autopilot-mode"></a>Cas d'usage du mode Autopilot

Les cas d'usage des conteneurs Azure Cosmos configurés en mode Autopilot peuvent être les suivants :

* **Charges de travail variables :** lorsque vous exécutez une application peu utilisée avec un pic d'utilisation d'une à plusieurs heures à différents moments de la journée, ou plusieurs fois par an. Cela concerne par exemple les applications de ressources humaines, de budgétisation et de création de rapports opérationnels. Dans ce type de scénarios, des conteneurs configurés en mode Autopilot peuvent être utilisés, et il n'est plus nécessaire de configurer manuellement la capacité maximale ou moyenne.

* **Charges de travail imprévisibles :** lorsque vous exécutez des charges de travail pour lesquelles la base de données est utilisée tout au long de la journée, mais avec également des pics d'activité difficiles à prévoir. Cela peut par exemple concerner un site dédié aux conditions de circulation dont l'activité augmente brusquement lorsque les prévisions météorologiques changent. Les conteneurs configurés en mode Autopilot augmentent leur capacité pour répondre aux besoins de la charge de pointe de l'application et la réduisent lorsque le pic d'activité est passé.

* **Nouvelles applications :** lorsque vous déployez une nouvelle application sans connaître le nombre d'unités de requête (débit approvisionné) dont vous avez besoin. Les conteneurs configurés en mode Autopilot permettent une mise à l'échelle automatique en fonction des besoins en capacité et des exigences de votre application.

* **Applications peu utilisées :** lorsque vous disposez d'une application qui n'est utilisée que quelques heures plusieurs fois par jour, par semaine ou par mois, comme une application à faible volume, un site web ou un blog.

* **Bases de données de développement et de test :** les développeurs utilisent les comptes Azure Cosmos pendant les heures de travail, mais n'en ont pas besoin la nuit ou le week-end. Les conteneurs configurés en mode Autopilot permettent de réduire leur capacité au minimum lorsqu'ils ne sont pas utilisés.

* **Charges de travail/requêtes de production planifiées :** si vous disposez d'une série de demandes/opérations/requêtes planifiées sur un même conteneur et que vous souhaitez réduire le débit au strict minimum pendant les périodes d'inactivité, vous pouvez désormais facilement le faire. Lorsqu'une requête/demande planifiée est transmise à un conteneur configuré en mode Autopilot, elle est automatiquement mise à l'échelle en fonction des besoins pour exécuter l'opération.

Les solutions aux problèmes précédents requièrent non seulement un temps considérable en termes d'implémentation, mais elles compliquent également la configuration ou votre code, et une intervention manuelle est souvent nécessaire pour remédier à ces problèmes. Le mode Autopilot active les scénarios prêts à l'emploi ci-dessus afin que vous n'ayez plus à vous soucier de ces problèmes.

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>Comparaison : conteneurs configurés en mode manuel ou en mode Autopilot

|  | Conteneurs configurés en mode manuel  | Conteneurs configurés en mode Autopilot |
|---------|---------|---------|
| **Débit approvisionné** | Approvisionnement manuel | Mise à l’échelle automatique et instantanée en fonction des modèles d’utilisation de la charge de travail. |
| **Limitation du nombre de demandes/opérations (429)**  | Peut intervenir si la consommation dépasse la capacité approvisionnée. | Ne se produira pas si le débit consommé est compris dans le débit maximal que vous choisissez avec le mode Autopilot.   |
| **Planification de la capacité** |  Vous devez procéder à une planification initiale de la capacité et approvisionner le débit dont vous avez besoin. |    Vous n'avez pas à vous soucier de la planification de la capacité. Le système prend automatiquement en charge la planification et la gestion de la capacité. |
| **Tarification** | Approvisionnement manuel en unités de requête (RU) par heure. | Pour les comptes à une seule région d'écriture, le débit utilisé est facturé sur une base horaire, en utilisant le taux d'unités de requête par heure du mode Autopilot. <br/><br/>Pour les comptes à plusieurs régions d'écriture, aucuns frais supplémentaires ne s'appliquent au mode Autopilot. Vous payez le débit utilisé sur une base horaire en utilisant le même taux de RU multimaîtres par heure. |
| **Idéal pour les types de charges de travail** |  Charges de travail prévisibles et stables|   Charges de travail imprévisibles et variables  |

## <a id="enable-autopilot"></a> Activer AutoPilot à partir du portail Azure

Vous pouvez essayer la fonctionnalité Autopilot dans vos comptes Azure Cosmos en l’activant à partir du portail Azure. Utilisez les étapes suivantes pour activer l’option Autopilot :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Accédez à votre compte Azure Cosmos et ouvrez l’onglet **Nouvelles fonctionnalités**. Sélectionnez **Auto Pilot** et **Inscrire**, comme illustré dans la capture d’écran suivante :

![Créer un conteneur en mode Autopilot](./media/provision-throughput-autopilot/enable-autopilot-azure-portal.png)

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>Créer une base de données ou un conteneur en mode Autopilot

Vous pouvez configurer les bases de données ou les conteneurs en mode AutoPilot lors de leur création. Procédez comme suit pour créer une base de données ou un conteneur, activer le mode AutoPilot et spécifier le débit maximal.

1. Connectez-vous au [portail Azure](https://portal.azure.com) ou à l'[explorateur Azure Cosmos.](https://cosmos.azure.com/)

1. Accédez à votre compte Azure Cosmos et ouvrez l'onglet **Explorateur de données**.

1. Sélectionnez **Nouveau conteneur**, entrez un nom pour votre conteneur et une clé de partition. Sélectionnez l’option **Autopilot**, puis choisissez le débit maximal que le conteneur ne peut pas dépasser en mode Autopilot.

   ![Créer un conteneur en mode Autopilot](./media/provision-throughput-autopilot/create-container-autopilot-mode.png)

1. Sélectionnez **OK**.

Suivez les mêmes étapes pour créer une base de données avec débit provisionné en mode Autopilot.

## <a id="autopilot-limits"></a> Limites de débit et de stockage pour AutoPilot

Le tableau suivant indique les limites de débit et de stockage pour AutoPilot pour les différentes options en mode AutoPilot :

|Limite de débit maximale  |Limite de stockage maximale  |
|---------|---------|
|4 000 RU/s  |   50 Go    |
|20 000 RU/s  |  200 Go  |
|100 000 RU/s    |  1 To   |
|500 000 RU/s    |  5 To  |

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [partitions logiques](partition-data.md).
* Découvrez comment [approvisionner le débit sur un conteneur Azure Cosmos](how-to-provision-container-throughput.md).
* Découvrez comment [approvisionner le débit sur une base de données Azure Cosmos](how-to-provision-database-throughput.md).
