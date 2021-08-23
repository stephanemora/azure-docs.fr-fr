---
title: Schéma de rôle Azure Cloud Services (classique) | Microsoft Docs
description: L’élément Rôle d’un fichier de configuration de service spécifie le nombre d’instances de rôle à déployer pour chaque rôle, les valeurs de configuration et les empreintes numériques de certificats.
ms.topic: article
ms.service: cloud-services
ms.subservice: deployment-files
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimtckit
ms.custom: ''
ms.openlocfilehash: dfa38c706dc9e847665a8791bae1e898e1eb26b3
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113088224"
---
# <a name="azure-cloud-services-classic-config-role-schema"></a>Schéma de rôle de configuration d’Azure Cloud Services (classique)

> [!IMPORTANT]
> [Azure Cloud Services (support étendu)](../cloud-services-extended-support/overview.md) est un nouveau modèle de déploiement basé sur Azure Resource Manager pour le produit Azure Cloud Services. En raison de ce changement, les instances Azure Cloud Services qui s’exécutent sur le modèle de déploiement basé sur Azure Service Manager ont été renommées Cloud Services (classique). Tous les nouveaux déploiements doivent passer par [Cloud Services (support étendu)](../cloud-services-extended-support/overview.md).

L’élément `Role` du fichier de configuration de service spécifie le nombre d’instances de rôle à déployer pour chaque rôle dans le service, les valeurs des paramètres de configuration et les empreintes des certificats associés à un rôle.

Pour en savoir plus sur le schéma de configuration de service Azure, consultez la section [Schéma de configuration de services cloud (classique)](schema-cscfg-file.md). Pour en savoir plus sur le schéma de définition de service Azure, consultez la section relative au [schéma de définition de service cloud (classique)](schema-csdef-file.md).

##  <a name="role-element"></a><a name="Role"></a> Élément de rôle
L’exemple suivant illustre l’élément `Role` et ses éléments enfants.

```xml 
<ServiceConfiguration>
  <Role name="<role-name>" vmName="<vm-name>">
    <Instances count="<number-of-instances>"/>
    <ConfigurationSettings>
      <Setting name="<setting-name>" value="<setting-value>" />
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="<certificate-name>" thumbprint="<certificate-thumbprint>" thumbprintAlgorithm="<algorithm>"/>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

La table suivante décrit les attributs de l’élément `Role`.

| Attribut | Description |
| --------- | ----------- |
| name   | Obligatoire. Indique le nom du rôle. Il doit correspondre au nom spécifié pour le rôle dans le fichier de définition de service.|
| vmName | facultatif. Indique le nom DNS d’une machine virtuelle. Ce nom ne peut pas comprendre plus de 10 caractères.|

La table suivante décrit les éléments enfants de l’élément `Role`.

| Élément | Description |
| ------- | ----------- |
| Instances | Obligatoire. Spécifie le nombre d’instances à déployer pour le rôle. Ce nombre est défini par un entier pour l’attribut `count`.|
| Paramètre   | facultatif. Spécifie un nom de paramètre et une valeur dans une collection de paramètres pour un rôle. Le nom du paramètre est défini par une chaîne pour l’attribut `name`, et la valeur du paramètre est définie par une chaîne pour l’attribut `value`.|
| Certificat | facultatif. Spécifie le nom, l’empreinte et l’algorithme d’un certificat de service qui doit être associé au rôle. Le nom du certificat est défini par une chaîne pour l’attribut `name`. L’empreinte de certificat est défini par une chaîne de caractères hexadécimaux ne comportant aucun espace pour l’attribut `thumbprint`. Les caractères hexadécimaux doivent être représentés à l’aide des chiffres et de caractères alphanumériques en majuscules. L’algorithme du certificat est défini par une chaîne pour l’attribut `thumbprintAlgorithm`.|

## <a name="see-also"></a>Voir aussi
[Schéma de configuration de services cloud (classique)](schema-cscfg-file.md)