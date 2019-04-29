---
title: Élément de définition de l’interface utilisateur de création Azure | Documents Microsoft
description: Décrit les éléments à utiliser pour construire des définitions d’interfaces utilisateurs pour le Portail Azure.
services: managed-applications
documentationcenter: na
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: tomfitz
ms.openlocfilehash: 41a583a77f85bb1524112fa20d9098e18bc4f431
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587936"
---
# <a name="createuidefinition-elements"></a>Éléments de CreateUiDefinition
Cet article décrit le schéma et les propriétés de tous les éléments pris en charge d’une fonction CreateUiDefinition. 

## <a name="schema"></a>Schéma

Le schéma pour la plupart des éléments est le suivant :

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "my value",
  "toolTip": "Provide a descriptive name.",
  "constraints": {},
  "options": {},
  "visible": true
}
```

| Propriété | Obligatoire | Description |
| -------- | -------- | ----------- |
| Nom | Oui | Identificateur interne pour faire référence à l’instance spécifique d’un élément. L’utilisation la plus courante du nom d’élément est dans `outputs`, où les valeurs de sortie des éléments spécifiés sont mises en correspondance avec les paramètres du modèle. Vous pouvez également l’utiliser pour lier la valeur de sortie d’un élément à la `defaultValue` d’un autre élément. |
| Type | Oui | Contrôle de l’interface utilisateur pour effectuer le rendu de l’élément. Pour obtenir la liste des types pris en charge, consultez [Éléments](#elements). |
| label | Oui | Texte d’affichage de l’élément. Certains types d’éléments contiennent plusieurs étiquettes. Par conséquent, la valeur pourrait être un objet contenant plusieurs chaînes. |
| defaultValue | Non  | Valeur par défaut de l’élément. Certains types d’élément prennent en charge des valeurs par défaut complexes. Par conséquent, la valeur pourrait être un objet. |
| toolTip | Non  | Texte à afficher dans l’info-bulle de l’élément. Comme pour `label`, certains éléments prennent en charge plusieurs chaînes d’info-bulle. Les liens inline peuvent être intégrés à l’aide de la syntaxe Markdown.
| constraints | Non  | Une ou plusieurs propriétés permettant de personnaliser le comportement de validation de l’élément. Les propriétés prises en charge pour constraints varient selon le type d’élément. Certains types d’éléments ne prennent pas en charge la personnalisation du comportement de validation et n’ont donc aucune propriété constraints. |
| options | Non  | Propriétés supplémentaires qui personnalisent le comportement de l’élément. À l’instar de `constraints`, les propriétés prises en charge varient selon le type d’élément. |
| visible | Non  | Indique si l’élément est affiché. Si la valeur est `true`, l’élément et les éléments enfants applicables sont affichés. La valeur par défaut est `true`. Utilisez les [fonctions logiques](create-uidefinition-functions.md#logical-functions) pour contrôler de manière dynamique cette valeur de propriété.

## <a name="elements"></a>Éléments

La documentation de chaque élément contient un exemple d’interface utilisateur, un schéma, des remarques sur le comportement de l’élément (en général concernant la validation et la personnalisation prise en charge) et un exemple de sortie.

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft.Common.InfoBox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft.Common.TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Étapes suivantes
Pour voir une présentation de la création de définitions d’interface utilisateur, consultez la page [Prise en main de CreateUiDefinition](create-uidefinition-overview.md).
