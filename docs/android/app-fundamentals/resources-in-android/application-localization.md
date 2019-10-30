---
title: Localisation d’applications et ressources de type chaîne
ms.prod: xamarin
ms.assetid: 374A9DA6-1853-8B98-6954-7FE3F591C07C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: 7191c37e81da728dfda21da2eaecc95f7b58a401
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025077"
---
# <a name="application-localization-and-string-resources"></a>Localisation d’applications et ressources de type chaîne

La localisation d’applications consiste à fournir des ressources alternatives pour cibler une région ou des paramètres régionaux spécifiques. Par exemple, vous pouvez fournir des chaînes de langues localisées pour différents pays, ou vous pouvez modifier les couleurs ou la disposition pour qu’elles correspondent à des cultures spécifiques. Android chargera et utilisera les ressources appropriées pour les paramètres régionaux de l’appareil au moment de l’exécution sans aucune modification du code source.

Par exemple, l’image ci-dessous montre la même application s’exécutant dans trois paramètres régionaux d’appareil différents, mais le texte affiché dans chaque bouton est spécifique aux paramètres régionaux définis pour chaque appareil :

[![des exemples de trois paramètres régionaux différents](application-localization-images/01-click-me-sml.png)](application-localization-images/01-click-me.png#lightbox)

Dans cet exemple, le contenu d’un fichier de disposition, **main. AXML** , se présente comme suit :

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
   android:orientation="vertical"
   android:layout_width="fill_parent"
   android:layout_height="fill_parent"
   >
<Button  
   android:id="@+id/myButton"
   android:layout_width="fill_parent"
   android:layout_height="wrap_content"
android:text="@string/hello"
   />
</LinearLayout>
```

Dans l’exemple ci-dessus, la chaîne du bouton a été chargée à partir des ressources en fournissant l’ID de ressource de la chaîne :

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Chaînes de ressources pour trois langues](application-localization-images/02-resource-strings-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![Chaînes de ressources pour trois langues](application-localization-images/02-resource-strings-xs.png)

-----

## <a name="localizing-android-apps"></a>Localisation d’applications Android

Lisez la [Présentation de la localisation](~/cross-platform/app-fundamentals/localization.md) pour obtenir des conseils et des conseils sur la localisation des applications mobiles.

Le guide sur la [localisation des applications Android](~/android/app-fundamentals/localization.md) contient des exemples plus spécifiques sur la façon de traduire des chaînes et de localiser des images à l’aide de Xamarin. Android.

## <a name="related-links"></a>Liens associés

- [Localisation d’applications Android](~/android/app-fundamentals/localization.md)
- [Vue d’ensemble de la localisation multiplateforme](~/cross-platform/app-fundamentals/localization.md)
