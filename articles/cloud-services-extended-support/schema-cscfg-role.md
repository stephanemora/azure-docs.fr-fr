---
title: Schéma de rôle Azure Cloud Services (support étendu) | Microsoft Docs
description: Informations relatives au schéma de rôle pour Cloud Services (support étendu)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2567f5bb817a34f6274d5e265a266d67a9c81413
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743916"
---
# <a name="azure-cloud-services-extended-support-config-role-schema"></a>Schéma de rôle de configuration Azure Cloud Services (support étendu)

L’élément `Role` du fichier de configuration de service spécifie le nombre d’instances de rôle à déployer pour chaque rôle dans le service, les valeurs des paramètres de configuration et les empreintes des certificats associés à un rôle.

Pour plus d’informations sur le schéma de configuration de service Azure, consultez [Schéma de configuration du service cloud (support étendu)](schema-cscfg-file.md). Pour plus d’informations sur le schéma de définition de service Azure, consultez [Schéma de définition du service cloud (support étendu)](schema-csdef-file.md).

##  <a name="role-element"></a>Élément de rôle <a name="Role"></a>
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
[Schéma de configuration du service cloud (support étendu)](schema-cscfg-file.md).