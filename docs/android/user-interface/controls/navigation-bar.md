---
title: Barre de navigation Xamarin. Android
ms.prod: xamarin
ms.assetid: 6023DB7E-9E72-4B90-A96A-11BC297B8A3D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/01/2017
ms.openlocfilehash: f28b9f19e901d75c432dfecbfec8a63588df3d70
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029226"
---
# <a name="xamarinandroid-navigation-bar"></a>Barre de navigation Xamarin. Android

Android 4 a introduit une nouvelle fonctionnalité de l’interface utilisateur système, appelée *barre de navigation*, qui fournit des contrôles de navigation sur les appareils qui n’incluent pas les boutons matériels pour la **page de démarrage**, l' **arrière-plan**et le **menu**.
La capture d’écran suivante montre la barre de navigation d’un appareil principal de passerelle :

 [![exemple d’une barre de navigation Android](navigation-bar-images/19-navbar.png)](navigation-bar-images/19-navbar.png#lightbox)

Plusieurs nouveaux indicateurs sont disponibles pour contrôler la visibilité de la barre de navigation et de ses contrôles, ainsi que la visibilité de la barre système qui a été introduite dans Android 3. Les indicateurs sont définis dans la classe `Android.View.View` et sont répertoriés ci-dessous :

- `SystemUiFlagVisible` &ndash; rend la barre de navigation visible. 
- `SystemUiFlagLowProfile` &ndash; estompent les contrôles dans la barre de navigation. 
- `SystemUiFlagHideNavigation` &ndash; masque la barre de navigation. 

Ces indicateurs peuvent être appliqués à n’importe quelle vue de la hiérarchie d’affichage en définissant la propriété `SystemUiVisibility`. Si cette propriété est définie pour plusieurs vues, le système les associe à une opération ou et les applique tant que la fenêtre dans laquelle les indicateurs sont définis conserve le focus. Lorsque vous supprimez une vue, tous les indicateurs qu’elle a définis sont également supprimés.

L’exemple suivant montre une application simple où le fait de cliquer sur l’un des boutons modifie la `SystemUiVisibility`:

 [Captures d’écran ![illustrant des SystemUiVisibility visibles, faibles et masqués](navigation-bar-images/18-systemuivisibility.png)](navigation-bar-images/18-systemuivisibility.png#lightbox)

Le code permettant de modifier le `SystemUiVisibility` définit la propriété sur un `TextView` à partir du gestionnaire d’événements Click de chaque bouton, comme indiqué ci-dessous :

```csharp
var tv = FindViewById<TextView> (Resource.Id.systemUiFlagTextView);
var lowProfileButton = FindViewById<Button>(Resource.Id.lowProfileButton);
var hideNavButton = FindViewById<Button> (Resource.Id.hideNavigation);
var visibleButton = FindViewById<Button> (Resource.Id.visibleButton);
           
lowProfileButton.Click += delegate {
    tv.SystemUiVisibility =
        (StatusBarVisibility)View.SystemUiFlagLowProfile;
};
           
hideNavButton.Click += delegate {
    tv.SystemUiVisibility =
       (StatusBarVisibility)View.SystemUiFlagHideNavigation;        
};
           
visibleButton.Click += delegate {
    tv.SystemUiVisibility = (StatusBarVisibility)View.SystemUiFlagVisible;
}
```

En outre, une modification de `SystemUiVisibility` déclenche un événement `SystemUiVisibilityChange`. Tout comme la définition de la propriété `SystemUiVisibility`, un gestionnaire pour l’événement `SystemUiVisibilityChange` peut être enregistré pour n’importe quelle vue de la hiérarchie. Par exemple, le code ci-dessous utilise l’instance `TextView` pour s’inscrire à l’événement :

```csharp
tv.SystemUiVisibilityChange +=
  delegate(object sender, View.SystemUiVisibilityChangeEventArgs e) {
        tv.Text = String.Format ("Visibility = {0}", e.Visibility);
  };
```

## <a name="related-links"></a>Liens associés

- [SystemUIVisibilityDemo (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/systemuivisibilitydemo)
- [Présentation du sandwich glacé](https://www.android.com/about/ice-cream-sandwich/)
- [Plateforme Android 4,0](https://developer.android.com/sdk/android-4.0.html)
