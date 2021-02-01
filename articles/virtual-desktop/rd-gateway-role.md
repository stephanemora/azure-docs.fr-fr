---
title: Déployer le rôle Passerelle des services Bureau à distance Windows Virtual Desktop - Azure
description: Comment déployer le rôle Passerelle des services Bureau à distance Windows Virtual Desktop - Azure
author: Heidilohr
ms.topic: how-to
ms.date: 01/26/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: ca3c36f339d5cbca6d5b5990415fba180ae42a13
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98798001"
---
# <a name="deploy-the-rd-gateway-role-in-windows-virtual-desktop"></a>Déployer le rôle Passerelle des services Bureau à distance Windows Virtual Desktop

Cet article vous indique comment déployer les serveurs de passerelle Bureau à distance dans votre environnement. Vous pouvez installer les rôles serveur sur des ordinateurs physiques ou des machines virtuelles, selon que vous créez un environnement local, hybride ou basé sur le cloud.

## <a name="install-the-rd-gateway-role"></a>Installer le rôle Passerelle des services Bureau à distance

1. Connectez-vous au serveur cible avec les informations d’identification d’administration.

2. Dans le **Gestionnaire de serveur**, sélectionnez **Gérer**, puis **Ajouter des rôles et fonctionnalités**. Le programme d'installation **Ajouter des rôles et des fonctionnalités** s’ouvre.

3. Dans la page **Avant de commencer**, cliquez sur **Suivant**.

4. Dans **Sélectionner le type d’installation**, sélectionnez **Installation basée sur un rôle** ou une fonctionnalité, puis cliquez sur **Suivant**.

5. Pour **Sélectionner le serveur de destination**, choisissez **Sélectionner un serveur du pool de serveurs**. Pour **Pool de serveurs**, sélectionnez le nom de votre ordinateur local. Quand vous avez terminé, sélectionnez **Suivant**.

6. Dans **Sélectionner des rôles de serveurs** > **Rôles**, sélectionnez **Services Bureau à distance**. Quand vous avez terminé, sélectionnez **Suivant**.

7. Dans **Services Bureau à distance**, sélectionnez **Suivant.**

8. Pour **Sélectionner des services de rôle**, sélectionnez uniquement **Passerelle des services Bureau à distance** ; lorsque vous êtes invité à ajouter les fonctionnalités requises, sélectionnez **Ajouter des fonctionnalités**. Quand vous avez terminé, sélectionnez **Suivant**.

9. Pour **Services de rôle pour les services de stratégie et d’accès réseau**, sélectionnez **Suivant**.

10. Pour **Rôle Web Server (IIS)** , sélectionnez **Suivant**.

11. Pour **Sélectionner des services de rôle**, sélectionnez **Suivant**.

12. Pour **Confirmer les sélections pour l’installation**, sélectionnez **Installer**. Ne fermez pas le programme d’installation pendant la procédure d’installation.

## <a name="configure-rd-gateway-role"></a>Configurer le rôle Passerelle des services Bureau à distance

Une fois le rôle Passerelle des services Bureau à distance installé, vous devez le configurer.

Pour configurer le rôle Passerelle des services Bureau à distance :

1. Ouvrez le **Gestionnaire de serveur**, puis sélectionnez **Services Bureau à distance**.

2. Accédez à **Serveurs**, cliquez avec le bouton droit sur le nom de votre serveur, puis sélectionnez **RD Gateway Manager**.

3. Dans RD Gateway Manager, cliquez avec le bouton droit sur le nom de votre passerelle, puis sélectionnez **Propriétés**.

4. Ouvrez l’onglet **Certificat SSL**, sélectionnez la bulle **Importer un certificat dans la passerelle Bureau à distance**, puis sélectionnez **Parcourir et importer un certificat...** .

5. Sélectionnez le nom de votre fichier PFX, puis **Ouvrir**.

6. Entrez le mot de passe du fichier PFX quand vous y êtes invité.

7. Une fois que vous avez importé le certificat et sa clé privée, l’affichage doit afficher les attributs de clé du certificat.

>[!NOTE]
>Étant donné que le rôle Passerelle des services Bureau à distance est supposé être public, nous vous recommandons d’utiliser un certificat émis publiquement. Si vous utilisez un certificat délivré par un organisme privé, vous devez vous assurer de configurer au préalable tous les clients avec la chaîne de confiance du certificat numérique.

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez ajouter la haute disponibilité à votre rôle Passerelle des services Bureau à distance, consultez [Ajouter la haute disponibilité au serveur frontal d’accès web et de passerelle des services Bureau à distance](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha).