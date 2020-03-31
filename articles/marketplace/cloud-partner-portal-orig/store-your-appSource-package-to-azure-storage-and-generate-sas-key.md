---
title: Stocker votre package AppSource dans le stockage Azure et générez une URL à l’aide d’une clé SAS
description: Détaille les étapes requises pour charger et sécuriser un package AppSource.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: dsindona
ms.openlocfilehash: 57bc370fd160b8b3d6d7941ea28cd460c99f3d96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285365"
---
<a name="store-your-appsource-package-to-azure-storage-and-generate-a-url-with-sas-key"></a>Stocker votre package AppSource dans le stockage Azure et générez une URL à l’aide d’une clé SAS
=============================================================================

Pour assurer la sécurité de vos fichiers, tous les partenaires doivent stocker leur fichier de package AppSource dans un compte de stockage blob Azure et utiliser une clé SAS pour le partager. Nous récupérerons le fichier de package depuis votre emplacement de stockage Azure pour certification et nous l’utiliserons pour des essais AppSource.

Pour charger votre package vers le stockage blob, procédez comme suit :

1. Allez sur <https://azure.microsoft.com> et créez un compte gratuit d’évaluation ou un compte facturé.

2. Connectez-vous au [Portail Azure](https://portal.azure.com/).

3. Créez un nouveau compte de stockage en cliquant sur **+ Nouveau** et en allant sur le compte **Données + Stockage**.

   ![Panneau Données + Stockage sur le portail Microsoft Azure](media/CRMScreenShot7.png)

4. Saisissez un **Nom** et un nom de **Groupe de ressources**, puis cliquez sur le bouton **Créer**.

   ![Créer un compte de stockage sur le portail Microsoft Azure](media/CRMScreenShot8.png)

5. Naviguez dans votre groupe de ressources nouvellement créé et créer un nouveau conteneur blob.

   ![Téléchargez le package en tant que blob à l’aide du portail Microsoft Azure](media/CRMScreenShot9.png)

6. Si ce n’est déjà fait, téléchargez et installez l’[Explorateur Stockage Azure](https://storageexplorer.com/) de Microsoft.

7. Ouvrez Explorateur Stockage et utilisez l’icône pour vous connecter à votre compte de stockage Azure.

8. Naviguez dans le conteneur blob que vous avez créé et cliquez sur **Charger** pour ajouter votre fichier zip de package.

   ![Charger le package à l’aide d’Explorateur Stockage Microsoft](media/CRMScreenShot10.png)

9. Effectuez un clic droit sur votre fichier, puis sélectionnez **Obtenir une signature d’accès partagé**.

   ![Obtenez une signature d’accès partagé d’un fichier Azure](media/CRMScreenShot11.png)

10. Modifiez l’**Heure d’expiration** pour activer la SAP pendant un mois, puis cliquez sur **Créer**.

    ![Modifier la date d’expiration de SAP d’un fichier Azure](media/CRMScreenShot12.png)

11. Copiez le champ URL et enregistrez-le pour une utilisation ultérieure. Vous devrez saisir cette URL lorsque vous créerez l’offre associée. 

    ![Copier l’URL SAS d’un fichier Azure](media/CRMScreenShot13.png)

