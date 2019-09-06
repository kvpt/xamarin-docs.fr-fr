---
title: Propriétés d’automatisation
description: Cet article explique comment utiliser la classe AutomationProperties dans une application Xamarin.Forms pour permettre à un lecteur d’écran d’énoncer les éléments figurant dans la page.
ms.prod: xamarin
ms.assetid: c0bb6893-fd26-47e7-88e5-3c333c9f786c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
ms.openlocfilehash: 92e2ceeea901e2acea70118e75d936f3b3ac94db
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199997"
---
# <a name="automation-properties-in-xamarinforms"></a>Propriétés d’automatisation dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility)

_Xamarin.Forms permet de définir des valeurs d’accessibilité sur les éléments d’interface utilisateur à l’aide de propriétés jointes à partir de la classe AutomationProperties, qui à leur tour définissent des valeurs natives d’accessibilité. Cet article explique comment utiliser la classe AutomationProperties pour permettre à un lecteur d’écran d’énoncer les éléments figurant dans la page._

Xamarin.Forms permet de définir des propriétés d’automatisation sur les éléments d’interface utilisateur via les propriétés jointes suivantes :

- `AutomationProperties.IsInAccessibleTree` – indique si l’élément est disponible pour une application accessible. Pour plus d’informations, consultez [AutomationProperties.IsInAccessibleTree](#isinaccessibletree).
- `AutomationProperties.Name` – brève description de l’élément qui sert d’identificateur prononçable pour l’élément. Pour plus d’informations, consultez [AutomationProperties.Name](#name).
- `AutomationProperties.HelpText` – description plus longue de l’élément, qui peut être considérée comme un texte d’info-bulle associé à l’élément. Pour plus d’informations, consultez [AutomationProperties.HelpText](#helptext).
- `AutomationProperties.LabeledBy` – permet à un autre élément de définir des informations d’accessibilité pour l’élément actuel. Pour plus d’informations, consultez [AutomationProperties.LabeledBy](#labeledby).

Ces propriétés jointes définissent des valeurs natives d’accessibilité pour permettre à un lecteur d’écran d’énoncer l’élément. Pour plus d’informations sur les propriétés jointes, consultez [Propriétés jointes](~/xamarin-forms/xaml/attached-properties.md).

> [!IMPORTANT]
> L’utilisation des propriétés jointes `AutomationProperties` peut avoir un impact sur l’exécution des tests de l’interface utilisateur dans Android. Les propriétés [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId), `AutomationProperties.Name` et `AutomationProperties.HelpText` définissent la propriété native `ContentDescription`, avec les valeurs de propriété `AutomationProperties.Name` et `AutomationProperties.HelpText` qui sont prioritaires sur la valeur `AutomationId` (si `AutomationProperties.Name` et `AutomationProperties.HelpText` sont tous deux définis, les valeurs sont concaténées). Cela signifie que tous les tests recherchant `AutomationId` échouent si `AutomationProperties.Name` ou `AutomationProperties.HelpText` sont également définis sur l’élément. Dans ce scénario, les tests d’interface utilisateur doivent être modifiés pour rechercher la valeur de `AutomationProperties.Name` ou de `AutomationProperties.HelpText`, ou une concaténation des deux.

Chaque plateforme dispose d’un lecteur d’écran différent pour effectuer la narration des valeurs d’accessibilité :

- iOS dispose de VoiceOver. Pour plus d’informations, consultez [Tester l’accessibilité sur votre appareil avec VoiceOver](https://developer.apple.com/library/content/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html) sur developer.apple.com.
- Android dispose de TalkBack. Pour plus d’informations, consultez [Test de l’accessibilité de votre application](https://developer.android.com/training/accessibility/testing.html#talkback) sur developer.android.com.
- Windows dispose du Narrateur. Pour plus d’informations, consultez [Vérifier les principaux scénarios d’application à l’aide du Narrateur](/windows/uwp/accessibility/accessibility-testing#verify-main-app-scenarios-by-using-narrator).

Toutefois, le comportement exact d’un lecteur d’écran dépend du logiciel et de sa configuration par l’utilisateur. Par exemple, la plupart des lecteurs d’écran lisent le texte associé à un contrôle lorsque ce dernier reçoit le focus, permettant aux utilisateurs de s’orienter lorsqu’ils se déplacent parmi les contrôles sur la page. Certains lecteurs d’écran lisent également l’interface utilisateur entière de l’application quand une page s’affiche, ce qui permet à l’utilisateur de recevoir tout le contenu informatif disponible de la page avant d’essayer de le parcourir.

Les lecteurs d’écran lisent également des valeurs d’accessibilité différentes. Dans l'exemple d'application :

- VoiceOver lit la valeur `Placeholder` de l’objet `Entry`, suivie des instructions d’utilisation du contrôle.
- VoiceOver lit la valeur `Placeholder` de l’objet `Entry`, suivie de la valeur `AutomationProperties.HelpText` et des instructions d’utilisation du contrôle.
- Le Narrateur lit la valeur `AutomationProperties.LabeledBy` de l’objet `Entry`, suivie des instructions d’utilisation du contrôle.

En outre, le Narrateur accorde la priorité à `AutomationProperties.Name`, à `AutomationProperties.LabeledBy` puis à `AutomationProperties.HelpText`. Dans Android, TalkBack peut combiner les valeurs `AutomationProperties.Name` et `AutomationProperties.HelpText`. Par conséquent, il est recommandé de réaliser des tests d’accessibilité complets sur chaque plateforme pour garantir une expérience optimale.

<a name="isinaccessibletree" />

## <a name="automationpropertiesisinaccessibletree"></a>AutomationProperties.IsInAccessibleTree

La propriété jointe `AutomationProperties.IsInAccessibleTree` est un `boolean` qui détermine si l’élément est accessible et par conséquent visible aux lecteurs d’écran. Elle doit être définie sur `true` pour utiliser les autres propriétés jointes d’accessibilité. Cela peut être accompli en XAML de la façon suivante :

```xaml
<Entry AutomationProperties.IsInAccessibleTree="true" />
```

Elle peut également être définie en C# comme suit :

```csharp
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
```

> [!NOTE]
> Notez que la méthode [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) peut également être utilisée pour définir la propriété jointe `AutomationProperties.IsInAccessibleTree` – `entry.SetValue(AutomationProperties.IsInAccessibleTreeProperty, true);`

<a name="name" />

## <a name="automationpropertiesname"></a>AutomationProperties.Name

La valeur de la propriété jointe `AutomationProperties.Name` doit être une brève chaîne de texte descriptive qu’un lecteur d’écran utilise pour annoncer un élément. Cette propriété doit être définie pour les éléments qui ont une signification importante pour comprendre le contenu ou interagir avec l’interface utilisateur. Cela peut être accompli en XAML de la façon suivante :

```xaml
<ActivityIndicator AutomationProperties.IsInAccessibleTree="true"
                   AutomationProperties.Name="Progress indicator" />
```

Elle peut également être définie en C# comme suit :

```csharp
var activityIndicator = new ActivityIndicator();
AutomationProperties.SetIsInAccessibleTree(activityIndicator, true);
AutomationProperties.SetName(activityIndicator, "Progress indicator");
```

> [!NOTE]
> Notez que la méthode [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) peut également être utilisée pour définir la propriété jointe `AutomationProperties.Name` – `activityIndicator.SetValue(AutomationProperties.NameProperty, "Progress indicator");`

<a name="helptext" />

## <a name="automationpropertieshelptext"></a>AutomationProperties.HelpText

La propriété jointe `AutomationProperties.HelpText` doit être définie sur le texte qui décrit l’élément d’interface utilisateur, et peut être considérée comme un texte d’info-bulle associé à l’élément. Cela peut être accompli en XAML de la façon suivante :

```xaml
<Button Text="Toggle ActivityIndicator"
        AutomationProperties.IsInAccessibleTree="true"
        AutomationProperties.HelpText="Tap to toggle the activity indicator" />
```

Elle peut également être définie en C# comme suit :

```csharp
var button = new Button { Text = "Toggle ActivityIndicator" };
AutomationProperties.SetIsInAccessibleTree(button, true);
AutomationProperties.SetHelpText(button, "Tap to toggle the activity indicator");
```

> [!NOTE]
> Notez que la méthode [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) peut également être utilisée pour définir la propriété jointe `AutomationProperties.HelpText` – `button.SetValue(AutomationProperties.HelpTextProperty, "Tap to toggle the activity indicator");`

Sur certaines plateformes, pour modifier des contrôles tels qu’un objet [`Entry`](xref:Xamarin.Forms.Entry), la propriété `HelpText` peut parfois être omise et remplacée par un texte d’espace réservé. Par exemple, « Entrez ici votre nom » est un bon candidat pour la propriété [`Entry.Placeholder`](xref:Xamarin.Forms.Entry.Placeholder) qui place le texte dans le contrôle avant que l’utilisateur l’entre réellement.

<a name="labeledby" />

## <a name="automationpropertieslabeledby"></a>AutomationProperties.LabeledBy

La propriété jointe `AutomationProperties.LabeledBy` permet à un autre élément de définir des informations d’accessibilité pour l’élément actuel. Par exemple, un [`Label`](xref:Xamarin.Forms.Label) en regard d’un objet [`Entry`](xref:Xamarin.Forms.Entry) peut être utilisé pour décrire ce que l’objet `Entry` représente. Cela peut être accompli en XAML de la façon suivante :

```xaml
<Label x:Name="label" Text="Enter your name: " />
<Entry AutomationProperties.IsInAccessibleTree="true"
       AutomationProperties.LabeledBy="{x:Reference label}" />
```

Elle peut également être définie en C# comme suit :

```csharp
var nameLabel = new Label { Text = "Enter your name: " };
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
AutomationProperties.SetLabeledBy(entry, nameLabel);
```

> [!NOTE]
> Notez que la méthode [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) peut également être utilisée pour définir la propriété jointe `AutomationProperties.IsInAccessibleTree` – `entry.SetValue(AutomationProperties.LabeledByProperty, nameLabel);`

## <a name="accessibility-intricacies"></a>Subtilités de l’accessibilité

Les sections suivantes décrivent les subtilités de la définition des valeurs d’accessibilité sur certains contrôles.

### <a name="navigationpage"></a>NavigationPage

Sur Android, pour définir le texte que les lecteurs d’écran liront pour la flèche Retour dans la barre d’action d’une [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), définissez les propriétés `AutomationProperties.Name` et `AutomationProperties.HelpText` dans une [`Page`](xref:Xamarin.Forms.Page). Toutefois, notez que cette opération n’a aucun effet sur les boutons Retour du système d’exploitation.

### <a name="masterdetailpage"></a>MasterDetailPage

Sur iOS et la plateforme Windows universelle (UWP), pour définir le texte que les lecteurs d’écran liront pour le bouton bascule d’une [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage), définissez les propriétés `AutomationProperties.Name` et `AutomationProperties.HelpText` dans la `MasterDetailPage` ou la propriété `IconImageSource` dans la page `Master`.

Sur Android, pour définir le texte que les lecteurs d’écran liront pour le bouton bascule dans une [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage), ajoutez des ressources de chaîne au projet Android :

```xml
<resources>
    <string name="app_name">Xamarin Forms Control Gallery</string>
    <string name="btnMDPAutomationID_open">Open Side Menu message</string>
    <string name="btnMDPAutomationID_close">Close Side Menu message</string>
</resources>
```

Ensuite, définissez la propriété `AutomationId` de la propriété `IconImageSource` de la page `Master` sur la chaîne appropriée :

```csharp
var master = new ContentPage { ... };
master.IconImageSource.AutomationId = "btnMDPAutomationID";
```

### <a name="toolbaritem"></a>ToolbarItem

Sur iOS, Android et UWP, les lecteurs d’écran liront la valeur de propriété `Text` des instances [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem), à condition que les valeurs `AutomationProperties.Name` ou `AutomationProperties.HelpText` ne soient pas définies.

Sur iOS et UWP, la valeur de propriété `AutomationProperties.Name` remplacera la valeur de propriété `Text` lue par le lecteur d’écran.

Sur Android, les valeurs de propriété `AutomationProperties.Name` et/ou `AutomationProperties.HelpText` remplaceront complètement les valeurs de propriété `Text` à la fois visibles et lues par les lecteurs d’écran. Notez qu’il s’agit d’une limitation des API inférieures à 26.

## <a name="related-links"></a>Liens associés

- [Propriétés jointes](~/xamarin-forms/xaml/attached-properties.md)
- [Accessibilité (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility)
