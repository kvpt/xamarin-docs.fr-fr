---
title: Thèmes de Xamarin.Forms
description: Cet article présente Xamarin.Forms thèmes, qui définissent des apparences visuelles différentes pour les vues standards.
ms.prod: xamarin
ms.assetid: 3DFB7C55-69F6-4980-A501-588719143482
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/01/2017
ms.openlocfilehash: 9a34cfab0c3ed045968f48ac6c67b2f4b66990cc
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832451"
---
# <a name="xamarinforms-themes"></a>Thèmes de Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/Themes/ThemesDemo)

![](~/media/shared/preview.png "Cette API est actuellement en version préliminaire")

Xamarin.Forms thèmes ont été annoncées lors de l’évolution 2016 et sont disponibles en version préliminaire pour les clients à tester et de fournir des commentaires.

Un thème est ajouté à une application Xamarin.Forms en incluant le **Xamarin.Forms.Theme.Base** de package Nuget, ainsi que d’un autre package qui définit un thème spécifique (par exemple). Xamarin.Forms.Theme.Light) ou autre un thème local peut être défini pour l’application.

Reportez-vous à la [le thème clair](light.md) et [le thème sombre](dark.md) pages pour obtenir des instructions sur la façon de les ajouter à une application, ou découvrir les [thème personnalisé exemple](custom.md).

> [!IMPORTANT]
> Vous devez également suivre les étapes à [charger des assemblys de thème (ci-dessous)](#loadtheme) en ajoutant du code réutilisable à iOS `AppDelegate` et Android `MainActivity`. Cela sera améliorée dans une version de la prochaine version d’évaluation.


## <a name="control-appearance"></a>Apparence de contrôle

Le [Light](light.md) et [foncé](dark.md) les thèmes les deux définissent une apparence visuelle spécifique pour les contrôles standards. Une fois que vous ajoutez un thème pour le dictionnaire de ressources de l’application, l’apparence des contrôles standard changera.

Le balisage XAML suivant montre quelques contrôles communs :

```xaml
<StackLayout Padding="40">
    <Label Text="Regular label" />
    <Entry Placeholder="type here" />
    <Button Text="OK" />
    <BoxView Color="Yellow" />
    <Switch />
</StackLayout>
```

Ces captures d’écran montrent ces contrôles avec :

* Aucun thème appliqué
* Thème clair (uniquement des différences subtiles pour n’avoir aucun thème)
* Thème foncé

![](images/standard-none-sml.png "Les contrôles sans thème") ![](images/standard-light-sml.png "contrôles avec le thème clair") ![](images/standard-dark-sml.png "contrôles avec le thème sombre")

<a name="styleclass" />

## <a name="styleclass"></a>StyleClass

Le `StyleClass` propriété permet l’apparence d’une vue à modifier en fonction d’une définition fournie par un thème.

Le [Light](light.md) et [foncé](dark.md) les thèmes les deux définissent trois différentes apparences pour un `BoxView`: `HorizontalRule`, `Circle`, et `Rounded`. Ce balisage illustre trois différents `BoxView`s avec les classes de style différent appliqué :

```xaml
<StackLayout Padding="40">
    <BoxView StyleClass="HorizontalRule" />
    <BoxView StyleClass="Circle" />
    <BoxView StyleClass="Rounded" />
</StackLayout>
```

Cela génère le rendu avec clair et foncé comme suit :

![](images/boxview-light-sml.png "BoxView avec un StyleClass le thème clair") ![](images/boxview-dark-sml.png "BoxView avec un StyleClass le thème sombre")

<a name="builtin" />

## <a name="built-in-classes"></a>Classes intégrées

En plus de styles automatiquement les contrôles communs la lumière et thèmes foncées prend actuellement en charge les classes suivantes qui peuvent être appliquées en définissant le `StyleClass` sur ces contrôles :

**BoxView**

* HorizontalRule
* Cercle
* Arrondi

**Image**

* Cercle
* Arrondi
* Miniature

**Button**

* Par défaut
* Principale
* Opération réussie
* Info
* Warning
* Danger
* Lien
* Petit
* Grand

**Label**

* Header
* En-tête secondaire
* Corps
* Lien
* Inverse


## <a name="troubleshooting"></a>Résolution des problèmes

<a name="loadtheme" />

### <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>Impossible de charger le fichier ou l’assembly 'Xamarin.Forms.Theme.Light' ou une de ses dépendances

Dans la version préliminaire, thèmes n’est peut-être pas en mesure de charger lors de l’exécution. Ajoutez le code ci-dessous dans les projets appropriés pour corriger cette erreur.

**iOS**

Dans le **AppDelegate.cs** ajoutez les lignes suivantes après `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

Dans le **MainActivity.cs** ajoutez les lignes suivantes après `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```


## <a name="related-links"></a>Liens associés

- [Exemple de ThemesDemo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Themes/ThemesDemo)
