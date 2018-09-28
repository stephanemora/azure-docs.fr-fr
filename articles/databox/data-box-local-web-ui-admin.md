---
title: Administration de l’interface utilisateur web locale de Microsoft Azure Data Box | Docs Microsoft dans les données
description: Explique comment utiliser l’interface utilisateur web locale pour gérer votre appareil Data Box
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: ''
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/11/2018
ms.author: alkohli
ms.openlocfilehash: a5f3f1b4d0d67a12ee0ebaa40935b8494e53dab3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956300"
---
# <a name="use-the-local-web-ui-to-administer-your-data-box"></a>Utiliser l’interface utilisateur web locale pour administrer votre Data Box

Cet article décrit certaines des tâches de gestion et de configuration qui peuvent être effectuées sur la Data Box. Vous pouvez gérer la Data Box via l’interface utilisateur du portail Azure et l’interface utilisateur web locale pour l’appareil. Cet article se concentre sur les tâches que vous pouvez effectuer à l'aide de l'interface utilisateur web locale.

L’interface utilisateur web locale pour la Data Box est utilisée pour la configuration initiale de l’appareil. Vous pouvez également utiliser l’interface utilisateur web locale pour arrêter ou redémarrer la Data Box, exécuter des tests de diagnostic, mettre à jour les logiciels, afficher les journaux de copie et générer un package de journaux destiné au support Microsoft.

Cet article inclut les didacticiels suivants :

- Générer un package de support
- Arrêter ou redémarrer votre appareil
- Effacer en toute sécurité des données de votre appareil
- Analyser la capacité disponible sur l’appareil
- Ignorer la validation de la somme de contrôle 

## <a name="generate-support-package"></a>Générer un package de support

Si vous rencontrez des problèmes avec l’appareil, vous pouvez créer un package de support dans les journaux système. Le support Microsoft utilise ce package pour résoudre le problème. Pour générer un package de support, procédez comme suit :

1. Dans l’interface utilisateur web locale, accédez à **Contacter le support technique**, puis cliquez sur **Générer un package de support**.

    ![Créer un package de support 1](media/data-box-local-web-ui-admin/create-support-package-1.png)

2. Un package de support est collecté. Cette opération prend quelques minutes.

    ![Créer un package de support 2](media/data-box-local-web-ui-admin/create-support-package-2.png)

3. Une fois le package de support créé, cliquez sur **Télécharger le package de support**. 

    ![Créer un package de support 4](media/data-box-local-web-ui-admin/create-support-package-4.png)

4. Recherchez puis choisissez le dossier de téléchargement. Ouvrez le dossier pour afficher le contenu.

    ![Créer un package de support 5](media/data-box-local-web-ui-admin/create-support-package-5.png)


## <a name="shut-down-or-restart-your-device"></a>Arrêter ou redémarrer votre appareil

Vous pouvez arrêter ou redémarrer Data Box à l'aide de l'interface utilisateur web locale. Avant de redémarrer, nous vous recommandons de mettre les partages hors connexion sur l’ordinateur hôte. Vous réduisez ainsi toute possibilité d’altération des données. Vérifiez que la copie des données n’est pas en cours lorsque vous arrêtez l’appareil.

Pour arrêter votre Data Box, procédez comme suit.

1. Dans l’interface utilisateur web locale, accédez à **Arrêter ou redémarrer**.
2. Cliquez sur **Arrêter**.

    ![Arrêter Data Box 1](media/data-box-local-web-ui-admin/shut-down-local-web-ui-1.png)

3. Lorsque vous êtes invité à confirmer l’opération, cliquez sur **OK** pour continuer.

    ![Arrêter Data Box 2](media/data-box-local-web-ui-admin/shut-down-local-web-ui-2.png)

Une fois l’appareil arrêté, utilisez le bouton d’alimentation sur le panneau avant pour allumer l’appareil.

Pour redémarrer votre Data Box, suivez les étapes ci-dessous.

1. Dans l’interface utilisateur web locale, accédez à **Arrêter ou redémarrer**.
2. Cliquez sur **Restart (Redémarrer)**.

    ![Redémarrer Data Box 1](media/data-box-local-web-ui-admin/restart-local-web-ui-1.png)

3. Lorsque vous êtes invité à confirmer l’opération, cliquez sur **OK** pour continuer.

   L’appareil s’arrête puis redémarre.

## <a name="view-available-capacity-of-the-device"></a>Afficher la capacité disponible sur l’appareil

Vous pouvez utiliser le tableau de bord de l’appareil pour afficher la capacité disponible et la capacité utilisée de l’appareil. 

1. Dans l’interface utilisateur web locale, accédez à **Afficher le tableau de bord**.
2. La fenêtre **Connexion et copie** affiche l’espace libre et l’espace utilisé sur l’appareil.

    ![Afficher la capacité disponible](media/data-box-local-web-ui-admin/verify-used-space-dashboard.png)


## <a name="skip-checksum-validation"></a>Ignorer la validation de la somme de contrôle

Par défaut, une somme de contrôle est générée pour vos données lorsque vous préparez l’expédition. Dans certains cas rares, selon le type de données (fichiers de petite taille), les performances peuvent être très lentes. Dans ces cas, vous pouvez ignorer la somme de contrôle. 

Nous vous recommandons vivement de ne pas désactiver la somme de contrôle, sauf si les performances sont fortement affectées.

1. Dans le coin supérieur droit de l’interface utilisateur web locale de votre appareil, accédez aux paramètres.

    ![Désactiver la somme de contrôle](media/data-box-local-web-ui-admin/disable-checksum.png)

2. **Désactiver** la validation de la somme de contrôle
3. Cliquez sur **Appliquer**.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [gérer Data Box via le portail Azure](data-box-portal-admin.md).

