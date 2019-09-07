---
title: Résolution des problèmes
description: Conditions d’erreur courantes et comment les résoudre
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63291951-7375-4CBF-BCC3-2E4AD157A2C8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 93cab36b21e2fe73a0e6890140b5ebaeb32f7951
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760031"
---
# <a name="troubleshooting"></a>Résolution des problèmes

_Conditions d’erreur courantes et comment les résoudre_

## <a name="error-unable-to-find-a-version-of-xamarinforms-compatible-with"></a>Erreur : « Impossible de trouver une version de Xamarin. Forms compatible avec... »

Les erreurs suivantes peuvent apparaître dans la fenêtre de la **console du package** lors de la mise à jour de tous les packages NuGet dans une solution Xamarin. Forms ou dans un projet d’application Android Xamarin. Forms :

```csharp
Attempting to resolve dependency 'Xamarin.Android.Support.v7.AppCompat (= 23.3.0.0)'.
Attempting to resolve dependency 'Xamarin.Android.Support.v4 (= 23.3.0.0)'.
Looking for updates for 'Xamarin.Android.Support.v7.MediaRouter'...
Updating 'Xamarin.Android.Support.v7.MediaRouter' from version '23.3.0.0' to '23.3.1.0' in project 'Todo.Droid'.
Updating 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0' to 'Xamarin.Android.Support.v7.MediaRouter 23.3.1.0' failed.
Unable to find a version of 'Xamarin.Forms' that is compatible with 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0'.
```

### <a name="what-causes-this-error"></a>Quelle est la cause de cette erreur ?

Visual Studio pour Mac (ou Visual Studio) peut indiquer que des mises à jour sont disponibles pour le package NuGet Xamarin. Forms *et toutes ses dépendances*. Dans Xamarin Studio, le nœud **packages** de la solution peut se présenter comme suit (les numéros de version peuvent être différents) :

![](images/updates-available.png "Dossier des packages de projet Android")

Cette erreur peut se produire si vous tentez de mettre à jour _tous_ les packages.

En effet, avec les projets Android définis sur une version cible/compilation d’Android 6,0 (API 23) ou en dessous, Xamarin. Forms a une dépendance matérielle sur des versions *spécifiques* des packages de prise en charge Android. Bien que les versions mises à jour de ces packages puissent être disponibles, Xamarin. Forms n’est pas nécessairement compatible avec eux.

Dans ce cas, vous devez mettre à jour _uniquement_ le package **Xamarin. Forms** , car cela garantit que les dépendances sont conservées sur les versions compatibles. Les autres packages que vous avez ajoutés à votre projet peuvent également être mis à jour individuellement, à condition qu’ils n’entraînent pas la mise à jour des packages de support Android.

> [!NOTE]
> Si vous utilisez Xamarin. Forms 2.3.4 ou une version ultérieure **et** que la version cible/compilation de votre projet Android est définie sur Android 7,0 (API 24) ou une version ultérieure, les dépendances matérielles mentionnées ci-dessus ne s’appliquent plus et vous pouvez mettre à jour les packages de prise en charge indépendamment de package Xamarin. Forms.

### <a name="fix-remove-all-packages-and-re-add-xamarinforms"></a>Correctif : Supprimer tous les packages et rajouter Xamarin. Forms

Si les packages **Xamarin. Android. support** ont été mis à jour vers des versions incompatibles, la solution la plus simple consiste à :

1. Supprimez manuellement tous les packages NuGet dans le projet Android, puis
2. Ajoutez de nouveau le package **Xamarin. Forms** .

Cela permet de télécharger automatiquement les versions *appropriées* des autres packages.

Pour voir une vidéo de ce processus, reportez-vous à ce [billet de Forum](https://forums.xamarin.com/discussion/comment/170012/#Comment_170012).
