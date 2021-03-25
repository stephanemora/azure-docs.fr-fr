---
title: Notification de mise à jour automatique et d’expiration du runtime d’intégration auto-hébergé
description: En savoir plus sur la notification de mise à jour automatique et d’expiration du runtime d’intégration auto-hébergé
ms.service: data-factory
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 12/25/2020
ms.openlocfilehash: 972015f0f42a8a869de0edcc8f0e921ae7278cd1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376256"
---
# <a name="self-hosted-integration-runtime-auto-update-and-expire-notification"></a>Notification de mise à jour automatique et d’expiration du runtime d’intégration auto-hébergé

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article décrit comment autoriser la mise à jour automatique du runtime d’intégration auto-hébergé vers la dernière version et comment ADF gère les versions du runtime d’intégration auto-hébergé.

## <a name="self-hosted-integration-runtime-auto-update"></a>Mise à jour automatique du runtime d'intégration auto-hébergé
En règle générale, lorsque vous installez un runtime d’intégration auto-hébergé sur votre machine locale ou sur une machine virtuelle Azure, vous disposez de deux options pour gérer la version du runtime d’intégration auto-hébergé : mise à jour automatique ou maintenance manuelle. En général, ADF publie deux nouvelles versions du runtime d’intégration auto-hébergé chaque mois, qui incluent une nouvelle fonctionnalité, un correctif de bogue ou une amélioration. Nous recommandons donc aux utilisateurs de mettre à jour vers la dernière version pour bénéficier des fonctionnalités et améliorations les plus récentes.

La méthode la plus pratique consiste à activer la mise à jour automatique lorsque vous créez ou modifiez le runtime d’intégration auto-hébergé. Ensuite, il sera automatiquement mis à jour vers la version la plus récente. Vous pouvez également planifier la mise à jour sur la plage horaire qui vous convient le mieux.

![Activer la mise à jour automatique](media/create-self-hosted-integration-runtime/shir-auto-update.png)

Vous pouvez vérifier la date et l’heure de la dernière mise à jour dans votre client du runtime d’intégration auto-hébergé.

![Capture d’écran de la vérification de l’heure de mise à jour](media/create-self-hosted-integration-runtime/shir-auto-update-2.png)

> [!NOTE]
> Pour garantir la stabilité du runtime d’intégration auto-hébergé, même si nous publions deux versions, nous ne le mettons à jour qu’une seule fois par mois. Par conséquent, vous constaterez parfois que la version mise à jour automatiquement correspond à la version précédente de la version la plus récente. Si vous souhaitez obtenir la version la plus récente, vous pouvez accéder au [centre de téléchargement](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="self-hosted-integration-runtime-expire-notification"></a>Notification d’expiration du runtime d’intégration auto-hébergé
Si vous souhaitez contrôler manuellement la version du runtime d’intégration auto-hébergé, vous pouvez désactiver le paramètre de mise à jour automatique et l’installer manuellement. Chaque version du runtime d’intégration auto-hébergé expire dans un délai d’un an. Le message d’expiration s’affiche dans le portail ADF et le client du runtime d’intégration auto-hébergé **90 jours** avant l’expiration.

## <a name="next-steps"></a>Étapes suivantes

- Étudiez les [concepts de runtime d’intégration dans Azure Data Factory](./concepts-integration-runtime.md).

- Apprenez à [créer un runtime d’intégration auto-hébergé sur le portail Azure](./create-self-hosted-integration-runtime.md).
