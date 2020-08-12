---
title: Microsoft Defender Advanced Threat Protection – Azure Security Center
description: Ce document présente l’intégration entre Azure Security Center et Microsoft Defender Advanced Threat Protection.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2020
ms.author: memildin
ms.openlocfilehash: f8ed7dc2caf3ad471ea7d4bb2fc8213114aad109
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089700"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Microsoft Defender Advanced Threat Protection avec Azure Security Center

Azure Security Center s’intègre à [Microsoft Defender ATP (Advanced Threat Protection)](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) pour fournir des fonctionnalités complètes de détection de point de terminaison et réponse (EDR, Endpoint Detection and Response).

## <a name="microsoft-defender-atp-features-in-security-center"></a>Fonctionnalités de Microsoft Defender ATP dans Security Center

Microsoft Defender ATP fournit les éléments suivants :

- **Capteurs de détection des violations avancés** : les capteurs Microsoft Defender ATP pour serveurs Windows collectent un large éventail de signaux comportementaux.

- **Détection des violations basée sur des analyses dans le cloud** : Microsoft Defender ATP s’adapte rapidement à l’évolution des menaces. Elle utilise l’analytique avancée et le Big Data. Microsoft Defender ATP est amplifié par la puissance d’Intelligent Security Graph avec des signaux à travers Windows, Azure et Office pour détecter les menaces inconnues. Il fournit des alertes actionnables et vous permet de réagir rapidement.

- **Informations sur les menaces** : Microsoft Defender ATP génère des alertes quand il identifie les outils, les techniques et les procédures de l’attaquant. Il utilise les données générées par les chasseurs de menaces de Microsoft et les équipes de sécurité, complétées par les renseignements fournis par les partenaires.


L’intégration de Defender ATP à Azure Security Center vous permet également de bénéficier des fonctionnalités supplémentaires suivantes :

- **Intégration automatisée** : L’intégration active automatiquement le capteur Microsoft Defender ATP pour les serveurs Windows supervisés par Azure Security Center (à moins qu’ils n’exécutent Windows Server 2019).

- **Volet unique** : la console Azure Security Center affiche des alertes Microsoft Defender ATP. Pour approfondir vos recherches, utilisez Microsoft Defender ATP. Microsoft Defender ATP fournit des informations supplémentaires telles que l’arborescence des processus d’alerte et le graphe d’incident. Vous pouvez également voir une chronologie détaillée de la machine, qui indique tous les comportements pour un historique pouvant s’étendre sur six mois.

    ![Page Microsoft Defender ATP avec des informations détaillées sur une alerte](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Plateforme prise en charge

Microsoft Defender ATP dans Security Center prend en charge la détection sur Windows Server 2016, 2012 R2 et 2008 R2 SP1. Pour les machines virtuelles Azure, vous avez besoin d’un abonnement de niveau Standard, et pour les machines virtuelles non-Azure, vous avez besoin seulement du niveau Standard au niveau de l’espace de travail.

La supervision des points de terminaison de serveur qui utilise cette intégration a été désactivée pour les clients Office 365 GCC.

## <a name="data-storage-location"></a>Emplacement de stockage des données

Quand vous utilisez Azure Security Center pour superviser des serveurs, un locataire Microsoft Defender ATP est créé automatiquement. Les données collectées par Microsoft Defender ATP sont stockées dans la zone géographique du locataire tel qu’il est identifié lors du provisionnement. Les données des clients sous une forme pseudonymisée peuvent également être stockées dans des systèmes de stockage et de traitement centralisés aux États-Unis. 

Une fois qu’il est configuré, vous ne pouvez pas changer l’emplacement de stockage de vos données. Si vous avez besoin de déplacer des données vers un autre emplacement, contactez le Support Microsoft pour réinitialiser le locataire.


## <a name="onboarding-servers-to-security-center"></a>Intégration de serveurs à Security Center 

Pour intégrer des serveurs à Security Center, cliquez sur **Accéder à Azure Security Center pour intégrer des serveurs** dans la page d’intégration de serveur Microsoft Defender ATP.

1. Dans la zone **Intégration**, sélectionnez ou créez un espace de travail dans lequel stocker les données.

2. Si vous ne voyez pas tous vos espaces de travail, cela peut être dû à un manque d’autorisations. Vérifiez que votre espace de travail est défini sur le niveau tarifaire Standard d’Azure Security Center. Pour plus d’informations, consultez [Passer au niveau tarifaire Standard de Security Center pour une sécurité renforcée](security-center-pricing.md).
    
3. Sélectionnez **Ajouter des serveurs** pour afficher des instructions sur la façon d’installer l’agent Log Analytics. 

4. Après l’intégration, vous pouvez surveiller les machines sous **Compute et applications**.

   ![Intégrer des ordinateurs](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>Activer l’intégration de Microsoft Defender ATP

Pour voir si l’intégration de Microsoft Defender ATP est activée, sélectionnez **Security Center** > **Tarification et paramètres**> cliquez sur votre abonnement.
Vous pouvez voir ici les intégrations actuellement activées.

  ![Page de paramètres de détection de menaces Azure Security Center avec l’intégration de Microsoft Defender ATP activée](media/security-center-wdatp/enable-integrations.png)

- Si vous avez déjà intégré les serveurs vers le niveau standard d’Azure Security Center, vous n’avez pas besoin de prendre d’autres mesures. Azure Security Center intégrera automatiquement les serveurs à Microsoft Defender ATP. L’intégration peut prendre jusqu’à 24 heures.

- Si vous n'avez jamais intégré les serveurs au niveau standard d'Azure Security Center, intégrez-les comme d'habitude dans Azure Security Center.

- Si vous avez intégré les serveurs par le biais de Microsoft Defender ATP :
  - Reportez-vous à la documentation pour obtenir des conseils sur [la manière d’annuler l’intégration des machines serveur](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Intégrer ces serveurs à Azure Security Center.

## <a name="access-to-the-microsoft-defender-atp-portal"></a>Accéder au portail Microsoft Defender ATP

1. Suivez les instructions données dans [Attribuer l’accès utilisateur au portail](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

1. Vérifiez si vous avez un proxy ou un pare-feu qui bloque le trafic anonyme. Le capteur Defender ATP se connecte à partir du contexte système, de sorte que le trafic anonyme doit être autorisé. Pour garantir un accès sans entraves au portail Microsoft Defender ATP, suivez les instructions mentionnées dans [Activer l’accès aux URL du service Microsoft Defender ATP dans le serveur proxy](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>Tester la fonctionnalité

Pour générer une alerte de test Microsoft Defender ATP inoffensive :

1. Créez un dossier « C:\test-MDATP-test ».

1. Utilisez le Bureau à distance pour accéder à une machine virtuelle Windows Server 2012 R2 ou à une machine virtuelle Windows Server 2016. Ouvrez une fenêtre de ligne de commande.

1. A l'invite, copiez et exécutez la commande suivante. La fenêtre d’invite de commandes se ferme automatiquement.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```

   ![Une fenêtre d’invite de commandes avec la commande ci-dessus](media/security-center-wdatp/image4.jpeg)

1. Si la commande réussit, une nouvelle alerte s’affiche sur le tableau de bord Azure Security Center et sur le portail Microsoft Defender ATP. L’alerte peut mettre quelques minutes à s’afficher.

1. Pour examiner l’alerte dans Security Center, accédez à **Alertes de sécurité** > **Ligne de commande PowerShell suspecte**.

1. Dans la fenêtre d’enquête, sélectionnez le lien d’accès au portail Microsoft Defender ATP.

## <a name="next-steps"></a>Étapes suivantes

- [Plateformes et fonctionnalités prises en charge par Azure Security Center](security-center-os-coverage.md)
- [Définition de stratégies de sécurité dans Azure Security Center](tutorial-security-policy.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
- [Gestion des suggestions de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez la façon dont les suggestions vous aident à protéger vos ressources Azure.
- [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.