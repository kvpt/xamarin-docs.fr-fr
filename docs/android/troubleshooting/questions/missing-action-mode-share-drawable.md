---
title: "Android.Support.v7.AppCompat - Aucune ressource trouvée correspondant au nom donné : attr 'android:actionModeShareDrawable'"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5814069C-FC43-41DE-B5A5-024D05E59929
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: e688bd27d1116b2a77a12ccd6da29ea582053581
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "75728107"
---
# <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawable"></a>Android.Support.v7.AppCompat - Aucune ressource trouvée correspondant au nom donné : attr 'android:actionModeShareDrawable'

1. Veillez à télécharger les derniers bonus, ainsi que le kit de développement logiciel (SDK) Android 5,0 (API 21) via le gestionnaire de Android SDK.

2. Assurez-vous que vous compilez votre application avec versions compilesdkversion défini sur 21. Vous pouvez également définir targetSdkVersion sur 21.

3. Si vous avez besoin d’une version précédente telle que l’API 19, téléchargez la version correspondante figurant sur la page NuGet :

[https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)

> [!NOTE]
> Si vous l’installez manuellement via la console du gestionnaire de package, assurez-vous d’installer également la même version de Xamarin. Android. support. v4.

[https://www.nuget.org/packages/Xamarin.Android.Support.v4/](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)

Référence Stack Overflow : [https://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro](https://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro)

## <a name="see-also"></a>Voir aussi

- [Quels packages Android SDK dois-je installer ?](~/android/troubleshooting/questions/install-android-sdk-packages.md)
