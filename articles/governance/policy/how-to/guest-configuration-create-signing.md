---
title: Comment signer des packages de configuration invité
description: Vous pouvez éventuellement signer des packages de contenu de configuration invité et forcer l’agent à autoriser uniquement le contenu signé
ms.date: 07/12/2021
ms.topic: how-to
ms.openlocfilehash: 7c7d120f17e32b3ea9accb6697ac66f4afcbb879
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122773050"
---
# <a name="how-to-sign-guest-configuration-packages"></a>Comment signer des packages de configuration invité

Les stratégies personnalisées de la Configuration invité utilisent le hachage SHA256 pour vérifier que le package de stratégie n’a pas changé. En option, les clients peuvent également utiliser un certificat pour signer des packages et forcer l’extension de la Configuration invité à autoriser uniquement le contenu signé.

Pour activer ce scénario, vous devez effectuer deux étapes. Exécutez l’applet de commande pour signer le package de contenu et ajoutez une balise aux machines qui doivent nécessiter du code pour être signées.

## <a name="signature-validation-using-a-code-signing-certificate"></a>Validation de signature à l’aide d’un certificat de signature de code

Pour utiliser la fonctionnalité de validation de signature, exécutez la cmdlet `Protect-GuestConfigurationPackage` pour signer le package avant sa publication. Cette cmdlet nécessite un certificat de « signature du code ».

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Paramètres de la cmdlet `Protect-GuestConfigurationPackage` :

- **Chemin** : Chemin d’accès complet du package de la Configuration invité.
- **Certificat** : Certificat de signature de code pour signer le package. Ce paramètre est uniquement pris en charge lors de la signature de contenu pour Windows.

## <a name="certificate-requirements"></a>Configuration requise des certificats

L’agent GuestConfiguration s’attend à trouver la clé publique du certificat dans « Autorités de certification racines de confiance » sur des machines Windows et dans le chemin `/usr/local/share/ca-certificates/extra` sur des machines Linux. Pour que le nœud vérifie le contenu signé, installez la clé publique du certificat sur la machine avant d’appliquer la stratégie personnalisée. Ce processus peut être effectué à l’aide de n’importe quelle technique à l’intérieur de la machine virtuelle ou à l’aide d’Azure Policy. Un exemple de modèle est disponible \[pour déployer une machine avec un certificat](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-push-certificate-windows).
La stratégie d’accès Key Vault doit autoriser le fournisseur de ressources de calcul à accéder aux certificats lors des déploiements. Pour les étapes détaillées, consultez [Configurer Key Vault pour des machines virtuelles dans Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Voici un exemple d’exportation de la clé publique à partir d’un certificat de signature, à importer vers la machine.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

## <a name="tag-requirements"></a>Exigences de balise

Une fois votre contenu publié, ajoutez une balise nommée `GuestConfigPolicyCertificateValidation` et avec une valeur `enabled` à toutes les machines virtuelles où la signature du code doit être requise. Pour plus d'informations sur la façon dont les balises peuvent être délivrées à grande échelle à l'aide d'Azure Policy, consultez les [Exemples de balises](../samples/built-in-policies.md#tags). Une fois cette balise en place, la définition de stratégie générée via l’applet de commande `New-GuestConfigurationPolicy` met en œuvre l’exigence via l’extension de la configuration invité.

## <a name="next-steps"></a>Étapes suivantes

- [Testez l’artefact de package](./guest-configuration-create-test.md) à partir de votre environnement de développement.
- [Publiez l’artefact de package](./guest-configuration-create-publish.md) afin qu’il soit accessible à vos machines.
- Utilisez le module `GuestConfiguration` afin de [créer une définition de Stratégie Azure ](./guest-configuration-create-definition.md) pour une gestion à l’échelle de votre environnement.
- [Attribuez votre définition de stratégie personnalisée](../assign-policy-portal.md) à l’aide du portail Azure.
- Découvrez comment visualiser les [informations relatives à la conformité pour les attributions de stratégie de configuration invité](./determine-non-compliance.md#compliance-details-for-guest-configuration).
