---
title: Condition Filtres pour appareils dans la stratégie d’accès conditionnel - Azure Active Directory
description: Utiliser des filtres pour appareils dans une stratégie d’accès conditionnel pour améliorer la posture de sécurité
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/03/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e760cbca23aba21ad2d8c85e21a8014590419e9
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111438321"
---
# <a name="conditional-access-filters-for-devices-preview"></a>Accès conditionnel : filtres pour appareils (préversion)

Lors de la création de stratégies d’accès conditionnel, les administrateurs ont souhaité avoir à disposition une fonction permettant de cibler ou d’exclure des appareils spécifiques dans leur environnement. La condition en préversion Filtres pour appareils offre aux administrateurs cette fonctionnalité. Vous pouvez maintenant cibler des appareils spécifiques à l’aide [des opérateurs et des propriétés d’appareil pris en charge pour les filtres](#supported-operators-and-device-properties-for-filters) et les autres conditions d’attribution disponibles dans vos stratégies d’accès conditionnel.

:::image type="content" source="media/concept-condition-filters-for-devices/create-filter-for-devices-condition.png" alt-text="Création d’un filtre pour appareil dans les conditions d’une stratégie d’accès conditionnel":::

> [!IMPORTANT]
> La condition Filtres pour appareils est actuellement en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="common-scenarios"></a>Scénarios courants

Les organisations peuvent à présent activer l’utilisation d’une condition Filtres pour appareils dans plusieurs cas de figure. Voici quelques scénarios de base avec des exemples d’utilisation de cette nouvelle condition.

- Limiter l’accès aux ressources privilégiées (comme Microsoft Azure Management) aux utilisateurs privilégiés, qui y accèdent à partir de [stations de travail d’administration privilégiées ou sécurisées](/security/compass/privileged-access-devices). Dans ce scénario, les organisations créent deux stratégies d’accès conditionnel :
   - Stratégie 1 : tous les utilisateurs ayant le rôle d’annuaire Administrateur général et qui accèdent à l’application cloud Microsoft Azure Management, et pour Contrôles d’accès, Accorder l’accès, mais exiger l’authentification multifacteur et que l’appareil soit marqué comme conforme.
   - Stratégie 2 : tous les utilisateurs ayant le rôle d’annuaire Administrateur général et qui accèdent à l’application cloud Microsoft Azure Management, en excluant les filtres pour appareils utilisant l’expression de règle device.extensionAttribute1 est égal à SAW et pour Contrôles d’accès, Bloquer.
- Bloquer l’accès aux ressources de l’organisation à partir d’appareils exécutant une version de système d’exploitation non prise en charge telle que Windows 7. Pour ce scénario, les organisations créent les deux stratégies d’accès conditionnel suivantes :
   - Stratégie 1 : tous les utilisateurs, qui accèdent à toutes les applications cloud et pour Contrôles d’accès, Accorder l’accès, mais exiger que l’appareil soit marqué comme conforme ou qu’il ait une jointure hybride Azure AD.
   - Stratégie 2 : tous les utilisateurs, accédant à toutes les applications cloud, incluant les filtres pour appareils utilisant l’expression de règle device.operatingSystem est égal à Windows et device.operatingSystemVersion commence par « 6.1 » et pour Contrôles d’accès, Bloquer.
- Il n’est pas nécessaire d’utiliser l’authentification multifacteur pour des comptes spécifiques tels que les comptes de service en cas d’utilisation sur des appareils spécifiques tels que des téléphones Teams ou des appareils Surface Hub. Pour ce scénario, les organisations créent les deux stratégies d’accès conditionnel suivantes :
   - Stratégie 1 : tous les utilisateurs à l’exclusion des comptes de service, accédant à toutes les applications cloud, et pour Contrôles d’accès, Accorder l’accès, mais exiger l’authentification multifacteur.
   - Stratégie 2 : une sélection d’utilisateurs et de groupes et inclure le groupe qui contient les comptes de service uniquement, accédant à toutes les applications cloud, à l’exclusion des filtres pour appareils utilisant l’expression de règle device.extensionAttribute2 n’est pas égal à TeamsPhoneDevice et pour Contrôles d’accès, Bloquer.

## <a name="create-a-conditional-access-policy"></a>Créer une stratégie d’accès conditionnel

Les filtres pour appareils sont une option lors de la création d’une stratégie d’accès conditionnel dans le portail Azure ou à l’aide de l’API Microsoft Graph.

> [!IMPORTANT]
> L’état de l’appareil et les filtres pour appareils ne peuvent pas être utilisés ensemble dans la stratégie d’accès conditionnel.

Les étapes suivantes vous aideront à créer deux stratégies d’accès conditionnel pour prendre en charge le premier scénario sous [Scénarios courants](#common-scenarios). 

Stratégie 1 : tous les utilisateurs ayant le rôle d’annuaire Administrateur général et qui accèdent à l’application cloud Microsoft Azure Management, et pour Contrôles d’accès, Accorder l’accès, mais exiger l’authentification multifacteur et que l’appareil soit marqué comme conforme.

1. Connectez-vous au **portail Microsoft Azure** en tant qu’administrateur général, administrateur de sécurité ou administrateur de l’accès conditionnel.
1. Accédez à **Azure Active Directory** > **Sécurité** > **Accès conditionnel.**
1. Sélectionnez **Nouvelle stratégie**.
1. Donnez un nom à votre stratégie. Nous recommandons aux organisations de créer une norme explicite pour les noms de leurs stratégies.
1. Sous **Affectations**, sélectionnez **Utilisateurs et groupes**.
   1. Sous **Inclure**, sélectionnez **Rôles d’annuaire** et choisissez **Administrateur général**.
   
      > [!WARNING]
      > Les stratégies d’accès conditionnel prennent en charge les rôles intégrés. Les stratégies d’accès conditionnel ne sont pas appliquées pour d’autres types de rôles, y compris les [rôles limités à une étendue d’unité administrative](../roles/admin-units-assign-roles.md) ou les [rôles personnalisés](../roles/custom-create.md).

   1. Sous **Exclure**, sélectionnez **Utilisateurs et groupes**, puis choisissez les comptes d’accès d’urgence ou de secours de votre organisation. 
   1. Sélectionnez **Terminé**.
1. Sous **Applications ou actions cloud** > **Inclure**, sélectionnez **Sélectionner les applications**, puis **Microsoft Azure Management**.
1. Sous **Contrôles d’accès** > **Accorder**, sélectionnez **Accorder l’accès**, **Exiger l’authentification multifacteur** et **Exiger que l’appareil soit marqué comme conforme**, puis choisissez **Sélectionner**.
1. Confirmez vos paramètres et réglez **Activer la stratégie** sur **Activé**.
1. Sélectionnez **Créer** pour créer votre stratégie.

Stratégie 2 : tous les utilisateurs ayant le rôle d’annuaire Administrateur général et qui accèdent à l’application cloud Microsoft Azure Management, en excluant les filtres pour appareils utilisant l’expression de règle device.extensionAttribute1 est égal à SAW et pour Contrôles d’accès, Bloquer.

1. Sélectionnez **Nouvelle stratégie**.
1. Donnez un nom à votre stratégie. Nous recommandons aux organisations de créer une norme explicite pour les noms de leurs stratégies.
1. Sous **Affectations**, sélectionnez **Utilisateurs et groupes**.
   1. Sous **Inclure**, sélectionnez **Rôles d’annuaire** et choisissez **Administrateur général**.
   
      > [!WARNING]
      > Les stratégies d’accès conditionnel prennent en charge les rôles intégrés. Les stratégies d’accès conditionnel ne sont pas appliquées pour d’autres types de rôles, y compris les [rôles limités à une étendue d’unité administrative](../roles/admin-units-assign-roles.md) ou les [rôles personnalisés](../roles/custom-create.md).

   1. Sous **Exclure**, sélectionnez **Utilisateurs et groupes**, puis choisissez les comptes d’accès d’urgence ou de secours de votre organisation. 
   1. Sélectionnez **Terminé**.
1. Sous **Applications ou actions cloud** > **Inclure**, sélectionnez **Sélectionner les applications**, puis **Microsoft Azure Management**.
1. Sous **Conditions**, sélectionnez **Filtres pour appareils (préversion)** .
   1. Définissez **Configurer** sur **Oui**.
   1. Définissez **Appareils correspondant à la règle** sur **Exclure les appareils filtrés de la stratégie**.
   1. Définissez la propriété sur `ExtensionAttribute1`, l’opérateur sur `Equals` et la valeur sur `SAW`.
   1. Sélectionnez **Terminé**.
1. Sous **Contrôles d’accès** > **Accorder**, sélectionnez **Bloquer l’accès**, puis **Sélectionner**.
1. Confirmez vos paramètres et réglez **Activer la stratégie** sur **Activé**.
1. Sélectionnez **Créer** pour créer votre stratégie.

### <a name="filters-for-devices-graph-api"></a>API Graph Filtres pour appareils

L’API Filtres pour appareils est actuellement disponible dans le point de terminaison bêta Microsoft Graph et est accessible à l’aide de https://graph.microsoft.com/beta/identity/conditionalaccess/policies/. Vous pouvez configurer des filtres pour appareils lors de la création d’une stratégie d’accès conditionnel ou vous pouvez mettre à jour une stratégie existante pour configurer une condition Filtres pour appareils. Pour mettre à jour une stratégie existante, vous pouvez effectuer un appel de correctif sur le point de terminaison bêta Microsoft Graph mentionné ci-dessus en ajoutant l’ID de stratégie d’une stratégie existante et en exécutant le corps de la requête suivante. L’exemple ci-dessous illustre la configuration d’une condition Filtres pour appareils excluant des appareils qui ne sont pas marqués comme des appareils SAW. La syntaxe de la règle peut être composée de plusieurs expressions. Pour en savoir plus sur la syntaxe, consultez la page sur les règles avec plusieurs expressions. 

```json
{
    "conditions": {
        "devices": {
            "deviceFilter": {
                "mode": "exclude",
                "rule": "device.extensionAttribute1 -ne \"SAW\""
            }
        }
    }
}
```

## <a name="supported-operators-and-device-properties-for-filters"></a>Opérateurs et propriétés d’appareil pris en charge pour les filtres

Les attributs d’appareil suivants peuvent être utilisés avec la condition Filtres pour appareils dans l’accès conditionnel.

| Attributs d’appareil pris en charge | Opérateurs pris en charge | Valeurs prises en charge | Exemple |
| --- | --- | --- | --- |
| deviceId | Equals, NotEquals, In, NotIn | ID d’appareil valide qui est un GUID | (device.deviceid -eq “498c4de7-1aee-4ded-8d5d-000000000000”) |
| displayName | Equals, NotEquals, StartsWith, NotStartsWith, EndsWith, NotEndsWith, Contains, NotContains, In, NotIn | Toute chaîne | (device.displayName -contains “ABC”) |
| manufacturer | Equals, NotEquals, StartsWith, NotStartsWith, EndsWith, NotEndsWith, Contains, NotContains, In, NotIn | Toute chaîne | (device.manufacturer -startsWith “Microsoft”) |
| mdmAppId | Equals, NotEquals, In, NotIn | Un ID d’application MDM valide | (device.mdmAppId -in [“0000000a-0000-0000-c000-000000000000”] |
| model | Equals, NotEquals, StartsWith, NotStartsWith, EndsWith, NotEndsWith, Contains, NotContains, In, NotIn | Toute chaîne | (device.model -notContains “Surface”) |
| operatingSystem | Equals, NotEquals, StartsWith, NotStartsWith, EndsWith, NotEndsWith, Contains, NotContains, In, NotIn | Un système d’exploitation valide (comme Windows, iOS ou Android) | (device.operatingSystem -eq “Windows”) |
| operatingSystemVersion | Equals, NotEquals, StartsWith, NotStartsWith, EndsWith, NotEndsWith, Contains, NotContains, In, NotIn | Une version valide du système d’exploitation (par exemple, 6.1 pour Windows 7, 6.2 pour Windows 8 ou 10.0 pour Windows 10) | (device.operatingSystemVersion -in [“10.0.18363”, “10.0.19041”, “10.0.19042”]) |
| pyhsicalIds | Contains, NotContains | À titre d’exemple, tous les appareils Windows AutoPilot stockent ZTDId (valeur unique affectée à tous les appareils Windows AutoPilot importés) dans la propriété physicalIds de l’appareil. | (device.devicePhysicalIDs -contains "[ZTDId]") |
| profileType | Equals, NotEquals | Type de profil valide défini pour un appareil. Les valeurs prises en charge sont les suivantes : RegisteredDevice (par défaut), SecureVM (pour les machines virtuelles Windows dans Azure activée avec la connexion Azure AD), Printer (pour les imprimantes), Shared (pour les appareils partagés), IoT (pour les appareils IoT) | (device.profileType -notIn [“Printer”, “Shared”, “IoT”] |
| systemLabels | Contains, NotContains | Liste des étiquettes appliquées à l’appareil par le système. Certaines des valeurs prises en charge sont les suivantes : AzureResource (pour les machines virtuelles Windows dans Azure activées avec la connexion Azure AD), M365Managed (pour les appareils managés à l’aide de Microsoft Managed Desktop), Multi-User (pour les appareils partagés) | (device.systemLabels -contains "M365Managed") |
| trustType | Equals, NotEquals | État inscrit valide pour les appareils. Les valeurs prises en charge sont les suivantes : AzureAD (pour les appareils Azure AD joints), ServerAD (pour les appareils Azure AD Hybride joints), Workplace (pour les appareils inscrits à Azure AD) | (device.trustType -notIn ‘ServerAD, Workplace’) |
| extensionAttribute1-15 | Equals, NotEquals, StartsWith, NotStartsWith, EndsWith, NotEndsWith, Contains, NotContains, In, NotIn | Les attributs d’extension 1 à 15 (extensionAttributes1-15) sont des attributs que les clients peuvent utiliser pour les objets d’appareil. Les clients peuvent mettre à jour les extensions d’attribut 1 à 15 (extensionAttributes1-15) avec des valeurs personnalisées et les utiliser dans la condition Filtres pour appareils dans l’accès conditionnel. Toute valeur de chaîne peut être utilisée. | (device.extensionAttribute1 -eq ‘SAW’) |

## <a name="policy-behavior-with-filters-for-devices"></a>Comportement de la stratégie avec la condition Filtres pour appareils

La condition Filtres pour appareils (en préversion) dans l’accès conditionnel évalue la stratégie en fonction des attributs d’appareil d’un appareil inscrit dans Azure AD. Par conséquent, il est important de comprendre dans quelles circonstances la stratégie est appliquée ou non. Le tableau ci-dessous illustre le comportement lorsque des conditions Filtres pour appareils sont configurées. 

| Condition Filtres pour appareils | État d’inscription de l’appareil | Filtre pour appareil appliqué 
| --- | --- | --- |
| Mode include/exclude avec opérateurs positifs (Equals, StartsWith, EndsWith, Contains, In) et utilisation de n’importe quel attribut | Appareil non inscrit | No |
| Mode include/exclude avec opérateurs positifs (Equals, StartsWith, EndsWith, Contains, In) et utilisation d’attributs (sauf attributs d’extension 1 à 15) | Appareil inscrit | Oui, si les critères sont satisfaits |
| Mode include/exclude avec opérateurs positifs (Equals, StartsWith, EndsWith, Contains, In) et utilisation d’attributs (incluant les attributs d’extension 1 à 15) | Appareil inscrit géré par Intune | Oui, si les critères sont satisfaits |
| Mode include/exclude avec opérateurs positifs (Equals, StartsWith, EndsWith, Contains, In) et utilisation d’attributs (incluant les attributs d’extension 1 à 15) | Appareil inscrit non géré par Intune | Oui, si les critères sont satisfaits et si l’appareil est conforme ou joint à Azure AD Hybride |
| Mode include/exclude avec opérateurs négatifs (NotEquals, NotStartsWith, NotEndsWith, NotContains, NotIn) et utilisation de n’importe quel attribut | Appareil non inscrit | Yes |
| Mode include/exclude avec opérateurs négatifs (NotEquals, NotStartsWith, NotEndsWith, NotContains, NotIn) et utilisation de n’importe quel attribut (sauf les extensions d’attribut 1 à 15) | Appareil inscrit | Oui, si les critères sont satisfaits |
| Mode include/exclude avec opérateurs négatifs (NotEquals, NotStartsWith, NotEndsWith, NotContains, NotIn) et utilisation de n’importe quel attribut (incluant les extensions d’attribut de 1 à 15) | Appareil inscrit géré par Intune | Oui, si les critères sont satisfaits |
| Mode include/exclude avec opérateurs négatifs (NotEquals, NotStartsWith, NotEndsWith, NotContains, NotIn) et utilisation de n’importe quel attribut (incluant les extensions d’attribut de 1 à 15) | Appareil inscrit non géré par Intune | Oui, si les critères sont satisfaits et si l’appareil est conforme ou joint à Azure AD Hybride |

## <a name="next-steps"></a>Étapes suivantes

- [Accès conditionnel : Conditions](concept-conditional-access-conditions.md)
- [Stratégies d’accès conditionnel courantes](concept-conditional-access-policy-common.md)
- [Sécurisation des appareils dans le contexte de l’accès privilégié](/security/compass/privileged-access-devices)
