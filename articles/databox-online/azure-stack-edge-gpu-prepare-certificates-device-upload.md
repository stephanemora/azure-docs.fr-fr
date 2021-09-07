---
title: Préparer les certificats à charger sur votre appareil Azure Stack Edge Pro GPU/Pro R/Mini R
description: Décrit comment préparer les certificats à charger sur des appareils Azure Stack Edge Pro GPU/Pro R/Mini R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/30/2021
ms.author: alkohli
ms.openlocfilehash: 2db11b9a1d159e292140901b50c2d7b659c88235
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113364055"
---
# <a name="prepare-certificates-to-upload-on-your-azure-stack-edge-pro-gpu"></a>Préparer les certificats à charger sur votre appareil Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article explique comment convertir les certificats dans un format approprié pour les préparer en vue de leur chargement sur votre appareil Azure Stack Edge. Cette procédure est généralement à effectuer si vous apportez vos propres certificats.

Pour en savoir plus sur la création de ces certificats, consultez [Créer des certificats à l’aide d’Azure PowerShell](azure-stack-edge-gpu-create-certificates-powershell.md).


## <a name="prepare-certificates"></a>Préparer les certificats

Si vous apportez vos propres certificats, les certificats que vous avez créés pour votre appareil résident par défaut dans le **magasin personnel** sur votre client. Ces certificats doivent être exportés sur votre client dans des fichiers de format appropriés qui peuvent ensuite être chargés sur votre appareil.

- **Préparer les certificats racines** : le certificat racine doit être exporté au format DER avec l’extension `.cer`. Pour obtenir des instructions détaillées, consultez [Exporter des certificats au format DER](#export-certificates-as-der-format).

- **Préparer les certificats de point de terminaison** : les certificats de point de terminaison doivent être exportés sous forme de fichiers  *.pfx* avec des clés privées. Pour obtenir des instructions détaillées, consultez [Exporter des certificats en tant que fichiers *.pfx* avec des clés privées](#export-certificates-as-pfx-format-with-private-key). 


## <a name="export-certificates-as-der-format"></a>Exporter les certificats au format DER

1. Exécutez *certlm.msc* pour lancer le magasin de certificats de l’ordinateur local.

1. Dans le magasin de certificats personnel, sélectionnez le certificat racine. Cliquez avec le bouton droit, puis sélectionnez **All Tasks (Toutes les tâches) -> Exporter...** .

    ![Exporter le certificat DER 1](media/azure-stack-edge-series-manage-certificates/export-cert-cer-1.png)

2. L’assistant de certificat s’ouvre. Sélectionnez le format **X.509 binaire encodé DER (.cer)** . Sélectionnez **Suivant**.

    ![Exporter le certificat DER 2](media/azure-stack-edge-series-manage-certificates/export-cert-cer-2.png)

3. Recherchez et sélectionnez l’emplacement où vous souhaitez exporter le fichier au format .cer.

    ![Exporter le certificat DER 3](media/azure-stack-edge-series-manage-certificates/export-cert-cer-3.png)

4. Sélectionnez **Terminer**.

    ![Exporter le certificat DER 4](media/azure-stack-edge-series-manage-certificates/export-cert-cer-4.png)


## <a name="export-certificates-as-pfx-format-with-private-key"></a>Exporter les certificats au format .pfx avec une clé privée

Procédez comme suit pour exporter un certificat SSL avec une clé privée sur un ordinateur Windows. 

> [!IMPORTANT]
> Effectuez ces étapes sur le même ordinateur que celui que vous avez utilisé pour créer le certificat. 

1. Exécutez *certlm.msc* pour lancer le magasin de certificats de l’ordinateur local.

1. Double-cliquez sur le dossier **Personnel**, puis sur **Certificats**.

    ![Exporter le certificat 1](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-1.png)
 
2. Cliquez avec le bouton droit sur le certificat que vous souhaitez sauvegarder, puis choisissez **Toutes les tâches > Exporter...**

    ![Exporter le certificat 2](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-2.png)

3. Suivez l’Assistant d’exportation de certificat pour sauvegarder votre certificat dans un fichier .pfx.

    ![Exporter le certificat 3](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-3.png)

4. Choisissez **Oui, exporter la clé privée**.

    ![Exporter le certificat 4](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-4.png)

5. Choisissez **Inclure tous les certificats dans le chemin d’accès du certificat si possible**, **Exporter toutes les propriétés étendues** et **Activer la confidentialité du certificat**. 

    > [!IMPORTANT]
    > NE sélectionnez PAS l’option **Supprimer la clé privée si l’exportation est réussie**.

    ![Exporter le certificat 5](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-5.png)

6. Entrez un mot de passe dont vous vous souviendrez. Confirmez le mot de passe. Le mot de passe protège la clé privée.

    ![Exporter le certificat 6](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-6.png)

7. Choisissez d’enregistrer le fichier à un emplacement défini.

    ![Exporter le certificat 7](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-7.png)
  
8. Sélectionnez **Terminer**.

    ![Exporter le certificat 8](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-8.png)

9. Vous recevez un message indiquant que l’exportation a réussi. Sélectionnez **OK**.

    ![Exporter le certificat 9](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-9.png)

La sauvegarde du fichier .pfx est maintenant enregistrée à l’emplacement que vous avez sélectionné et est prête à être déplacée ou stockée pour une conservation sûre.


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [Charger des certificats sur votre appareil](azure-stack-edge-gpu-manage-certificates.md).
