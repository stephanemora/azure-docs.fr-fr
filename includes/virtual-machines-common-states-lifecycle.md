---
title: Fichier include
description: Fichier include
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: include
ms.date: 08/09/2018
ms.author: vashan, cynthn, rajsqr
ms.custom: include file
ms.openlocfilehash: 57f557a812ec5e4eea75b76ca1394ca360a85d30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67177002"
---
Les machines virtuelles Microsoft Azure passent par différents états qui peuvent être classés en états *d’approvisionnement* et *d’alimentation*. Cet article vise à décrire ces états et en particulier à mettre en exergue la date de facturation des clients pour l’utilisation des instances. 

## <a name="power-states"></a>États d’alimentation

L’état d’alimentation représente le dernier état connu de la machine virtuelle.

![Diagramme d’état de l’alimentation des machines virtuelles](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
Le tableau suivant contient une description de l’état de chaque instance et indique si elle est facturée pour utilisation ou non.

<table>
<tr>
<th>
State
</th>
<th>
Description
</th>
<th>
Facturation de l’utilisation de l’instance
</th>
</tr>
<tr>
<td>
<p><b>Démarrage en cours</b></p>
</td>
<td>
<p>La machine virtuelle démarre.</p>
<code>"statuses": [<br>
   {<br>
      "code": "PowerState/starting",<br>
       "level": "Info",<br>
        "displayStatus": "VM starting"<br>
    }<br>
    ]</code><br>
</td>
<td>
<p><b>Pas de facturation</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Exécution</b></p>
</td>
<td>
<p>État de fonctionnement normal d’une machine virtuelle</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/running",<br>
 "level": "Info",<br>
 "displayStatus": "VM running"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Facturation</b></p>
</td>
</tr>
<tr>
<td>
<p><b>En cours d’arrêt</b></p>
</td>
<td>
<p>Il s’agit d’un état transitoire. À l’issue de l’opération, l’état indiqué est **Arrêté**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopping",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopping"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Facturation</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Arrêté</b></p>
</td>
<td>
<p>La machine virtuelle a été arrêtée à partir du système d’exploitation invité ou à l’aide des API PowerOff.</p>
<p>Le matériel est toujours alloué à la machine virtuelle et demeure sur l’hôte. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopped",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopped"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Facturation&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Désallocation</b></p>
</td>
<td>
<p>État transitoire. À l’issue de l’opération, la machine virtuelle indique l’état **Désalloué**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocating",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocating"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Pas de facturation</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Désalloué</b></p>
</td>
<td>
<p>La machine virtuelle a été arrêtée et supprimée de l’hôte. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocated",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocated"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Pas de facturation</b></p>
</td>
</tr>
</tbody>
</table>


&#42;Certaines ressources Azure, par exemple les disques et le réseau, impliquent des frais. Les licences de logiciels sur l’instance n’engendrent aucun frais.

## <a name="provisioning-states"></a>États d’approvisionnement

Un état d’approvisionnement est l’état d’une opération de type plan de contrôle initiée par l’utilisateur sur la machine virtuelle. Ces états sont distincts de l’état d’alimentation d’une machine virtuelle.

- **Créer** : création d’une machine virtuelle.

- **Mettre à jour** : met à jour le modèle correspondant à une machine virtuelle existante. Certaines modifications apportées aux machines virtuelles et ne concernant pas un modèle, par exemple Démarrer/Redémarrer, relèvent également d’une mise à jour.

- **Supprimer** : suppression d’une machine virtuelle.

- **Désallouer** : lorsqu’une machine virtuelle est arrêtée, puis supprimée de l’hôte. Étant donné que la désallocation d’une machine virtuelle est considérée comme une mise à jour, elle affiche les états d’approvisionnement liés à la mise à jour.



Voici les états transitoires des opérations une fois que la plateforme a accepté une action initiée par l’utilisateur :

<br>

<table>
<tbody>
<tr>
<td width="162">
<p><b>États</b></p>
</td>
<td width="366">
<p>Description</p>
</td>
</tr>
<tr>
<td width="162">
<p><b>Création</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating",<br>
 "level": "Info",<br>
 "displayStatus": "Creating"<br>
 }</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Mise à jour</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/updating",<br>
 "level": "Info",<br>
 "displayStatus": "Updating"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Suppression en cours</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/deleting",<br>
 "level": "Info",<br>
 "displayStatus": "Deleting"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>États d’approvisionnement d’un système d’exploitation</b></p>
</td>
<td width="366">
<p>Si une machine virtuelle est créée avec une image de système d’exploitation et pas avec une image spécialisée, les sous-états suivants peuvent être observés :</p>
<p>1. <b>OSProvisioningInprogress</b> &ndash; La machine virtuelle est en cours d’exécution et l’installation du système d’exploitation invité est en cours. <p /> 
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningInprogress",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning In progress"<br>
 }<br>
]</code><br>
<p>2. <b>OSProvisioningComplete</b> &ndash; État de courte durée. La machine virtuelle passe rapidement à l’état **Réussite**, sauf si des extensions doivent être installées. L’installation des extensions peut prendre du temps. <br />
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningComplete",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning Complete"<br>
 }<br>
]</code><br>
<p><b>Remarque</b> : Le provisionnement du système d’exploitation peut passer à l’état **Échec** en cas de défaillance du système d’exploitation, ou si ce dernier n’est pas installé à temps. Les clients sont facturés pour la machine virtuelle déployée dans l’infrastructure.</p>
</td>
</tr>
</table>


Une fois l’opération terminée, la machine virtuelle passe à l’un des états suivants :

- **Réussi** : les actions initiées par l’utilisateur se sont terminées.

    ```
  "statuses": [ 
  {
     "code": "ProvisioningState/succeeded",
     "level": "Info",
     "displayStatus": "Provisioning succeeded",
     "time": "time"
  }
  ]
    ```

 

- **Échec** : représente une opération en échec. Reportez-vous aux codes d’erreur pour obtenir plus d’informations ainsi que les solutions possibles.

    ```
  "statuses": [
    {
      "code": "ProvisioningState/failed/InternalOperationError",
      "level": "Error",
      "displayStatus": "Provisioning failed",
      "message": "Operation abandoned due to internal error. Please try again later.",
      "time": "time"
    }
    ]
    ```



## <a name="vm-instance-view"></a>Vue d’instance de machine virtuelle

L’API de vue d’instance fournit des informations sur l’état d’exécution d’une machine virtuelle. Pour plus d’informations, consultez la documentation relative à l’API [Virtual Machines - Instance View](https://docs.microsoft.com/rest/api/compute/virtualmachines/instanceview) (Machines virtuelles : vue d’instance).

Azure Resource Explorer présente une IU simple permettant d’afficher l’état en cours d’exécution des machines virtuelles : [Resource Explorer](https://resources.azure.com/).

Les états d’approvisionnement sont visibles dans la vue d’instance et les propriétés des machines virtuelles. Les états d’alimentation sont disponibles dans la vue d’instance des machines virtuelles. 

