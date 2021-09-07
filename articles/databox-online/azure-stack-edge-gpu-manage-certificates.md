---
title: Utiliser des certificats avec Azure Stack Edge Pro avec GPU | Microsoft Docs
description: Décrit l’utilisation de certificats avec les appareils Azure Stack Edge Pro avec GPU, y compris la raison pour laquelle les utiliser et les types et la méthode de téléchargement des certificats sur votre appareil.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/01/2021
ms.author: alkohli
ms.openlocfilehash: d39b1f1b4220c0899cb649f0544bc7da94f20c09
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113361600"
---
# <a name="upload-import-and-export-certificates-on-azure-stack-edge-pro-gpu"></a>Charger, importer et exporter des certificats sur l’appareil GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Pour garantir une communication sécurisée et fiable entre votre appareil Azure Stack Edge et les clients qui s’y connectent, vous pouvez utiliser des certificats auto-signés ou apporter vos propres certificats. Cet article explique comment gérer ces certificats, notamment comment les charger, les importer, les exporter ou voir leur date d’expiration.

Pour en savoir plus sur la création de ces certificats, consultez [Créer des certificats à l’aide d’Azure PowerShell](azure-stack-edge-gpu-create-certificates-powershell.md).


## <a name="upload-certificates-on-your-device"></a>Charger des certificats sur votre appareil

Si vous apportez vos propres certificats, les certificats que vous avez créés pour votre appareil résident par défaut dans le **magasin personnel** sur votre client. Ces certificats doivent être exportés sur votre client dans des fichiers de format appropriés qui peuvent ensuite être chargés sur votre appareil.


### <a name="prerequisites"></a>Configuration requise

Avant de charger vos certificats racines et vos certificats de point de terminaison sur l’appareil, assurez-vous que les certificats sont exportés dans le format approprié.

- Le certificat racine doit être exporté au format DER avec l’extension `.cer`. Pour obtenir des instructions détaillées, consultez [Exporter des certificats au format DER](azure-stack-edge-gpu-prepare-certificates-device-upload.md#export-certificates-as-der-format).
- Les certificats de point de terminaison doivent être exportés en tant que fichiers *.pfx* avec des clés privées. Pour obtenir des instructions détaillées, consultez [Exporter des certificats en tant que fichiers *.pfx* avec des clés privées](azure-stack-edge-gpu-prepare-certificates-device-upload.md#export-certificates-as-pfx-format-with-private-key). 

### <a name="upload-certificates"></a>Charger des certificats 

Pour charger des certificats racines et de point de terminaison sur l’appareil, utilisez l’option **+ Ajouter un certificat** dans la page **Certificats** de l’interface utilisateur web locale. Procédez comme suit :

1. Téléchargez d’abord le certificat racine. Dans l’interface utilisateur web locale, accédez à **Certificats > + Ajouter un certificat**.

    ![Ajouter un certificat de chaîne de signature 1](media/azure-stack-edge-series-manage-certificates/add-cert-1.png)

2. Ensuite, téléchargez les certificats de point de terminaison. 

    ![Ajouter un certificat de chaîne de signature 2](media/azure-stack-edge-series-manage-certificates/add-cert-2.png)

    Choisissez les fichiers de certificat au format *.pfx* et entrez le mot de passe que vous avez fourni lors de l’exportation du certificat. L’application du certificat Azure Resource Manager peut prendre quelques minutes.

    Si la chaîne de signature n’est pas mise à jour d’abord et que vous essayez de charger les certificats de point de terminaison, vous obtiendrez une erreur.

    ![Erreur d’application du certificat](media/azure-stack-edge-series-manage-certificates/apply-cert-error-1.png)

    Revenez en arrière et chargez le certificat de chaîne de signature, puis chargez et appliquez les certificats de point de terminaison.

> [!IMPORTANT]
> Si le nom de l’appareil ou le domaine DNS est modifié, de nouveaux certificats doivent être créés. Les certificats clients et les certificats d’appareil doivent ensuite être mis à jour avec les nouveaux nom d’appareil et domaine DNS. 

## <a name="import-certificates-on-the-client-accessing-the-device"></a>Importer des certificats sur le client qui accède à l’appareil

Vous pouvez utiliser les certificats générés par l’appareil ou apporter vos propres certificats. Si vous utilisez les certificats générés par l’appareil, vous devez les télécharger sur votre client avant de pouvoir les importer dans le magasin de certificats approprié. Consultez [Télécharger des certificats sur le client qui accède à l’appareil](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates).

Dans les deux cas, les certificats que vous avez créés et chargés sur votre appareil doivent être importés sur votre client Windows (qui accède à l’appareil) dans le magasin de certificats approprié. 

- Le certificat racine que vous avez exporté en tant que DER doit maintenant être importé dans les **Autorités de certification racines de confiance** sur votre système client. Pour obtenir des instructions détaillées, consultez [Importer des certificats dans le magasin d’autorités de certification racine approuvées](#import-certificates-as-der-format).

- Les certificats de point de terminaison que vous avez exportés en tant que `.pfx` doivent être exportés au format DER avec l’extension `.cer`. Ce `.cer` est ensuite importé dans le **magasin de certificats personnel** sur votre système. Pour obtenir des instructions détaillées, consultez [Importer des certificats dans le magasin de certificats personnel](#import-certificates-as-der-format).

### <a name="import-certificates-as-der-format"></a>Importer des certificats au format DER 

Pour importer des certificats sur un client Windows, procédez comme suit :

1. Cliquez avec le bouton droit sur le fichier et sélectionnez **Installer le certificat**. Cette action démarre l’Assistant Importation de certificat.

    ![Importation du certificat 1](media/azure-stack-edge-series-manage-certificates/import-cert-1.png)

2. Pour **Emplacement du magasin**, sélectionnez **Ordinateur local**, puis sélectionnez **Suivant**.

    ![Importation du certificat 2](media/azure-stack-edge-series-manage-certificates/import-cert-2.png)

3. Sélectionnez **Placer tous les certificats dans le magasin suivant**, puis sélectionnez **Parcourir**. 

    - Pour effectuer l’importation dans le magasin personnel, accédez au magasin personnel de votre hôte distant, puis sélectionnez **Suivant**.

        ![Importer le certificat 4](media/azure-stack-edge-series-manage-certificates/import-cert-4.png)


    - Pour effectuer l’importation dans le magasin de confiance, accédez à l’autorité de certification racine approuvée, puis sélectionnez **Suivant**.

        ![Importer le certificat 3](media/azure-stack-edge-series-manage-certificates/import-cert-3.png)

 
4. Sélectionnez **Terminer**. Un message indiquant que l’importation a réussi s’affiche.


## <a name="view-certificate-expiry"></a>Afficher l’expiration du certificat

Si vous apportez vos propres certificats, les certificats expirent généralement après 1 an ou 6 mois. Pour afficher la date d’expiration de votre certificat, accédez à la page **Certificats** dans l’interface utilisateur web locale de votre appareil. Si vous sélectionnez un certificat spécifique, vous pouvez afficher la date d’expiration de votre certificat.


## <a name="next-steps"></a>Étapes suivantes

[Résoudre les problèmes de certificat](azure-stack-edge-gpu-certificate-troubleshooting.md)