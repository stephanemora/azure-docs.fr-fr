---
title: Comment tester des artefacts de package de configuration d’invité
description: Expérience de création et de test de packages qui auditent ou appliquent des configurations à des machines.
ms.date: 07/20/2021
ms.topic: how-to
ms.openlocfilehash: 927e048f59d74b4137710c2f0a1f284adec0cdcb
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122773098"
---
# <a name="how-to-test-guest-configuration-package-artifacts"></a>Comment tester des artefacts de package de configuration d’invité

Le module PowerShell `GuestConfiguration` inclut des outils permettant d’automatiser le test d’un package de configuration en dehors d’Azure. Servez-vous de ces outils pour détecter des problèmes et effectuer des itérations rapides avant de passer à la phase de test dans un environnement connecté à Azure ou Arc.

Avant de commencer à tester, suivez toutes les étapes décrites dans [Comment configurer un environnement de création de configuration d’invité](./guest-configuration-create-setup.md), puis dans [Comment créer des artefacts de package de configuration d’invité personnalisés](./guest-configuration-create.md) afin de créer et de publier un package de configuration d’invité personnalisé.

> [!IMPORTANT]
> Les packages personnalisés qui auditent l’état d’un environnement sont généralement disponibles, mais les packages qui appliquent des configurations sont **en préversion**. **Les limites suivantes s'appliquent :**
> 
> Pour utiliser les packages de configuration d’invité qui appliquent des configurations, l’extension de configuration d’invité de machine virtuelle Azure version **1.29.24** ou ultérieure, ou l’agent Arc version **1.10.0** ou version ultérieure, sont requis.
> 
> Pour tester la création et l’application de configurations sur Linux, le module `GuestConfiguration` n’est disponible que sur Ubuntu 18, mais le package et les stratégies générés par le module peuvent être utilisés sur n’importe quelle version de distribution Linux prise en charge dans Azure ou arc.
>
> Les packages de test sur MacOS ne sont pas disponibles.

Vous pouvez tester le package à partir de votre station de travail ou de votre environnement d’intégration continue et livraison continue (CI/CD).  Le module `GuestConfiguration` comprend inclut le même agent pour votre environnement de développement que celui utilisé dans des machines avec Azure ou Arc. L’agent incluant une instance autonome de PowerShell 7.1.3 pour Windows et 7.2.0-preview.7 pour Linux, l’environnement de script où le package est testé est cohérent avec les machines que vous gérez à l’aide d’une configuration d’invité.

Le service de l’agent dans les machines avec Azure et Arc s’exécute en tant que compte « LocalSystem » dans Windows et « Root » dans Linux. Pour obtenir des résultats optimaux, exécutez les commandes ci-dessous dans un contexte de sécurité privilégié.

Pour exécuter PowerShell en tant que « LocalSystem » dans Windows, utilisez l’outil SysInternals [PSExec](/sysinternals/downloads/psexec).

Pour exécuter PowerShell en tant que « Root » dans Linux, utilisez la [commande Su](https://manpages.ubuntu.com/manpages/man1/su.1.html).

## <a name="validate-the-configuration-package-meets-requirements"></a>Valider la conformité du package de configuration aux exigences

Commencez par tester si le package de configuration est conforme aux exigences de base à l’aide de la commande `Get-GuestConfigurationPacakgeComplianceStatus `. Celle-ci vérifie les exigences de package suivantes.

- MOF est présent et valide, à l’emplacement approprié.
- Les modules/dépendances requis sont présents, de la bonne version et sans doublons.
- Le package est signé (facultatif).
- Les demandes `Test` et `Get` renvoient des informations sur l’état de conformité.

Paramètres de la cmdlet `Get-GuestConfigurationPackageComplianceStatus ` :

- **Package** : chemin d’accès ou URI du package de configuration d’invité.
- **Paramètre** : Paramètres de stratégie fournis au format Hashtable.

Lors de la première exécution de cette commande, l’agent de configuration d’invité est installé sur la machine de test dans le chemin d’accès `c:\programdata\GuestConfig\bin` sur Windows, et `/var/lib/GuestConfig/bin` sur Linux. Ce chemin d’accès n’étant pas accessible à un compte d’utilisateur, la commande nécessite une élévation de privilèges.

Exécutez la commande suivante pour tester le package :

Dans Windows, à partir d’une session PowerShell 7 avec élévation de privilèges.

```powershell
# Get the current compliance results for the local machine
Get-GuestConfigurationPackageComplianceStatus -Package ./MyConfig.zip
```

Dans Linux, en exécutant PowerShell à l’aide de sudo.

```bash
# Get the current compliance results for the local machine
sudo pwsh -command 'Get-GuestConfigurationPackageComplianceStatus -Package ./MyConfig.zip'
```

La sortie de la commande est un objet contenant l’état de conformité et des détails par ressource.

```powershell
  complianceStatus  resources
  ----------------  ---------
  True              @{BuiltInAccount=localSystem; ConfigurationName=MyConfig; Credential=; Dependencies=System.Obje…
```

#### <a name="test-the-configuration-package-can-apply-a-configuration"></a>Tester que le package de configuration peut appliquer une configuration

Enfin, si le mode du package de configuration est `AuditandSet`, vous pouvez tester le fait que la méthode `Set` peut appliquer des paramètres à une machine locale à l’aide de la commande `Start-GuestConfigurationPackageRemediation`.

> [!IMPORTANT]
> Cette commande tente d’apporter des modifications dans l’environnement local où elle est exécutée.

Paramètres de la cmdlet `Start-GuestConfigurationPackageRemediation` :

- **Package** : chemin d’accès complet du package de configuration d’invité.

Dans Windows, à partir d’une session PowerShell 7 avec élévation de privilèges.

```powershell
# Test applying the configuration to local machine
Start-GuestConfigurationPackageRemediation -Package ./MyConfig.zip
```

Dans Linux, en exécutant PowerShell à l’aide de sudo.

```bash
# Test applying the configuration to local machine
sudo pwsh -command 'Start-GuestConfigurationPackageRemediation -Package ./MyConfig.zip'
```

La commande ne retourne rien, sauf si des erreurs se produisent. Pour obtenir des détails sur la résolution de problèmes relatifs aux événements qui se produisent pendant l’exécution de la commande `Set`, utilisez le paramètre `-verbose`.

Après avoir exécuté la commande `Start-GuestConfigurationPackageRemediation`, vous pouvez exécuter la commande `Get-GuestConfigurationComplianceStatus` pour confirmer que l’ordinateur est désormais dans l’état approprié.

## <a name="next-steps"></a>Étapes suivantes

- [Publiez l’artefact de package](./guest-configuration-create-publish.md) afin qu’il soit accessible à vos machines.
- Utilisez le module `GuestConfiguration` afin de [créer une définition Azure Policy](./guest-configuration-create-definition.md) pour une gestion à grande échelle de votre environnement.
- [Attribuez votre définition de stratégie personnalisée](../assign-policy-portal.md) à l’aide du portail Azure.
- Découvrez comment visualiser les [informations relatives à la conformité pour les attributions de stratégie de configuration d’invité](./determine-non-compliance.md#compliance-details-for-guest-configuration).
