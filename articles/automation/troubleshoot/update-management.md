---
title: Résoudre les erreurs avec Update Management
description: Découvrez comment résoudre les problèmes rencontrés avec Update Management.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: b77d1210ff48a4bd30834fcbad64173bf77b1290
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064387"
---
# <a name="troubleshooting-issues-with-update-management"></a>Résolution des problèmes rencontrés avec Update Management

Cet article traite des solutions pour résoudre les problèmes que vous pouvez rencontrer lors de l’utilisation d’Update Management.

## <a name="windows"></a>Windows

Si vous rencontrez des problèmes quand vous essayez d’intégrer la solution sur une machine virtuelle, recherchez dans le journal des évènements **Operations Manager** sous **Journaux des applications et des services\** sur la machine locale des événements avec l’ID d’événement **4502** et un message d’événement contenant **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**.

La section suivante met en évidence des messages d’erreur spécifiques et une résolution possible pour chacun d’eux. Pour d’autres problèmes d’intégration, consultez [Résoudre les problèmes d’intégration des solutions](onboarding.md).

### <a name="machine-already-registered"></a>Scénario : La machine est déjà inscrite sur un autre compte

#### <a name="issue"></a>Problème

Vous recevez le message d’erreur suivant :

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Cause :

La machine est déjà intégrée à un autre espace de travail pour Update Management.

#### <a name="resolution"></a>Résolution :

Éliminez les anciens artefacts sur la machine en [supprimant le groupe de Runbooks hybrides](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group), puis réessayez.

### <a name="machine-unable-to-communicate"></a>Scénario : La machine ne peut pas communiquer avec le service

#### <a name="issue"></a>Problème

Vous recevez un des messages d’erreur suivants :

```
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

#### <a name="cause"></a>Cause :

Il est possible qu’un proxy, une passerelle ou un pare-feu bloque la communication réseau.

#### <a name="resolution"></a>Résolution :

Passez en revue votre réseau, et vérifiez que les ports et les adresses appropriés sont autorisés. Consultez [Configuration réseau requise](../automation-hybrid-runbook-worker.md#network-planning) pour obtenir la liste des ports et des adresses nécessaires pour Update Management et pour les Runbooks Worker hybrides.

### <a name="unable-to-create-selfsigned-cert"></a>Scénario : Impossible de créer un certificat auto-signé

#### <a name="issue"></a>Problème

Vous recevez un des messages d’erreur suivants :

```
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Cause :

Le Runbook Worker hybride n’a pas pu générer un certificat auto-signé.

#### <a name="resolution"></a>Résolution :

Vérifiez que le compte système a accès en lecture au dossier **C:\ProgramData\Microsoft\Crypto\RSA**, puis réessayez.

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Scénario : Le démarrage de l’exécution de la mise à jour échoue

#### <a name="issue"></a>Problème

Le démarrage de l’exécution d’une mise à jour échoue sur une machine Linux.

#### <a name="cause"></a>Cause :

Le Worker hybride Linux est défectueux.

#### <a name="resolution"></a>Résolution :

Faites une copie du fichier journal suivant et conservez-le pour résoudre les problèmes :

```
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Scénario : L’exécution de la mise à jour démarre, mais rencontre des erreurs

#### <a name="issue"></a>Problème

L’exécution d’une mise à jour démarre, mais rencontre des erreurs pendant l’exécution.

#### <a name="cause"></a>Cause :

Les causes possibles sont :

* Le Gestionnaire de package n’est pas sain
* Des packages spécifiques peuvent interférer avec la mise à jour corrective cloud
* Autres raisons

#### <a name="resolution"></a>Résolution :

Si des échecs se produisent pendant l’exécution d’une mise à jour après qu’elle a démarré avec succès sur Linux, vérifiez la sortie du travail de la machine affectée dans l’exécution. Vous pouvez trouver des messages d’erreur spécifiques provenant du Gestionnaire de package de votre machine, effectuer des recherches sur ces erreurs et entreprendre des actions pour les résoudre. Update Management nécessite que le Gestionnaire de package soit sain pour que les déploiements des mises à jour réussisse.

Dans certains cas, les mises à jour de package peuvent interférer avec Update Management, empêchant le déploiement correct d’une mise à jour. Si vous constatez cela, vous devez exclure ces packages des exécutions de mise à jour futures ou les installer manuellement vous-même.

Si vous ne pouvez pas résoudre un problème de mise à jour corrective, pour pouvoir résoudre le problème, faites une copie du fichier journal suivant et enregistrez-le **avant** le démarrage du déploiement de mise à jour suivant :

```
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure via les [Forums Windows](https://azure.microsoft.com/support/forums/)
* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), qui est le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées : réponses, support technique et experts.
* Si vous avez besoin de plus d’aide, vous pouvez signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.