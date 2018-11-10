---
title: Activer le support pour Avere vFXT - Azure
description: Découvrez comment activer les chargements de données de support à partir d’Avere vFXT pour Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: bc21e06b704bfe3d25132092efbbf23f342acb14
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669286"
---
# <a name="enable-support-uploads"></a>Activer les chargements de données de support

Avere vFXT pour Azure peut charger automatiquement des données de support relatives à votre cluster. Ces chargements permettent aux équipes de support de délivrer un meilleur service client.

## <a name="steps-to-enable-uploads"></a>Étapes à suivre pour activer les chargements

Pour activer le support, effectuez les étapes suivantes à partir du Panneau de configuration Avere. (Consultez [Accéder au cluster vFXT](avere-vfxt-cluster-gui.md) pour savoir comment ouvrir le Panneau de configuration Avere.)

1. Accédez à l’onglet **Settings** (Paramètres) en haut.
1. Cliquez sur le lien **Support** sur la gauche et acceptez la politique de confidentialité.

   ![Capture d’écran pour confirmer l’acceptation de la politique de confidentialité](media/avere-vfxt-privacy-policy.png)
1. Cliquez sur le triangle à gauche de **Customer Info** (Informations client) pour développer la section.
1. Nommez le support du cluster dans **Unique Cluster Name** (Nom de cluster unique) en veillant à choisir un nom qui identifie de manière unique votre cluster auprès de l’équipe de support.
1. Cochez les cases **Statistics Monitoring** (Analyse des statistiques), **General Information Upload** (Chargement des informations générales) et **Crash Information Upload** (Chargement des informations d’incident).
1. Cliquez sur le bouton **Validate upload information** (Valider les informations de chargement).
1. Cliquez sur **Envoyer**.
1. Cliquez sur le triangle à gauche de **Secure Proactive Support (SPS)** pour développer la section.
1. Cochez la case **Enable SPS Link** (Activer le lien SPS).
1. Cliquez sur **Envoyer**.

   ![Capture d’écran présentant toutes les étapes pour activer le support](media/avere-vfxt-support-info-steps.png)


## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’ajouter un système de stockage local au cluster, suivez les instructions détaillées dans [Configurer un stockage](avere-vfxt-add-storage.md). 

Si vous êtes prêt à attacher des clients au cluster, consultez [Monter le cluster Avere vFXT](avere-vfxt-mount-clients.md).