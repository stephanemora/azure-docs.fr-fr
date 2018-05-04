---
title: Connecter l’Explorateur Stockage à un compte de stockage ou abonnement Azure Stack | Microsoft Docs
description: Découvrez comment connecter l’Explorateur Stockage à un abonnement Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/20/2018
ms.author: mattbriggs
ms.reviewer: xiaofmao
ms.openlocfilehash: 8b670ec7040aab7eca26d411c9e31a934052be19
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2018
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription-or-a-storage-account"></a>Connecter l’Explorateur Stockage à un compte de stockage ou abonnement Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

L’Explorateur Stockage Azure est une application autonome qui vous permet d’utiliser facilement les données de stockage Azure Stack sur Windows, macOS et Linux. Il existe plusieurs outils qui permettent de déplacer des données vers et à partir du stockage Azure Stack. Pour plus d’informations, consultez [Outils de transfert de données pour le stockage Azure Stack](azure-stack-storage-transfer.md).

Dans cet article, vous découvrez comment vous connecter à vos comptes de stockage et abonnements Azure Stack à l’aide de l’Explorateur Stockage. 

Si vous n’avez pas installé l’Explorateur Stockage, [téléchargez](http://www.storageexplorer.com/)-le et installez-le.

Une fois que vous êtes connecté à votre compte de stockage ou abonnement Azure Stack, vous pouvez consulter les [articles sur l’Explorateur Stockage Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) pour utiliser vos données Azure Stack. 

## <a name="prepare-for-connecting-to-azure-stack"></a>Se préparer à la connexion à Azure Stack

Vous avez besoin d’un accès direct à Azure Stack ou à une connexion VPN pour que l’Explorateur Stockage puisse accéder à l’abonnement Azure Stack. Pour savoir comment configurer une connexion VPN à Azure Stack, consultez [Connect to Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) (Se connecter à Azure Stack).

Pour le Kit de développement Azure Stack, vous devez exporter le certificat racine d’autorité Azure Stack.

### <a name="export-and-then-import-the-azure-stack-certificate"></a>Exporter et importer le certificat Azure Stack

1. Ouvrez `mmc.exe` sur une machine hôte Azure Stack ou sur une machine locale avec une connexion VPN à Azure Stack. 

2. Dans **Fichier**, sélectionnez **Ajouter/Supprimer un composant logiciel enfichable**, ajoutez **Certificats** pour gérer **Mon compte utilisateur**.

3. Sous **Racine de la console\Certificats (ordinateur local)\Autorités de certification racines de confiance\Certificats**, recherchez **AzureStackSelfSignedRootCert**.

    ![Charger le certificat racine Azure Stack via mmc.exe](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

4. Cliquez avec le bouton droit sur le certificat, sélectionnez **Toutes les tâches** > **Exporter**, puis suivez les instructions pour exporter le certificat avec **X.509 encodé en base 64 (.cer)**.  

    Le certificat exporté sera utilisé à l’étape suivante.

5. Démarrez l’Explorateur Stockage et, si vous voyez la boîte de dialogue **Connexion au stockage Azure**, cliquez sur Annuler.

6. Dans le menu **Modifier**, pointez sur **Certificats SSL**, puis sélectionnez **Importer les certificats**. Utilisez la boîte de dialogue du sélecteur de fichier pour rechercher et ouvrir le certificat que vous avez exporté à l’étape précédente.

    Vous êtes invité à redémarrer l’Explorateur de stockage suite à l’importation.

    ![Importer le certificat dans l’Explorateur Stockage](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

7. Après le redémarrage de l’Explorateur Stockage, sélectionnez le menu **Modifier** et vérifiez que l’option **Cibler Azure Stack** est sélectionnée. Si l’option n’est pas sélectionnée, sélectionnez-la et redémarrez l’Explorateur de stockage pour appliquer la modification. Cette configuration est requise pour la compatibilité avec votre environnement Azure Stack.

    ![S’assurer que l’option Target Azure Stack (Cibler Azure Stack) est sélectionnée](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-subscription"></a>Connexion à un abonnement Azure Stack

Vous êtes maintenant prêts à connecter l’Explorateur Stockage à un abonnement Azure Stack.

1. Dans le volet gauche de l’Explorateur Stockage, sélectionnez **Gérer les comptes**.  
    Tous les abonnements Microsoft auxquels vous êtes connecté s’affichent.

2. Pour vous connecter à l’abonnement Azure Stack, sélectionnez **Ajouter un compte**.

    ![Ajouter un compte Azure Stack](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. Dans la boîte de dialogue Se connecter au Stockage Azure, sous **Environnement Azure**, sélectionnez **Azure** ou **Azure - Chine**, selon le compte Azure Stack utilisé, puis sélectionnez **Se connecter**. Pour vous connecter au compte Azure Stack associé à au moins un abonnement Azure Stack actif.

    ![Se connecter au Stockage Azure](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. Une fois que vous êtes connecté avec un compte Azure Stack, le volet gauche est renseigné avec les abonnements Azure Stack associés à ce compte. Sélectionnez les abonnements Azure Stack que vous souhaitez utiliser, puis cliquez sur Appliquer. (La case à cocher Tous les abonnements permet de sélectionner ou de désélectionner l’ensemble des abonnements Azure Stack répertoriés.)

    ![Sélectionner les abonnements Azure Stack après avoir renseigné la boîte de dialogue Custom Cloud Environment (Environnement cloud personnalisé)](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)  

    Le volet de gauche affiche les comptes de stockage associés aux abonnements Azure Stack sélectionnés.

    ![Liste des comptes de stockage, y compris les comptes d’abonnement Azure Stack](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-storage-account"></a>Se connecter à un compte de stockage Azure Stack

Vous pouvez également vous connecter à un compte de stockage Azure Stack à l’aide du nom de compte de stockage et de la paire de clés.

1.  Dans le volet gauche de l’Explorateur Stockage, sélectionnez Gérer les comptes. Tous les comptes Microsoft auxquels vous êtes connecté sont affichés.

    ![Ajouter un compte](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2.  Pour vous connecter à l’abonnement Azure Stack, sélectionnez **Ajouter un compte**.
 
    ![Ajouter un compte](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3.  Dans la boîte de dialogue Se connecter au Stockage Azure, sélectionnez **Utiliser un nom et une clé de compte de stockage**.

4. Entrez le nom de votre compte dans le champ **Nom du compte**, collez la clé de compte dans la zone de texte **Clé du compte**, sélectionnez **Other (enter below)** (Autre (spécifiez ci-dessous)) dans **Domaine des points de terminaison de stockage** et indiquez le point de terminaison Azure Stack.  

    Un point de terminaison Azure Stack comprend deux parties : le nom d’une région et le domaine Azure Stack. Dans le Kit de développement Azure Stack, le point de terminaison par défaut est **local.azurestack.external**. Contactez votre administrateur cloud si vous n’êtes pas sûr de votre point de terminaison.

    ![Joindre un nom et une clé](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5.  Sélectionnez **Connecter**.
6.  Une fois le compte de stockage joint, il s’affiche avec (**Externe, Autre**) ajouté à son nom.

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>Étapes suivantes
* [Prise en main de l’Explorateur Stockage](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Stockage Azure Stack : Différences et considérations](azure-stack-acs-differences.md)
* Pour en savoir plus sur le stockage Azure, consultez [Présentation du stockage Microsoft Azure](../../storage/common/storage-introduction.md)
