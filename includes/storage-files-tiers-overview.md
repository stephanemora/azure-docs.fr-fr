---
title: Fichier include
description: Fichier include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7fd91e898c12a13e35ae8b9055ebb5a57de2a051
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89272175"
---
Azure Files offre quatre niveaux de stockage, premium, optimisé pour les transactions, chaud et froid, pour vous permettre de personnaliser vos partages en fonction des performances et du budget demandés par votre scénario :

- **Premium** : Les partages de fichiers Premium sont sauvegardés sur des disques SSD et déployés dans le type **Compte de stockage FileStorage**. Les partages de fichiers Premium offrent de façon constante des performances élevées et une faible latence (à un chiffre en millisecondes pour la plupart des opérations d’E/S) pour les charges de travail gourmandes en E/S. Ils sont ainsi adaptés à un vaste éventail de charges de travail, telles que les bases de données, l’hébergement de site web et les environnements de développement. 
- **Optimisé pour les transactions** : Les partages de fichiers optimisés pour les transactions permettent d’avoir des charges de travail lourdes en transactions qui n’ont pas besoin de la latence offerte par les partages de fichiers premium. Les partages de fichiers optimisés pour les transactions sont offerts sur le matériel de stockage standard fourni par des lecteurs de disque dur (HDD) et sont déployés dans le type **Compte de stockage v2 universel (GPv2)** . Ce niveau de stockage était appelé « standard », mais cela fait référence au type de support de stockage plutôt qu’au niveau lui-même (les niveaux chaud et froid sont également des niveaux « standard », car ils se trouvent sur du matériel de stockage standard).
- **Chaud** : Les partages de fichiers chauds offrent un stockage optimisé pour les scénarios de partage de fichiers à usage général, tels que les partages d’équipe et Azure File Sync. Les partages de fichiers chauds sont offerts sur le matériel de stockage standard fourni par des lecteurs HDD et sont déployés dans le type **Compte de stockage v2 universel (GPv2)** .
- **Froid** : Les partages de fichiers froids offrent un stockage abordable optimisé pour les scénarios de stockage d’archive en ligne. Azure File Sync peut également convenir aux charges de travail avec un churn inférieur. Les partages de fichiers froids sont offerts sur le matériel de stockage standard fourni par des lecteurs HDD et sont déployés dans le type **Compte de stockage v2 universel (GPv2)** .

Les partages de fichiers Premium sont disponibles uniquement dans le cadre d’un modèle de facturation avec approvisionnement. Pour plus d’informations sur le modèle de facturation avec approvisionnement pour les partages de fichiers Premium, consultez [Comprendre l’approvisionnement des partages de fichiers Premium](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares). Les partages de fichiers standard, notamment les partages de fichiers optimisés pour les transactions, chauds et froids, sont disponibles dans un modèle de paiement à l’utilisation.

Les partages de fichiers chauds et froids sont actuellement disponibles dans les régions publiques suivantes (les partages de fichiers optimisés pour les transactions sont disponibles dans toutes les régions Azure) :

- Australie Centre
- Australie Centre 2
- Australie Est
- Australie Sud-Est
- Brésil Sud
- Est du Canada
- Centre du Canada
- France Centre
- France Sud
- Allemagne Nord (public)
- Allemagne Centre-Ouest (public)
- Inde Centre
- Sud de l’Inde
- Inde Ouest
- Japon Est
- OuJapon Est
- Centre de la Corée
- Corée du Sud
- Norvège Est
- Norvège Ouest
- Afrique du Sud Nord
- Afrique du Sud Ouest
- Suisse Nord
- Suisse Ouest
- Émirats arabes unis Centre
- Émirats arabes unis Nord
- Sud du Royaume-Uni
- Ouest du Royaume-Uni
- Centre-Nord des États-Unis
- États-Unis - partie centrale méridionale

Pour déployer un partage de fichiers chaud ou froid, consultez [Créer un partage de fichiers chaud ou froid](../articles/storage/files/storage-how-to-create-file-share.md#create-a-hot-or-cool-file-share). 