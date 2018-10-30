---
title: Valider les packages de fabricant d’ordinateurs OEM dans le service Validation en tant que service pour Azure Stack | Microsoft Docs
description: Découvrez comment contrôler les packages de fabricant d’ordinateurs OEM avec le service Validation en tant que service.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: bcfc4cb65c94e34e9f6056ada53726f88489fefb
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646649"
---
# <a name="validate-oem-packages"></a>Valider les packages OEM

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Vous pouvez tester un nouveau package OEM lorsqu’une modification a été apportée au microprogramme ou aux pilotes d’une solution ayant fait l’objet d’une validation. Une fois que votre package a réussi le test, il est signé par Microsoft. Votre test doit contenir le package d’extension OEM mis à jour avec les pilotes et le microprogramme qui ont réussi les tests de certification PCS (Partner Certification Services) et du logo Windows Server.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> Avant de charger ou soumettre des packages, vérifier [Créer un package OEM](azure-stack-vaas-create-oem-package.md) afin d’obtenir le format et le contenu du package escomptés.

## <a name="managing-packages-for-validation"></a>Gestion des packages pour validation

Lorsque vous utilisez le workflow **Validation de package** pour valider un package, vous devez fournir une URL à **Stockage Blob Azure**. Ce blob est le package OEM qui a été installé sur la solution au moment du déploiement. Créez le blob à l’aide du compte de stockage Azure que vous avez créé au moment de l’installation (voir [Configurer votre validation en tant que Ressources de service](azure-stack-vaas-set-up-resources.md)).

### <a name="prerequisite-provision-a-storage-container"></a>Condition préalable : Configurer un conteneur de stockage

Créer un conteneur dans votre compte de stockage pour les objets blob de package. Ce conteneur peut être utilisé à chaque fois que la validation de package est exécutée.

1. Accédez au compte de stockage créé dans [configurer votre validation en tant que Ressources de service](azure-stack-vaas-set-up-resources.md) depuis le [Portail Azure](https://portal.azure.com).
2. Dans le panneau de gauche sous **Service Blob**, sélectionnez **Conteneurs**.
3. Sélectionnez **+ Conteneur** dans la barre de menus et donnez un nom au conteneur. Par exemple, `vaaspackages`.

### <a name="upload-package-to-storage-account"></a>Charger le package pour le compte de stockage

1. Préparer le package que vous voulez valider. Si votre package contient plusieurs fichiers, compressez-le dans un `.zip` fichier.
2. Dans le [Portail Azure](https://portal.azure.com), sélectionnez le conteneur de package et chargez le package en sélectionnant **Charger** dans la barre de menus.
3. Sélectionnez le `.zip`fichier de package à charger. Conservez les valeurs par défaut pour **Type d’objet blob** (par exemple, **objet blob de blocs**) et **Taille de bloc**.

> [!NOTE]
> Veuillez vérifier que les `.zip` contenus soient placés à la racine du `.zip` fichier. Il ne doit y avoir aucun sous-dossier dans le package.

### <a name="generate-package-blob-url-for-vaas"></a>Générer des URL d’objet blob de package pour VaaS

Lorsque vous créez un workflow **Validation de package** dans le portail VaaS, vous devez fournir une URL au Stockage Blob Azure qui contient votre package.

#### <a name="option-1-generating-an-account-sas-url"></a>Option 1 : Générer une URL SAS de compte

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. Sélectionnez l’option **Blob** dans **Services autorisés**. Désélectionnez les autres options.

1. Sélectionnez **Conteneur**, et **Objet** dans **Types de ressources autorisés**. Désélectionnez les autres options.

1. Sélectionnez **Lecture** et **Liste** dans **Permissions autorisées**. Désélectionnez les autres options.

1. Définissez **Heure de début** sur l’heure actuelle et **Heure de fin** sur une heure depuis l’heure actuelle.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]
    Le format devrait s’afficher comme suit : `https://storageaccountname.blob.core.windows.net/?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

1. Modifier l’URL SAS générée pour inclure le conteneur de packages, `{containername}`et le nom de votre objet blob de package, `{mypackage.zip}`, comme suit :  `https://storageaccountname.blob.core.windows.net/{containername}/{mypackage.zip}?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

    Utilisez cette valeur lors du démarrage d’un nouveau workflow **Validation de package** dans le portail VaaS.

#### <a name="option-2-using-public-read-container"></a>Option 2 : Utilisation d’un conteneur en lecture publique

> [!CAUTION]
> Cette option ouvre votre conteneur à un accès anonyme en lecture seule.

1. Autoriser l’**accès en lecture publique pour les objets blob uniquement** du conteneur de package en suivant les instructions dans la section [Accorder à des utilisateurs anonymes des autorisations d’accès aux conteneurs et objets blob](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs).

2. Dans le conteneur de package, sélectionnez l’objet blob de package dans le conteneur pour ouvrir le volet Propriétés.

3. Copiez l’**URL**. Utilisez cette valeur lors du démarrage d’un nouveau workflow **Validation de package** dans le portail VaaS.

## <a name="apply-monthly-update"></a>Appliquer la mise à jour mensuelle

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-package-validation-workflow"></a>Créer un workflow validation de package

1. Se connecter au [portail VaaS](https://azurestackvalidation.com).

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Sélectionnez **Démarrer** sur la vignette **Validation de package**.

    ![Vignette workflow de validations du package](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Spécifiez l’URL de l’objet du stockage Azure qui a été installé sur la solution au moment du déploiement. Pour obtenir des instructions, consultez [Générer des URL d’objet blob de package pour VaaS](#generate-package-blob-url-for-vaas).

6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Après la création d’un workflow, les paramètres d’environnement ne peuvent plus être modifiés.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Vous allez être redirigé vers la page de résumé de tests.

## <a name="run-package-validation-tests"></a>Exécution des tests de validations du package

Sur la page **résumé des tests de validation du Package**, vous trouverez une liste de tests requis pour terminer la validation. Les tests dans ce workflow durent environ 24 heures.

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

Une fois que tous les tests ont été effectués avec succès, envoyez le nom de votre validation de solution VaaS et validation de package à l’adresse [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) pour demander la signature du package.

## <a name="next-steps"></a>Étapes suivantes

- [Surveiller et gérer les tests dans le portail VaaS](azure-stack-vaas-monitor-test.md)