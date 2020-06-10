---
titre : « mode d’entrée de clavier logiciel sur Android » Description : «les spécificités de la plateforme vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme Android qui définit le mode d’opération pour une zone de saisie de clavier souple.
ms. Prod : xamarin ms. AssetID : AFCDC92F-F61E-42F6-904B-50B5C4949970 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 07/10/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="soft-keyboard-input-mode-on-android"></a>Mode d’entrée de clavier Soft sur Android

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme Android est utilisé pour définir le mode d’opération d’une zone de saisie de clavier souple et est consommé en XAML en affectant [`Application.WindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.WindowSoftInputModeAdjustProperty) à la propriété jointe la valeur de l' [`WindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) énumération :

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize">
  ...
</Application>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

App.Current.On<Android>().UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize);
```

La `Application.On<Android>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur Android. [ `Application.UseWindowSoftInputModeAdjust` ] (XREF : Xamarin.Forms . PlatformConfiguration. AndroidSpecific. application. UseWindowSoftInputModeAdjust ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Application}, Xamarin.Forms . La méthode PlatformConfiguration. AndroidSpecific. WindowSoftInputModeAdjust)), dans l' [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espace de noms, est utilisée pour définir le mode de fonctionnement de la zone d’entrée du clavier conditionnel, avec l' [`WindowSoftInputModeAdjust`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) énumération qui fournit deux valeurs : [`Pan`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Pan) et [`Resize`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize) . La `Pan` valeur utilise l' [`AdjustPan`](xref:Android.Views.SoftInput.AdjustPan) option d’ajustement, qui ne redimensionne pas la fenêtre lorsqu’un contrôle d’entrée a le focus. Au lieu de cela, le contenu de la fenêtre est panoramique afin que le focus actuel ne soit pas masqué par le clavier logiciel. La `Resize` valeur utilise l' [`AdjustResize`](xref:Android.Views.SoftInput.AdjustResize) option de réglage, qui redimensionne la fenêtre lorsqu’un contrôle d’entrée a le focus, afin de faire de la place pour le clavier conditionnel.

Le résultat est que le mode de fonctionnement de la zone d’entrée du clavier conditionnel peut être défini lorsqu’un contrôle d’entrée a le focus :

[![](soft-keyboard-input-mode-images/pan-resize.png "Soft Keyboard Operating Mode Platform-Specific")](soft-keyboard-input-mode-images/pan-resize-large.png#lightbox "Soft Keyboard Operating Mode Platform-Specific")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
