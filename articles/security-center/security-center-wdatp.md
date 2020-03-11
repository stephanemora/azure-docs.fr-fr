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
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: 13852acb39a420e2f0da84e18bef4df823c1fa78
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206263"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Microsoft Defender Advanced Threat Protection avec Azure Security Center

Azure Security Center étend son offre de plateformes de protection de charge de travail cloud en s’intégrant à [Microsoft Defender Advanced Threat Protection](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) (ATP).
Cette modification apporte des fonctionnalités complètes de détection du point de terminaison et de réponse (EDR). L’intégration de Microsoft Defender ATP vous permet de repérer les anomalies. Vous pouvez également détecter les attaques avancées sur les points de terminaison de serveur surveillés par Azure Security Center et y répondre.

## <a name="microsoft-defender-atp-features-in-security-center"></a>Fonctionnalités de Microsoft Defender ATP dans Security Center

Quand vous utilisez Microsoft Defender ATP, vous disposez des fonctionnalités suivantes :

- **Capteurs de détection des violations avancés** : les capteurs Microsoft Defender ATP pour serveurs Windows collectent un large éventail de signaux comportementaux.

- **Détection des violations basée sur des analyses dans le cloud** : Microsoft Defender ATP s’adapte rapidement à l’évolution des menaces. Elle utilise l’analytique avancée et le Big Data. Microsoft Defender ATP est amplifié par la puissance d’Intelligent Security Graph avec des signaux à travers Windows, Azure et Office pour détecter les menaces inconnues. Il fournit des alertes actionnables et vous permet de réagir rapidement.

- **Informations sur les menaces** : Microsoft Defender ATP génère des alertes quand il identifie les outils, les techniques et les procédures de l’attaquant. Il utilise les données générées par les chasseurs de menaces de Microsoft et les équipes de sécurité, complétées par les renseignements fournis par les partenaires.

Les fonctionnalités suivantes sont maintenant disponibles dans Azure Security Center :

- **Intégration automatisée** : le capteur Microsoft Defender ATP est automatiquement activé pour les serveurs Windows intégrés à Azure Security Center.

- **Volet unique** : la console Azure Security Center affiche des alertes Microsoft Defender ATP.

- **Examen détaillé de la machine** : les clients Azure Security Center peuvent utiliser la console Microsoft Defender ATP pour effectuer un examen détaillé et découvrir l’étendue d’une violation.

![Azure Security Center affiche une liste d’alertes et des informations générales sur chacune d’elles.](media/security-center-wdatp/image1.png)

Pour approfondir vos recherches, utilisez Microsoft Defender ATP. Microsoft Defender ATP fournit des informations supplémentaires telles que l’arborescence des processus d’alerte et le graphe d’incident. Vous pouvez également voir une chronologie détaillée de la machine, qui indique tous les comportements pour un historique pouvant s’étendre sur six mois.

![Page Microsoft Defender ATP avec des informations détaillées sur une alerte](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Plateforme prise en charge

Microsoft Defender ATP dans Security Center prend en charge la détection sur Windows Server 2016, 2012 R2 et 2008 R2 SP1. Pour les machines virtuelles Azure, vous avez besoin d’un abonnement de niveau standard ; pour les autres, il vous faut un niveau standard pour l’espace de travail uniquement.

> [!NOTE]
> Quand vous utilisez Azure Security Center pour superviser les serveurs, un locataire Microsoft Defender ATP est automatiquement créé et les données Microsoft Defender ATP sont stockées dans la région Europe par défaut. Si vous avez besoin de déplacer des données vers un autre emplacement, vous devez contacter le Support Microsoft pour réinitialiser le locataire. La supervision des points de terminaison de serveur qui utilise cette intégration a été désactivée pour les clients Office 365 GCC.

## <a name="onboarding-servers-to-security-center"></a>Intégration de serveurs à Security Center 

Pour intégrer des serveurs à Security Center, cliquez sur **Accéder à Azure Security Center pour intégrer des serveurs** dans la page d’intégration de serveur Microsoft Defender ATP.

1. Dans la zone **Intégration**, sélectionnez ou créez un espace de travail dans lequel stocker les données. <br>
2. Si vous ne voyez pas tous vos espaces de travail, cela peut être dû à un manque d’autorisations. Vérifiez que votre espace de travail est défini sur le niveau Standard d’Azure Security Center. Pour plus d’informations, consultez [Passer au niveau tarifaire Standard de Security Center pour une sécurité renforcée](security-center-pricing.md).
    
3. Sélectionnez **Ajouter des serveurs** pour afficher des instructions sur la façon d’installer Microsoft Monitoring Agent. 

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

Suivez les instructions données dans [Attribuer l’accès utilisateur au portail](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

## <a name="set-the-firewall-configuration"></a>Définir la configuration du pare-feu

Si vous avez un proxy ou un pare-feu qui bloque le trafic anonyme, comme un capteur Microsoft Defender ATP se connecte à partir du contexte système, vérifiez que le trafic anonyme est autorisé. Suivez les instructions mentionnées dans [Activer l’accès aux URL du service Microsoft Defender ATP dans le serveur proxy](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>Tester la fonctionnalité

Pour générer une alerte de test Microsoft Defender ATP inoffensive :

1. Créez un dossier « C:\test-MDATP-test ».

1. Utilisez le Bureau à distance pour accéder à une machine virtuelle Windows Server 2012 R2 ou à une machine virtuelle Windows Server 2016. Ouvrez une fenêtre de ligne de commande.

1. A l'invite, copiez et exécutez la commande suivante. La fenêtre d’invite de commandes se ferme automatiquement.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```

   ![Une fenêtre d’invite de commandes avec la commande ci-dessus](media/security-center-wdatp/image4.jpeg)

3. Si la commande réussit, une nouvelle alerte s’affiche sur le tableau de bord Azure Security Center et sur le portail Microsoft Defender ATP. L’alerte peut mettre quelques minutes à s’afficher.

4. Pour examiner l’alerte dans Security Center, accédez à **Alertes de sécurité** > **Ligne de commande PowerShell suspecte**.

5. Dans la fenêtre d’enquête, sélectionnez le lien d’accès au portail Microsoft Defender ATP.

## <a name="next-steps"></a>Étapes suivantes

- [Plateformes et fonctionnalités prises en charge par Azure Security Center](security-center-os-coverage.md)
- [Définition de stratégies de sécurité dans Azure Security Center](tutorial-security-policy.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
- [Gestion des suggestions de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez la façon dont les suggestions vous aident à protéger vos ressources Azure.
- [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
