---
title: Problèmes connus d’Azure Percept
description: En savoir plus sur les problèmes connus d’Azure Percept et leurs solutions de rechange
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: reference
ms.date: 03/25/2021
ms.openlocfilehash: bf1e6f1297e73811e4ce7044319b13dc4c5b149f
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114295869"
---
# <a name="azure-percept-known-issues"></a>Problèmes connus d’Azure Percept

Voici les problèmes liés à Azure Percept DK, Azure Percept Audio ou Azure Percept Studio dont les équipes de produits sont informées. Des solutions de contournement et étapes de résolution des problèmes sont fournies dans la mesure du possible. Si vous êtes bloqué par l’un de ces problèmes, vous pouvez publier un billet en tant que question sur [Microsoft Q&A](/answers/topics/azure-percept.html) ou soumettre une demande de support client dans le [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). 

|Domaine|Symptômes|Description du problème|Solution de contournement|
|-------|---------|---------|---------|
| DK Azure Percept | Impossible de déployer les exemples de modèle et les modèles de démonstration dans Azure Percept Studio | Parfois, les modules azureeyemodule ou azureearspeechmodule cessent de fonctionner. Les journaux edgeAgent contiennent un message d’erreur « Trop de niveaux de liens symboliques ». | Réinitialiser votre appareil en le [mettant à jour via USB](./how-to-update-via-usb.md) |
| Localisation | Il se peut que des utilisateurs non anglophones voient certaines parties du processus d’installation d’Azure Percept DK afficher du texte en anglais. | L’expérience d’installation d’Azure Percept DK n’est pas entièrement localisée. | Un correctif est planifié pour juillet 2021  |
| DK Azure Percept | Lorsque vous exécutez le processus d’installation sur un Mac, il se peut qu’il se ferme soudainement après connexion au réseau Wi-Fi. | Lorsque vous exécutez le processus d’installation sur un Mac, il s’ouvre initialement dans une fenêtre plutôt que dans un navigateur web. La fenêtre n’est pas conservée une fois que la connexion bascule du point d’accès de l’appareil au Wi-Fi. | Ouvrez un navigateur Web et accédez à https://10.1.1.1, ce qui vous permettra d’effectuer l’installation. |
| DK Azure Percept | Le kit de développement exécute un modèle personnalisé, puis, après redémarrage, exécute l’exemple de modèle par défaut. | Le conteneur de jumeau de module pour le modèle personnalisé n’est pas conservé entre les redémarrages de l’appareil. Après le redémarrage, le jumeau de module personnalisé doit être reconstruit, ce qui peut prendre 5 minutes voire davantage. Le kit de développement exécute le modèle par défaut jusqu’à ce que le processus soit terminé. | Après un redémarrage, vous devez attendre la recréation du jumeau de module personnalisé. |