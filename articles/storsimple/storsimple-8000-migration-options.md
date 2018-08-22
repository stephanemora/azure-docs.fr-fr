---
title: Évaluer les options pour migrer des données de la gamme StorSimple 5000-7000 | Microsoft Docs
description: Fournit une vue d’ensemble des options pour migrer des données de la gamme StorSimple 5000-7000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/10/2018
ms.author: alkohli
ms.openlocfilehash: a1cdf3235674ae651c30fa4f13622b80212abc7d
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2018
ms.locfileid: "40099693"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>Options pour migrer des données de la gamme StorSimple 5000-7000 
La [fin de la prise en charge](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) de la gamme StorSimple 5000-7000 est prévue en juillet 2019. Les clients qui exécutent la gamme StorSimple 5000-7000 peuvent mettre à niveau vers d’autres services hybrides internes Azure. Cet article décrit les options hybrides Azure disponibles pour migrer les données. 

## <a name="migration-options"></a>Options de migration

Les clients qui utilisent la gamme StorSimple 5000-7000 disposent des deux options clés suivantes :

- **Mettre à niveau vers la gamme StorSimple 8000** : mettez à niveau vers la gamme StorSimple 8000 et continuez ainsi sur la plateforme StorSimple.  Ce chemin d’accès de mise à niveau nécessite que les clients remplacent leurs appareils de la gamme 5000-7000 par un de la gamme 8000. Les données sont migrées à partir de l’appareil de la gamme 5000-7000 à l’aide de l’outil de migration. Une fois la migration terminée avec succès, les appareils de la gamme StorSimple 8000 continueront de classer les données dans le stockage Blob Azure. 

    Pour plus d’informations sur la migration des données à l’aide d’un appareil de la gamme StorSimple 8000, accédez à [Migrer des données d’un appareil StorSimple 5000-7000 vers un appareil StorSimple 8000](storsimple-8000-migrate-from-5000-7000.md).

- **Migrer vers Azure File Sync** : cette toute nouvelle option de migration permet aux clients de stocker les partages de fichiers de leur organisation dans les fichiers Azure. Ces partages de fichiers sont ensuite centralisés pour un accès local à l’aide d’Azure File Sync (AFS). AFS peut être déployé sur un hôte Windows Server. La migration des données réelle est ensuite effectuée sous forme de copie hôte ou à l’aide de l’outil de migration.

    Pour plus d’informations sur la migration des données vers Azure File Sync, accédez à [Migrer des données de la gamme StorSimple 5000-7000 vers Azure File Sync](https://aka.ms/StorSimpleMigrationAFS).

## <a name="migration---frequently-asked-questions"></a>Migration - Forum aux questions

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>Q. Quand la fin du service des appareils des gammes StorSimple 5000 et 7000 est-elle prévue ? 

R. La [fin du service](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) de la gamme StorSimple 5000-7000 est prévue en juillet 2019. La fin du service implique que Microsoft ne sera plus en mesure de fournir la prise en charge matérielle et logicielle de ces appareils après juillet 2019. Nous vous recommandons vivement de commencer à envisager dès maintenant un plan pour migrer les données de vos appareils.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>Q. Que se passe-t-il pour les données que j’ai stockées dans Azure ?  

R. Vous pouvez continuer à utiliser les données dans Azure après avoir migré vers un service plus récent. 


### <a name="q--what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>Q.  Que se passe-t-il pour les données que j’ai stockées localement sur mon appareil StorSimple ? 

R. Les données qui se trouvent sur l’appareil local peuvent être copiées vers le service plus récent, comme décrit dans les documents de migration.

### <a name="what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>Que se passe-t-il si je souhaite conserver mon appliance de la gamme StorSimple 5000/7000 ? 

R. Même si les services peuvent continuer à fonctionner, il se peut que Microsoft ne soit plus en mesure de fournir la prise en charge matérielle et logicielle. Une migration est vivement recommandée pour la continuité des activités.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>Q. Quelles options sont disponibles pour migrer des données à partir d’appareils de la gamme StorSimple 5000-7000 ? 

R. En fonction de leur scénario, les utilisateurs de la gamme StorSimple 5000-7000 disposent des options de migration suivantes. 

 - **Mettre à niveau vers la gamme 8000** : utilisez cette option si vous souhaitez continuer sur la plateforme StorSimple. 
 - **Migrer vers Azure File Sync** : utilisez cette option si vous souhaitez basculer vers le format natif d’Azure. Vous pouvez utiliser Azure File Sync pour la gestion centralisée des partages de fichiers. 

Vous pouvez contacter le Support Microsoft pour discuter des options de migration non répertoriées ici.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>Q. La migration vers d’autres solutions de stockage est-elle prise en charge ?

R. Oui. La migration vers d’autres solutions de stockage à l’aide de la copie hôte des données est prise en charge.

### <a name="q-is-migration-supported-by-microsoft"></a>Q. La migration est-elle prise en charge par Microsoft ? 

R. La migration à partir de la gamme 5000 ou 7000 est une opération entièrement prise en charge. En réalité, Microsoft vous recommande de contacter le support avant de commencer la migration. La migration est actuellement une opération assistée. Si vous envisagez de migrer des données à partir de votre appareil de la gamme StorSimple 5000-7000, [ouvrez un ticket de support](storsimple-8000-contact-microsoft-support.md).

### <a name="q-how-does-the-cost-compare-for-the-two-listed-migrations-to-azure-hybrid-services"></a>Q. Quelle est la différence de coûts entre les deux migrations répertoriées vers des services hybrides Azure ? 

R. Le coût de la migration varie selon l’option choisie. Alors que la migration proprement dite est gratuite, si vous décidez de mettre à niveau vers la gamme StorSimple 8000, le coût correspondra à l’appareil matériel. De même, si vous utilisez Azure File Sync, des frais d’abonnement peuvent s’appliquer pour le service. Dans chaque cas, les clients devront également s’acquitter de coûts de stockage. Reportez-vous à la [calculatrice de prix Microsoft des services respectifs](https://azure.microsoft.com/pricing/#product-picker) pour obtenir une estimation.  

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>Q.  Quelle est la durée d’exécution d’une migration ?

R. La durée de migration des données varie en fonction de la quantité de données et de l’option de mise à niveau sélectionnée. 

## <a name="next-steps"></a>Étapes suivantes
 - [Migrer des données d’un appareil StorSimple 5000-7000 vers un appareil StorSimple 8000](storsimple-8000-migrate-from-5000-7000.md).
 - [Migrer des données d’un appareil StorSimple 5000-7000 vers Azure File Sync](storsimple-5000-7000-afs-migration.md)
