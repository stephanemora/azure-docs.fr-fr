---
title: Appliquer Bureau à distance dans Microsoft Azure Cloud Services (support étendu)
description: Activer Bureau à distance pour Microsoft Azure Cloud Services (support étendu)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 94827916f28c9028d46bf7b5461a4fbd941b2a96
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773400"
---
# <a name="apply-the-remote-desktop-extension-to-azure-cloud-services-extended-support"></a>Appliquer le Bureau à distance à Microsoft Azure Cloud Services (support étendu)

Le Portail Azure utilise l’extension Bureau à distance, pour activer le Bureau à distance même après le déploiement de l’application. Les paramètres du bureau à distance pour votre service Cloud vous permettent d'activer le bureau à distance, de mettre à jour le compte de l'administrateur local, de sélectionner les certificats utilisés dans l'authentification et de définir la date d'expiration de ces certificats. 

## <a name="apply-remote-desktop--extension"></a>Appliquer l’extension Bureau à distance
1. Accédez au service cloud pour lequel vous souhaitez activer le Bureau à distance, puis sélectionnez **« Bureau à distance »** dans le volet de navigation gauche.

    :::image type="content" source="media/remote-desktop-1.png" alt-text="L’image affiche la sélection de l’option Bureau à distance dans le Portail Azure":::

2. Sélectionnez **Ajouter**.
3. Choisissez les rôles pour lesquels activer le Bureau à distance.
4. Renseignez les champs requis pour le nom d’utilisateur, le mot de passe, la date d’expiration et le certificat (non requis).
> [Remarque] Le mot de passe pour le bureau à distance doit comprendre entre 8 et 123 caractères, et doit remplir au moins trois critères de complexité de mot de passe parmi les suivants : 1) Contenir un caractère majuscule 2) Contenir un caractère minuscule 3) Contenir un chiffre 4) Contenir un caractère spécial 5) Les caractères de contrôle ne sont pas autorisés

   :::image type="content" source="media/remote-desktop-2.png" alt-text="Image montrant comment entrer les informations requises pour se connecter au bureau à distance.":::

5. Lorsque vous avez terminé, sélectionnez **Enregistrer**. Vos instances de rôles pourront recevoir les connexions quelques instants plus tard.

## <a name="connect-to-role-instances-with-remote-desktop-enabled"></a>Se connecter à des instances de rôle avec Bureau à distance activé
Une fois que le Bureau à distance aura été activé sur les rôles, vous pourrez initier une connexion directement à partir du Portail Azure.

1. Cliquez sur **Rôles et instances** pour ouvrir les paramètres de l’instance.

    :::image type="content" source="media/remote-desktop-3.png" alt-text="L’image affiche la sélection de l’option Rôles et instances dans le panneau de configuration.":::

2. Sélectionnez une instance de rôle pour laquelle la fonctionnalité Bureau à distance est configurée.
3. Cliquez sur **Se connecter** pour télécharger un fichier de connexion Bureau à distance.

    :::image type="content" source="media/remote-desktop-4.png" alt-text="L’image affiche la sélection de l’instance de rôle de travail dans le Portail Azure.":::
    
4. Ouvrez le fichier pour vous connecter à l’instance de rôle.


## <a name="next-steps"></a>Étapes suivantes 
- Consultez les [prérequis du déploiement](deploy-prerequisite.md) de Cloud Services (support étendu).
- Consultez les [questions fréquentes (FAQ)](faq.md) sur Cloud Services (support étendu).
- Déployez une instance de Cloud Services (support étendu) avec le [portail Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), un [modèle](deploy-template.md) ou [Visual Studio](deploy-visual-studio.md).
