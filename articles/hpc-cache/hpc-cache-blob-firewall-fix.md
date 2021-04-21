---
title: Contourner les paramètres du pare-feu de stockage
description: Un paramètre de pare-feu réseau de compte de stockage peut entraîner un échec lors de la création d’une cible de stockage Blob Azure dans Azure HPC Cache. Cet article fournit une solution de contournement pour la limitation jusqu’à ce qu’un correctif logiciel soit en place.
author: ekpgh
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 03/18/2021
ms.author: v-erkel
ms.openlocfilehash: 45a7169330b11e98a8618b08205217212414ca5d
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258926"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>Contourner les paramètres de pare-feu de compte de stockage Blob

Un paramètre particulier utilisé dans les pare-feu de compte de stockage peut faire échouer la création de la cible de stockage d’objets blob. L’équipe Azure HPC Cache travaille sur un correctif logiciel pour résoudre ce problème, mais vous pouvez le contourner en suivant les instructions de cet article.

Le paramètre de pare-feu qui autorise l’accès uniquement à partir de « réseaux sélectionnés » peut empêcher le cache de créer ou de modifier une cible de stockage Blob. Cette configuration se trouve dans la page de paramètres **Pare-feu et réseaux virtuels** du compte de stockage. (Ce problème ne s’applique pas aux cibles de stockage ADLS-NFS.)

Le problème est que le service de cache utilise un réseau virtuel de service masqué distinct des environnements des clients. Il n’est pas possible d’autoriser explicitement ce réseau à accéder à votre compte de stockage.

Quand vous créez une cible de stockage d’objets blob, le service de cache utilise ce réseau pour vérifier si le conteneur est vide ou non. Si le pare-feu n’autorise pas l’accès à partir du réseau masqué, la vérification échoue, ainsi que la création de la cible de stockage.

Le pare-feu peut également bloquer les modifications apportées aux chemins d’accès aux espaces de noms cibles du stockage Blob.

Pour contourner ce problème, changez temporairement les paramètres de votre pare-feu lors de la création de la cible de stockage :

1. Accédez à la page **Pare-feu et réseaux virtuels** du compte de stockage, puis définissez le paramètre « Autoriser l’accès depuis » sur **Tous les réseaux**.
1. Créez la cible de stockage d’objets blob dans votre instance Azure HPC Cache.
1. Créer le chemin d’accès aux espaces de noms de la cible de stockage.
1. Une fois la cible de stockage et le chemin d’accès créés avec succès, redéfinissez le paramètre de pare-feu du compte sur **Réseaux sélectionnés**.

Azure HPC Cache n’utilise pas le réseau virtuel de service pour accéder à la cible de stockage terminée.

Pour obtenir de l’aide sur cette solution de contournement, [contacter le support technique Microsoft](hpc-cache-support-ticket.md).
