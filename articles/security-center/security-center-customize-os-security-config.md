---
title: Personnaliser les configurations de la sécurité du système d’exploitation dans Azure Security Center (version préliminaire) | Microsoft Docs
description: Cet article explique comment personnaliser les évaluations de Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2019
ms.author: v-mohabe
ms.openlocfilehash: df4f51f97798048b28c0193dbc61e07fc55c9adc
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69535079"
---
# <a name="customize-os-security-configurations-in-azure-security-center-retired"></a>Personnaliser les configurations de la sécurité du système d’exploitation dans Azure Security Center (hors service)

Cette procédure pas à pas explique comment personnaliser les évaluations de configuration de la sécurité du système d’exploitation (préversion) dans Azure Security Center.

> [!NOTE]
> La possibilité de personnaliser les configurations de la sécurité du système d’exploitation (fonctionnalité d’évaluation) a été supprimée le 31 juillet 2019. Pour plus d’informations et connaître les alternatives, consultez [Retrait des fonctionnalités de Security Center (juillet 2019).](security-center-features-retirement-july2019.md#menu_securityconfigurations)

## <a name="what-are-os-security-configurations"></a>Quelles sont les configurations de sécurité du système d’exploitation ?

Azure Security Center analyse les configurations de la sécurité en appliquant un ensemble de [plus de 150 règles recommandées](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) pour renforcer le système d’exploitation, y compris des règles relatives aux pare-feux, aux audits, aux stratégies de mot de passe et bien plus encore. Si une configuration vulnérable est identifiée sur un ordinateur, Security Center génère une recommandation de sécurité.

En personnalisant les règles, les organisations ont la possibilité de décider des options de configuration les mieux adaptées à leur environnement. Cette fonctionnalité permet aux utilisateurs de définir une stratégie d’évaluation personnalisée et de l’appliquer à tous les ordinateurs candidats de l’abonnement.

> [!NOTE]
> - Actuellement, la personnalisation de la configuration de la sécurité du système d’exploitation est uniquement disponible pour les systèmes d’exploitation Windows Server 2008, 2008 R2, 2012, 2012 R2 et 2016.
> - La configuration s’applique à l’ensemble des machines virtuelles et des ordinateurs connectés à tous les espaces de travail de l’abonnement sélectionné.
> - La personnalisation de la configuration de la sécurité du système d’exploitation est disponible uniquement sur le niveau Standard de Security Center.
>
>

Vous pouvez personnaliser les règles de configuration de la sécurité du système d’exploitation en activant ou en désactivant une règle en particulier, en modifiant le paramètre souhaité d’une règle existante ou en ajoutant une nouvelle règle selon les types pris en charge (registre, stratégie d’audit et stratégie de sécurité). Actuellement, le paramètre souhaité doit avoir une valeur exacte.

Les nouvelles règles doivent avoir le même format et la même structure que les autres règles existantes du même type.

> [!NOTE]
> Pour personnaliser les configurations de la sécurité du système d’exploitation, vous devez avoir le rôle *Propriétaire de l’abonnement*, *Collaborateur de l’abonnement* ou *Administrateur de la sécurité*.
>
>

## <a name="customize-the-default-os-security-configuration"></a>Personnaliser la configuration par défaut de la sécurité du système d’exploitation

Pour personnaliser la configuration par défaut de la sécurité du système d’exploitation dans Security Center, procédez comme suit :

1.  Ouvrez le tableau de bord **Security Center**.

2.  Dans le volet gauche, sélectionnez **Tarifs et paramètres**.

    ![Liste des stratégies de sécurité](media/security-center-customize-os-security-config/manual-provision.png)

4. Sélectionnez l’abonnement applicable, puis sélectionnez **Modifier les configurations de sécurité**.  

    ![La fenêtre Modifier les configurations de la sécurité](media/security-center-customize-os-security-config/blade.png)

5. Suivez les étapes pour télécharger, modifier et charger le fichier modifié.

   > [!NOTE]
   > Par défaut, le fichier de configuration téléchargé est au format *json*. Vous trouverez les instructions à suivre pour modifier ce fichier dans la rubrique [Personnaliser le fichier de configuration](#customize-the-configuration-file).
   >

6. Pour valider la modification, sélectionnez **Enregistrer**. Sinon, la stratégie n’est pas stockée.

    ![Bouton Enregistrer](media/security-center-customize-os-security-config/save-successfully.png)

   Une fois le fichier enregistré, la configuration s’applique à l’ensemble des machines virtuelles et des ordinateurs connectés aux espaces de travail de l’abonnement sélectionné. Généralement, le processus prend quelques minutes mais peut nécessiter plus de temps, en fonction de la taille de l’infrastructure.

À tout moment, vous pouvez réinitialiser la configuration de stratégie actuelle à son état par défaut. Pour ce faire, dans la fenêtre **Modifier les règles de configuration de la sécurité du système d’exploitation**, sélectionnez **Réinitialiser**. Pour confirmer cette option, sélectionnez **Oui** dans la fenêtre contextuelle de confirmation.

![La fenêtre de confirmation Réinitialiser](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>Personnaliser le fichier de configuration

Dans le fichier de personnalisation, chaque version de système d’exploitation prise en charge a un ensemble de règles. Chaque ensemble de règles possède un nom et un ID unique, tels que représentés dans l’exemple suivant :

![Nom et ID de l’ensemble de règles](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> Ce fichier d’exemple a été modifié dans Visual Studio, mais vous pouvez également utiliser le Bloc-notes à condition que le plug-in JSON Viewer soit installé.
>
>

Lorsque vous modifiez le fichier de personnalisation, vous pouvez modifier une règle ou la totalité d’entre elles. Chaque groupe de règles comprend une section *rules* qui est divisée en trois catégories : Registry, AuditPolicy et SecurityPolicy, comme illustré ici :

![Trois catégories d’ensemble de règles](media/security-center-customize-os-security-config/rules-section.png)

Chaque catégorie possède son propre ensemble d’attributs. Vous pouvez modifier les attributs suivants :

- **expectedValue** : le type de données du champ de cet attribut doit correspondre aux valeurs prises en charge par chaque *type de règle*, par exemple :

  - **baselineRegistryRules** : la valeur doit correspondre à l’élément [regValueType](https://msdn.microsoft.com/library/windows/desktop/ms724884) défini dans cette règle.

  - **baselineAuditPolicyRules** : Utilisez l’une des valeurs de chaîne suivantes :

    - *Success and Failure* (« Succès et échec »)

    - *Success*

  - **baselineSecurityPolicyRules** : Utilisez l’une des valeurs de chaîne suivantes :

    - *No one* (« Aucune »)

    - Liste des groupes d’utilisateurs autorisés, par exemple : *Administrators* (Administrateurs), *Backup Operators* (Opérateurs de sauvegarde)

-   **state** : chaîne qui peut contenir l’option *Disabled* ou *Enabled* (« Désactivé » ou « Activé ») Dans cette version, la chaîne respecte la casse.

Ce sont les seuls champs configurables. Si vous ne respectez pas le format ou la taille de fichier, vous ne pourrez pas enregistrer la modification. Un message d’erreur indique que vous devez pour charger un fichier de configuration JSON valide.

Pour obtenir une liste des erreurs potentielles, consultez la rubrique [Codes d’erreur](#error-codes).

Les trois sections suivantes comportent des exemples des règles précédentes. Les attributs *expectedValue* et *state* peuvent être modifiés.

**baselineRegistryRules**
```json
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\LanManServer\\\\Parameters",
    "valueName": "restrictnullsessaccess",
    "ruleId": "f9020046-6340-451d-9548-3c45d765d06d",
    "originalId": "0f319931-aa36-4313-9320-86311c0fa623",
    "cceId": "CCE-10940-5",
    "ruleName": "Network access: Restrict anonymous access to Named Pipes and
    Shares",
    "ruleType": "Registry",
    "expectedValue": "1",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Disabled"

    }
```

**baselineAuditPolicyRules**
```json
    {
    "auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
    "ruleId": "37745508-95fb-44ec-ab0f-644ec0b16995",
    "originalId": "2ea0de1a-c71d-46c8-8350-a7dd4d447895",
    "cceId": "CCE-11001-5",
    "ruleName": "Audit Policy: Account Management: Other Account Management Events",
    "ruleType": "AuditPolicy",
    "expectedValue": "Success and Failure",
    "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Enabled"
    }
```

**baselineSecurityPolicyRules**
```json
    {
    "sectionName": "Privilege Rights",
    "settingName": "SeIncreaseWorkingSetPrivilege",
    "ruleId": "b0ec9d5e-916f-4356-83aa-c23522102b33",
    "originalId": "b61bd492-74b0-40f3-909d-36b9bf54e94c",
    "cceId": "CCE-10548-6",
    "ruleName": "Increase a process working set",
    "ruleType": "SecurityPolicy",
    "expectedValue": "Administrators, Local Service",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "state": "Enabled"
    }
```

Certaines règles sont dupliquées pour les types différents de systèmes d’exploitation. Les règles dupliquées comportent le même attribut *originalId*.

## <a name="create-custom-rules"></a>Créer des règles personnalisées

Il est également possible de créer des règles. Au préalable, tenez compte des restrictions suivantes :

-   La version du schéma, *baselineId* et *baselineName* ne sont pas modifiables.

-   Il n’est pas possible de supprimer un ensemble de règles.

-   Il n’est pas possible d’ajouter un ensemble de règles.

-   Le nombre maximal de règles autorisées (y compris les règles par défaut) est de 1 000 instances.

Les nouvelles règles personnalisées sont marquées par une nouvelle source personnalisée (!= « Microsoft »). Le champ *ruleId* peut avoir la valeur Null ou être vide. S’il est vide, Microsoft en génère un. Sinon, il doit avoir un GUID valide et commun à toutes les règles (par défaut et personnalisées). Passez en revue les contraintes suivantes concernant les champs de base :

-   **originalId** : peut comporter une valeur Null ou être vide. Si *non vide*, doit être un GUID valide.

-   **cceId** : peut comporter une valeur Null ou être vide. Si *non vide*, doit être unique.

-   **ruleType** : (sélectionnez une valeur) Registry, AuditPolicy ou SecurityPolicy.

-   **Severity** : (sélectionnez une valeur) Unknown, Critical, Warning ou Informational.

-   **analyzeOperation** : Doit être *Equals*.

-   **auditPolicyId** : Doit être un GUID valide.

-   **regValueType** : (sélectionnez une valeur) Int, Long, String ou MultipleString.

> [!NOTE]
> Hive doit avoir la valeur *LocalMachine*.
>
>

Exemple de nouvelle règle personnalisée :

**Registry** :
```json
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\Netlogon\\\\MyKeyName",
    "valueName": "MyValueName",
    "originalId": "",
    "cceId": "",
    "ruleName": "My new registry rule", "baselineRuleType": "Registry",
    "expectedValue": "123", "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "MyCustomSource",
    "state": "Enabled"
    }
```
**Security policy** :
```json
   {
   "sectionName": "Privilege Rights",
   "settingName": "SeDenyBatchLogonRight",
   "originalId": "",
   "cceId": "",
   "ruleName": "My new security policy rule", "baselineRuleType":
   "SecurityPolicy",
   "expectedValue": "Guests",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```
**Audit policy** :
```json
   {
   "auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
   "originalId": "",
   "cceId": "",
   "ruleName": " My new audit policy rule ", "baselineRuleType": "AuditPolicy",
   "expectedValue": " Failure",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```

## <a name="file-upload-failures"></a>Échecs de chargement de fichier

Si le fichier de configuration soumis n’est pas valide en raison d’erreurs dans les valeurs ou dans la mise en forme, une erreur d’échec s’affiche, comme **Échec de l’action d’enregistrement**. Il est possible de télécharger un rapport .csv détaillé des erreurs pour résoudre et corriger les erreurs avant de soumettre à nouveau un fichier de configuration corrigé.

Exemple de fichier d’erreur :

![Exemple de fichier d’erreur](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>Codes d’erreur

Le tableau suivant répertorie l’ensemble des erreurs potentielles :

| **Error**                                | **Description**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfigurationSchemaVersionError  | La propriété *schemaVersion* est vide ou non valide. Elle doit avoir la valeur *{0}* .                                                         |
| BaselineInvalidStringError               | La propriété *{0}* ne peut pas contenir *\\n*.                                                                                                         |
| BaselineNullRuleError                    | La liste de règles de configuration de base contient une règle dont la valeur est *null*.                                                                         |
| BaselineRuleCceIdNotUniqueError          | Le CCE-ID *{0}* n’est pas unique.                                                                                                                  |
| BaselineRuleEmptyProperty                | La propriété *{0}* est manquante ou non valide.                                                                                                       |
| BaselineRuleIdNotInDefault               | La règle a comme propriété de source *Microsoft*, mais est introuvable dans l’ensemble de règles Microsoft par défaut.                                                   |
| BaselineRuleIdNotUniqueError             | L’identificateur de règle n’est pas unique.                                                                                                                       |
| BaselineRuleInvalidGuid                  | La propriété *{0}* n’est pas valide. La valeur n’est pas un GUID valide.                                                                             |
| BaselineRuleInvalidHive                  | L’instance Hive doit avoir la valeur LocalMachine.                                                                                                                   |
| BaselineRuleNameNotUniqueError           | Le nom de règle n’est pas unique.                                                                                                                 |
| BaselineRuleNotExistInConfigError        | La règle est introuvable dans la nouvelle configuration. La règle n’est pas supprimable.                                                                     |
| BaselineRuleNotFoundError                | La règle est introuvable dans l’ensemble de règles de base par défaut.                                                                                        |
| BaselineRuleNotInPlace                   | La règle correspond à une règle par défaut possédant le type {0} et figure dans la liste {1}.                                                                       |
| BaselineRulePropertyTooLong              | La propriété *{0}* est trop longue. Longueur maximale autorisée : {1}.                                                                                        |
| BaselineRuleRegTypeInvalidError          | La valeur attendue *{0}* ne correspond pas au type de valeur de registre défini.                                                              |
| BaselineRulesetAdded                     | L’ensemble de règles correspondant à l’identificateur *{0}* est introuvable dans la configuration par défaut. Il n’est pas possible d’ajouter un ensemble de règles.                                               |
| BaselineRulesetIdMustBeUnique            | L’ensemble de règles de base spécifié *{0}* doit être unique.                                                                                           |
| BaselineRulesetNotFound                  | L’ensemble de règles correspondant à l’identificateur *{0}* et au nom *{1}* est introuvable dans la configuration spécifiée. Il n’est pas possible de supprimer un ensemble de règles.                                |
| BaselineRuleSourceNotMatch               | Une règle correspondant à l’identificateur *{0}* est déjà définie.                                                                                                       |
| BaselineRuleTypeDoesntMatch              | Le type de règle par défaut est *{0}* .                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | Le type réel de la règle est *{0}* , alors que la propriété *ruleType* est *{1}* .                                                                          |
| BaselineRuleUnpermittedChangesError      | Seules les propriétés *expectedValue* et *state* sont modifiables.                                                                       |
| BaselineTooManyRules                     | Le nombre maximal de règles personnalisées autorisées est de {0} règles. La configuration donnée comporte {1} règles, {2} règles par défaut et {3} règles personnalisées. |
| ErrorNoConfigurationStatus               | Aucun état de configuration n’a été trouvé. État de configuration souhaité : *Default* (« Par défaut ») ou *Custom* (« Personnalisé »).                                    |
| ErrorNonEmptyRulesetOnDefault            | L’état de la configuration a la valeur par défaut. La liste *BaselineRulesets* doit comporter la valeur Null ou être vide.                                                          |
| ErrorNullRulesetsPropertyOnCustom        | L’état de configuration indiqué est *Custom* (« personnalisé »), alors que la propriété *baselineRulesets* comporte la valeur Null ou est vide.                                             |
| ErrorParsingBaselineConfig               | La configuration spécifiée n’est pas valide. Au moins une des valeurs définies comporter la valeur Null ou a un type non valide.                                  |
| ErrorParsingIsDefaultProperty            | La valeur *configurationStatus* spécifiée, *{0}* , n’est pas valide. Elle ne peut être que *Default* (« par défaut ») ou *Custom* (« personnalisé »).                                         |
| InCompatibleViewVersion                  | La version de l’affichage *{0}* n’est *pas* prise en charge sur ce type d’espace de travail.                                                                                   |
| InvalidBaselineConfigurationGeneralError | La configuration de base spécifiée comporte une ou plusieurs erreurs de validation de type.                                                          |
| ViewConversionError                      | La vue est une version antérieure à celle prise en charge par l’espace de travail. La conversion de l’affichage a échoué : {0}.                                                                 |

Si vous ne disposez pas d’autorisations suffisantes, vous risquez de recevoir une erreur d’échec général, telle que représentée ici :

![Message d’erreur d’échec de l’action d’enregistrement](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>Étapes suivantes
Cet article vous a montré comment personnaliser les évaluations de la configuration de la sécurité du système d’exploitation dans Security Center. Pour en savoir plus sur les règles de configuration et sur la correction, consultez les pages :

- [Règles de base et identificateurs de configuration courants de Security Center](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- Security Center utilise CCE (Common Configuration Enumeration) pour affecter des identificateurs uniques aux règles de configuration. Pour plus d’informations, consultez la section [CCE](https://nvd.nist.gov/config/cce/index).
- [Corriger les configurations de la sécurité](security-center-virtual-machine-protection.md) explique comment résoudre les vulnérabilités lorsque la configuration du système d’exploitation ne correspond pas aux règles de configuration de la sécurité recommandées.
