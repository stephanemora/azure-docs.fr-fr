---
title: Préparer des certificats pour infrastructure à clé publique Azure Stack pour le déploiement de systèmes intégrés Azure Stack | Microsoft Docs
description: Explique comment préparer le certificat pour infrastructure à clé publique Azure Stack pour des systèmes intégrés Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 934585082e2832c41885874c82ab43d64a1fa361
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2018
ms.locfileid: "33203474"
---
# <a name="prepare-azure-stack-pki-certificates-for-deployment"></a>Préparer des certificats PKI Azure Stack pour le déploiement
Les fichiers de certificat [obtenus auprès de l’autorité de certification de votre choix](azure-stack-get-pki-certs.md) doivent être importés et exportés avec des propriétés correspondant aux exigences de certificat d’Azure Stack.


## <a name="prepare-certificates-for-deployment"></a>Préparer les certificats pour le déploiement
Suivez ces étapes pour préparer et valider les certificats PKI Azure Stack : 

### <a name="import-the-certificate"></a>Importer le certificat

1.  Copiez les versions du certificat d’origine [obtenues auprès de l’autorité de certification de votre choix](azure-stack-get-pki-certs.md) dans un répertoire de l’ordinateur hôte de déploiement. 
  > [!WARNING]
  > Ne copiez pas les fichiers qui ont déjà été importés, exportés ou modifiés d’une façon ou d’une autre à partir des fichiers fournis directement par l’autorité de certification.

2.  Cliquez avec le bouton droit sur le certificat, puis sélectionnez **Installer le certificat** ou **Installer PFX** selon la façon dont le certificat a été remis par votre autorité de certification.

3. Dans **l’Assistant Importation de certificat**, sélectionnez **Ordinateur local** en tant qu’emplacement d’importation. Sélectionnez **Suivant**. Sur l’écran suivant, cliquez à nouveau sur Suivant.

    ![Emplacement d’importation sur l’ordinateur local](.\media\prepare-pki-certs\1.png)

4.  Sélectionnez **Placer tous les certificats dans le magasin suivant**, puis sélectionnez **Approbation de l’entreprise** comme emplacement. Cliquez sur **OK** pour fermer la boîte de dialogue de sélection du magasin de certificats, puis cliquez sur **Suivant**.

    ![Configurer le magasin de certificats](.\media\prepare-pki-certs\3.png)

    a. Si vous importez un PFX, une boîte de dialogue supplémentaire s’affiche. Sur la page **Protection de la clé privée**, entrez le mot de passe correspondant à vos fichiers de certificat, puis activez l’option **Marquer cette clé comme exportable. Cela vous permet de sauvegarder ou transporter vos clés ultérieurement**. Sélectionnez **Suivant**.

    ![Marquer la clé comme exportable](.\media\prepare-pki-certs\2.png)

5. Cliquez sur Terminer pour effectuer l’importation.

### <a name="export-the-certificate"></a>Exportation du certificat

Ouvrez la console MMC du Gestionnaire de certificats et connectez-vous au magasin de certificats de l’ordinateur Local.

1. Ouvrez Microsoft Management Console ; sous Windows 10, cliquez avec le bouton droit sur le menu Démarrer, puis cliquez sur Exécuter. Tapez **mmc** et cliquez sur OK.

2. Cliquez sur Fichier, Ajouter/supprimer un composant logiciel enfichable, puis sélectionnez Certificats et cliquez sur Ajouter.

    ![Ajouter un composant logiciel enfichable Certificats](.\media\prepare-pki-certs\mmc-2.png)
 
3. Sélectionnez Compte d’ordinateur et cliquez sur Suivant ; ensuite, sélectionnez Ordinateur Local, puis Terminer. Cliquez sur OK pour fermer la page Ajouter/Supprimer un composant logiciel enfichable.

    ![Ajouter un composant logiciel enfichable Certificats](.\media\prepare-pki-certs\mmc-3.png)

4. Accédez à Certificats > Confiance de l’entreprise > Emplacement des certificats. Vérifiez que votre certificat apparaît sur la droite.

5. Dans la barre des tâches de la console Gestionnaire de certificats, sélectionnez **Actions** > **Toutes les tâches** > **Exporter**. Sélectionnez **Suivant**.

  > [!NOTE]
  > Selon le nombre de certificats Azure Stack, vous devrez peut-être suivre cette procédure plusieurs fois.

4. Sélectionnez **Oui, exporter la clé privée**, puis cliquez sur **Suivant**.

5. Dans la section Format de fichier d’exportation, sélectionnez **Exporter toutes les propriétés étendues**, puis cliquez sur **Suivant**.

6. Sélectionnez **Mot de passe** et indiquez un mot de passe pour les certificats. N’oubliez pas ce mot de passe car il sera utilisé comme paramètre de déploiement. Sélectionnez **Suivant**.

7. Choisissez un nom de fichier et l’emplacement du fichier pfx à exporter. Sélectionnez **Suivant**.

8. Sélectionnez **Terminer**.

## <a name="next-steps"></a>Étapes suivantes
[Validate PKI certificates](azure-stack-validate-pki-certs.md) (Valider des certificats d’infrastructure à clé publique)