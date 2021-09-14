---
title: Azure Virtual Desktop - Déployer des applications avec l’attachement d’application MSIX - Azure
description: Comment déployer des applications avec l’attachement d’application MSIX pour Azure Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 929f69ca4503a48e8e8456111c85043cbf2db9f2
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730241"
---
# <a name="deploy-apps-with-msix-app-attach"></a>Déployer des applications avec l’attachement d’application MSIX

Cet article fournit une présentation générale de la publication d’une application dans Azure Virtual Desktop avec la fonctionnalité d’attachement d’application MSIX. Dans cet article, nous vous proposons également des liens vers des ressources qui peuvent vous fournir des explications et des instructions plus détaillées.

## <a name="what-is-msix-app-attach"></a>Qu’est-ce que l’attachement d’application MSIX ?

L’attachement d’application MSIX est une solution de superposition des applications qui vous permet de fournir des applications à des sessions utilisateur actives dans Azure Virtual Desktop. Le système de package MSIX sépare les applications du système d’exploitation, ce qui facilite la création d’images pour les machines virtuelles. Les packages MSIX vous permettent également de mieux contrôler les applications auxquelles vos utilisateurs peuvent accéder sur leurs machines virtuelles. Vous pouvez même séparer les applications de l’image principale et les donner aux utilisateurs ultérieurement.

Pour en savoir plus, consultez [Qu’est-ce que l’attachement d’application MSIX ?](../what-is-app-attach.md).

## <a name="requirements"></a>Configuration requise

Vous devez disposer des éléments suivants pour utiliser l’attachement d’application MSIX dans Azure Virtual Desktop :

- Une application empaquetée MSIX
- Une image MSIX créée à partir de l’application MSIX développée
- Un partage MSIX, qui est l’emplacement réseau où vous stockez les images MSIX
- Au moins un hôte de session sain et actif dans le pool d’hôtes que vous allez utiliser
- Si votre application empaquetée MSIX possède un certificat privé, ce certificat doit être disponible sur tous les hôtes de session dans le pool d’hôtes
- Configuration d’Azure Virtual Desktop pour l’attachement d’application MSIX (affectation d’utilisateurs, association d’application MSIX avec un groupe d’applications, ajout d’une image MSIX à un pool d’hôtes)

## <a name="create-an-msix-package-from-an-existing-installer"></a>Créer un package MSIX à partir d’un programme d’installation existant

Pour commencer à utiliser l’attachement d’application MSIX, vous devez mettre votre application dans un package MSIX. Certaines applications sont déjà au format MSIX, mais si vous utilisez un programme d’installation hérité comme MSI, ClickOnce, etc., vous devez convertir l’application au format de package MSIX. Découvrez comment convertir vos applications existantes au format MSIX dans notre [article de présentation de MSIX](/windows/msix/packaging-tool/create-an-msix-overview).

## <a name="test-the-application-fidelity-of-your-packaged-app"></a>Tester la fidélité de votre application empaquetée 

Une fois que vous avez réempaqueté votre application en tant que package MSIX, vous devez vérifier que la fidélité de votre application est élevée. La fidélité de l’application correspond au comportement et aux performances de l’application avant et après le réempaquetage. Un package d’application avec une haute fidélité d’application présente des performances similaires avant et après.

Si vous constatez que la fidélité de votre application diminue après le réempaquetage, votre organisation doit tester l’application pour garantir que ses performances sont conformes aux normes de l’utilisateur. Si ce n’est pas le cas, vous devrez peut-être mettre à jour votre application pour éviter le problème ou retentez le réempaquetage.

## <a name="create-an-msix-image"></a>Créer une image MSIX

Ensuite, vous devez créer une image MSIX à partir de votre application empaquetée. Une image MSIX est ce qui se produit quand vous développez un package d’application MSIX et que vous stockez l’application résultante dans un stockage CIM ou VHD(x). Pour savoir comment créer une image MSIX, consultez[Créer une image MSIX](../app-attach-msixmgr.md#create-an-msix-image).

## <a name="configure-an-msix-file-share"></a>Configurer un partage de fichiers MSIX

Ensuite, vous devez configurer un partage réseau MSIX pour stocker les images MSIX. Une fois configurés, vos hôtes de session utilisent le partage MSIX pour attacher les packages MSIX à des sessions utilisateur actives, fournissant des applications à vos utilisateurs. Découvrez comment configurer un partage MSIX dans [Configurer un partage de fichiers pour l’attachement d’application MSIX\](../app-attach-file-share.md).

## <a name="configure-msix-app-attach-for-azure-virtual-desktop-host-pool"></a>Configurer l’attachement d’application MSIX pour un pool d’hôtes Azure Virtual Desktop

Une fois que vous avez chargé une image MSIX dans le partage MSIX, vous devez ouvrir le portail Azure et configurer le pool d’hôtes que vous allez utiliser pour accepter l’attachement d’application MSIX. Découvrez comment configurer votre pool d’hôtes dans [Configurer l’attachement d’application MSIX avec le portail Azure](../app-attach-azure-portal.md).
