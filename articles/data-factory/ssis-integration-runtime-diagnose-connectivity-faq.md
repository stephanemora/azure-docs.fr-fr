---
title: Résoudre les problèmes de connectivité de diagnostic dans le runtime d’intégration SSIS
description: Cet article fournit des instructions pour la résolution des problèmes de connectivité de diagnostic dans le runtime d’intégration SSIS
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
manager: yidetu
ms.date: 06/07/2020
ms.openlocfilehash: 8e520048a6067f134e847953f4f4aa0598d9926e
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86172425"
---
# <a name="troubleshoot-diagnose-connectivity-in-the-ssis-integration-runtime"></a>Résoudre les problèmes de connectivité de diagnostic dans le runtime d’intégration SSIS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Si vous rencontrez des problèmes de connectivité lors de l’exécution de packages SSIS dans le runtime d’intégration SSIS, en particulier si votre runtime d’intégration SSIS a rejoint le réseau virtuel Azure. Vous pouvez essayer de diagnostiquer vous-même les problèmes à l’aide de la fonctionnalité de connectivité de diagnostic à la page du runtime d’intégration SSIS du portail Azure Data Factory. 

 ![Surveiller la page : diagnostiquer la connectivité](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png) ![Surveiller la page : tester la connexion](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)
 
Cet article contient des erreurs les plus courantes que vous pouvez rencontrer quand vous testez la connexion dans le runtime d’intégration SSIS. Il décrit les causes possibles et les actions permettant de résoudre les erreurs. 

## <a name="common-errors-potential-causes-and-recommendation-solutions"></a>Erreurs courantes, causes potentielles et recommandations

### <a name="error-code-invalidinput"></a>Code d’erreur : InvalidInput.
* **Message d’erreur** : Vérifiez que votre entrée est correcte.
* **Causes potentielles :** Votre entrée est incorrecte.
* **Recommandation :** Vérifiez votre entrée.

### <a name="error-code-firewallornetworkissue"></a>Code d’erreur : FirewallOrNetworkIssue.
* **Message d’erreur** : Vérifiez que ce port est ouvert sur votre pare-feu/serveur/NSG et que le réseau est stable.
* **Causes potentielles :** 
  * Votre serveur n’ouvre pas ce port.
  * Le trafic sortant est refusé à votre groupe de sécurité réseau sur ce port.
  * Votre pare-feu appliance virtuelle réseau/Azure/local n’ouvre pas ce port.
* **Recommandation :** 
  * Ouvrez ce port sur le serveur.
  * Mettez à jour le groupe de sécurité réseau pour autoriser le trafic sortant sur ce port.
  * Ouvrez ce port sur le pare-feu appliance virtuelle réseau/Azure/pare-feu local.

### <a name="error-code-misconfigureddnssettings"></a>Code d’erreur : MisconfiguredDnsSettings.
* **Message d’erreur** : Si vous utilisez votre propre serveur DNS dans le réseau virtuel rejoint par votre Azure-SSIS IR, vérifiez qu’il peut résoudre votre nom d’hôte.
* **Causes potentielles :** 
  *  Problème de DNS personnalisé
  *  Vous n’utilisez pas un nom de domaine complet (FQDN) pour votre nom d’hôte privé
* **Recommandation :** 
  *  Corrigez votre problème de DNS personnalisé pour vous assurer qu’il peut résoudre le nom d’hôte.
  *  Utilisez un nom de domaine complet (FQDN) pour le nom de votre hôte privé, par exemple, utilisez <your_private_server>.contoso.com au lieu de <your_private_server>, car Azure-SSIS IR n’ajoute pas automatiquement votre propre suffixe DNS.

### <a name="error-code-servernotallowremoteconenction"></a>Code d’erreur : ServerNotAllowRemoteConnection.
* **Message d’erreur** : Vérifiez que votre serveur autorise les connexions TCP distantes via ce port.
* **Causes potentielles :** 
  *  Le pare-feu de votre serveur n’autorise pas les connexions TCP distantes.
  *  Votre serveur n'est pas connecté.
* **Recommandation :** 
  *  Autorisez les connexions TCP distantes sur le pare-feu du serveur.
  *  Démarrez le serveur.
   
### <a name="error-code-misconfigurednsgsettings"></a>Code d’erreur : MisconfiguredNsgSettings.
* **Message d’erreur** : Vérifiez que le groupe de sécurité réseau de votre réseau virtuel autorise le trafic sortant via ce port. Si vous utilisez Azure ExpressRoute et/ou un itinéraire défini par l’utilisateur, vérifiez que ce port est ouvert sur votre pare-feu/serveur.
* **Causes potentielles :** 
  *  Le trafic sortant est refusé à votre groupe de sécurité réseau sur ce port.
  *  Votre pare-feu appliance virtuelle réseau/Azure/local n’ouvre pas ce port.
* **Recommandation :** 
  *  Mettez à jour le groupe de sécurité réseau pour autoriser le trafic sortant sur ce port.
  *  Ouvrez ce port sur le pare-feu appliance virtuelle réseau/Azure/pare-feu local.

### <a name="error-code-genericissues"></a>Code d’erreur : GenericIssues.
* **Message d’erreur** : Échec du test de connexion en raison de problèmes génériques.
* **Causes potentielles :** Le test de connexion a rencontré un problème temporaire général.
* **Recommandation :** Réessayez de faire le test de connexion ultérieurement. Si une nouvelle tentative échoue, contactez l’équipe du support Azure Data Factory.


### <a name="error-code-pspingexecutiontimeout"></a>Code d’erreur : PSPingExecutionTimeout.
* **Message d’erreur** : Expiration du délai d’attente du test de connexion, réessayez ultérieurement.
* **Causes potentielles :** Expiration du délai d’attente du test de connectivité.
* **Recommandation :** Réessayez de faire le test de connexion ultérieurement. Si une nouvelle tentative échoue, contactez l’équipe du support Azure Data Factory.

### <a name="error-code-networkinstable"></a>Code d’erreur : NetworkInstable.
* **Message d’erreur** : Le test de connexion a échoué de façon irrégulière en raison d’une instabilité du réseau.
* **Causes potentielles :** Problème réseau temporaire.
* **Recommandation :** Vérifiez que le réseau du serveur ou du pare-feu est stable.

## <a name="next-steps"></a>Étapes suivantes

- Déployez vos packages. Pour plus d’informations, voir [Déployer un projet SSIS dans Azure avec SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms).
- Exécutez vos packages. Pour plus d’informations, voir [Exécuter des packages SSIS sur Azure avec SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms).
- Planifiez vos packages. Pour plus d’informations, consultez [Planifier des packages SSIS dans Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15).

