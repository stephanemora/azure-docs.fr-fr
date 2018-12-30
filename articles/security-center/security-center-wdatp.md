---
title: Windows Defender Advanced Threat Protection avec Azure Security Center
description: Ce document présente l’intégration entre Azure Security Center et Windows Defender Advanced Threat Protection.
services: security-center
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: barclayn
ms.openlocfilehash: 1109e72d00ccddd6fc0f120ee71c88adaae72fed
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53337595"
---
# <a name="windows-defender-advanced-threat-protection-with-azure-security-center"></a>Windows Defender Advanced Threat Protection avec Azure Security Center

Azure Security Center étend son offre de plateformes de protection de charge de travail cloud en s’intégrant avec [Windows Defender Advanced Threat Protection](https://www.microsoft.com/en-us/WindowsForBusiness/windows-atp) (ATP).
Cette modification apporte des fonctionnalités complètes de détection du point de terminaison et de réponse (EDR). L’intégration de Windows Defender ATP vous permet de repérer les anomalies. Vous pouvez également détecter les attaques avancées sur les points de terminaison de serveur surveillés par Azure Security Center et y répondre.

Les clients Azure Security Center peuvent désormais utiliser les fonctionnalités de Windows Defender ATP :

- **Capteurs de détection des violations nouvelle génération** : les capteurs Windows Defender ATP pour serveurs Windows collectent un vaste tableau de signaux de comportement.

- **Détection des violations basée sur des analyses dans le cloud** : Windows Defender ATP s'adapte rapidement aux menaces changeantes. Elle utilise l’analytique avancée et le Big Data. Windows Defender ATP est amplifié par la puissance d’Intelligent Security Graph avec des signaux à travers Windows, Azure et Office pour détecter les menaces inconnues. Il fournit des alertes exploitables et vous permet de réagir rapidement.

- **Informations sur les menaces** : Windows Defender ATP identifie les outils, les techniques et les procédures de l’attaquant. Lorsqu’il les détecte ces, il génère des alertes. Il utilise les données générées par les chasseurs de menaces de Microsoft et les équipes de sécurité, complétées par les renseignements fournis par les partenaires.

Ces nouvelles fonctionnalités sont maintenant disponibles dans Azure Security Center :

- **Intégration automatisée** : le capteur Windows Defender ATP est automatiquement activé pour les serveurs Windows qui sont intégrés à Azure Security Center.

- **Volet unique** : la console Azure Security Center affiche des alertes Windows Defender ATP.

- **Examen détaillé de la machine** : les clients d'Azure Security Center peuvent accéder à la console Windows Defender ATP pour effectuer un examen détaillé et découvrir l'étendue d'une violation.

![Azure Security Center affiche une liste d’alertes et des informations générales sur chacune d’elles.](media/security-center-wdatp/image1.png)

Vous pouvez [examiner l’alerte](security-center-investigation.md) dans Azure Security Center :

![Tableau de bord d’examen des alertes dans Azure Security Center](media/security-center-wdatp/image2.png)

Vous pouvez également examiner l’alerte en vous servant de Windows Defender ATP. Vous pouvez y voir des informations supplémentaires telles que l’arborescence des processus d’alerte et le graphique d’incident. Vous pouvez également voir une chronologie détaillée de la machine, qui indique tous les comportements pour un historique pouvant s’étendre sur six mois.

![Page Windows Defender ATP avec des informations détaillées sur une alerte](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Prise en charge de plateformes

Cette fonctionnalité prend en charge la détection sur Windows Server 2012 R2 et Windows Server 2016.

Seuls les serveurs des abonnements pour le niveau de service standard sont pris en charge.

## <a name="onboarding-instructions"></a>Instructions d'intégration

Pour afficher si l’intégration de Windows Defender ATP est activée, sélectionnez **Security Center** > **Stratégie de sécurité** > **Abonnement**  >  **Modifier les paramètres**.

  ![Gestion des stratégies d’Azure Security Center](media/security-center-wdatp/policy-management.png)

Vous pouvez voir ici les intégrations actuellement activées.

  ![Page de paramètres de détection de menaces Azure Security Center avec intégration de Windows Defender ATP activée](media/security-center-wdatp/enable-integrations.png)

- Si vous avez déjà intégré les serveurs vers le niveau standard d’Azure Security Center, vous n’avez pas besoin de prendre d’autres mesures. Azure Security Center intégrera automatiquement les serveurs dans Windows Defender ATP. Ceci peut prendre jusqu’à 24 heures.

- Si vous n'avez jamais intégré les serveurs au niveau standard d'Azure Security Center, intégrez-les comme d'habitude dans Azure Security Center.

- Si vous avez intégré les serveurs via Windows Defender ATP :
  - Reportez-vous à la documentation pour obtenir des conseils sur [la manière d’annuler l’intégration des machines serveur](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Intégrer ces serveurs à Azure Security Center.

## <a name="access-to-the-windows-defender-atp-portal"></a>Accéder au portail Windows Defender ATP

Suivez les instructions données dans [Attribuer l’accès utilisateur au portail](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/assign-portal-access-windows-defender-advanced-threat-protection).

## <a name="set-the-firewall-configuration"></a>Définir la configuration du pare-feu

Si vous avez un proxy ou un pare-feu qui bloque le trafic anonyme, comme un capteur ATP Windows Defender se connecte depuis le contexte système, assurez-vous que le trafic anonyme est autorisé. Suivez les instructions données dans [Activer l’accès aux URL du service Windows Defender ATP dans le serveur proxy](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/configure-proxy-internet-windows-defender-advanced-threat-protection#enable-access-to-windows-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>Tester la fonctionnalité

Pour générer une alerte de test de Windows Defender ATP inoffensive :

1. Utilisez le Bureau à distance pour accéder à une machine virtuelle Windows Server 2012 R2 ou à une machine virtuelle Windows Server 2016.  Ouvrez une fenêtre d’invite de commandes.

2. A l'invite, copiez et exécutez la commande suivante. La fenêtre d’invite de commandes se ferme automatiquement.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-WDATP-test\\invoice.exe'); Start-Process 'C:\\test-WDATP-test\\invoice.exe'
    ```

   ![Une fenêtre d’invite de commandes avec la commande ci-dessus](media/security-center-wdatp/image4.jpeg)

3. Si la commande réussit, vous verrez une nouvelle alerte sur le tableau de bord d’Azure Security Center et sur le portail Windows Defender ATP. L’alerte peut mettre quelques minutes à s’afficher.

4. Pour examiner l’alerte dans Security Center, accédez à **Alertes de sécurité** >  **Ligne de commande Powershell suspecte**.

5. Dans la fenêtre d’enquête, sélectionnez le lien d’accès au portail Windows Defender ATP.

## <a name="next-steps"></a>Étapes suivantes

- [Définition de stratégies de sécurité dans Azure Security Center](tutorial-security-policy.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
- [Gestion des suggestions de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez la façon dont les suggestions vous aident à protéger vos ressources Azure.
- [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
